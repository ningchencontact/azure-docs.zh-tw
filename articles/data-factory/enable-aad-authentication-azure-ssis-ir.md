---
title: 啟用適用於 Azure-SSIS Integration Runtime 的 Azure Active Directory 驗證 | Microsoft Docs
description: 本文說明如何使用 Azure Data Factory 的受控識別啟用 Azure Active Directory 驗證，來建立 Azure-SSIS Integration Runtime。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 2/19/2019
ms.author: douglasl
ms.openlocfilehash: b672264e1cb3cd415532cf4bcfbbd268afffa70d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415932"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>啟用適用於 Azure-SSIS Integration Runtime 的 Azure Active Directory 驗證

本文說明如何使用 Azure Data Factory (ADF) 的受控識別啟用 Azure Active Directory (Azure AD) 驗證，並使用它 (而非 SQL 驗證) 來建立 Azure-SSIS Integration Runtime (IR)，然後代表您在 Azure SQL Database 伺服器/受控執行個體中，建立 SSIS 目錄資料庫 (SSISDB)。

如需 ADF 受控識別的詳細資訊，請參閱 [Azure Data Factory 服務識別](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)。

> [!NOTE]
> 如果您已使用 SQL 驗證建立 Azure-SSIS IR，此時您無法將該整合執行階段重新設定為透過 PowerShell 使用 Azure AD 驗證，但是您可以在 Azure 入口網站/ADF 應用程式中執行此操作。 

## <a name="enable-azure-ad-on-azure-sql-database"></a>在 Azure SQL Database 上啟用 Azure AD

Azure SQL Database 伺服器支援由 Azure AD 使用者建立資料庫。 首先，您需要以成員的身分，使用您 ADF 的受控識別，建立 Azure AD 群組。 接著，您需要將 Azure AD 使用者設定為 Azure SQL Database 伺服器的 Active Directory 管理員，然後在 SQL Server Management Studio (SSMS) 上將該使用者連線到伺服器。 最後，您需要建立一個代表 Azure AD 群組的內含使用者，以便 Azure-SSIS IR 可以使用您 ADF 的受控識別，代表您建立 SSISDB。

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>以成員的身分使用您 ADF 的受控識別，建立 Azure AD 群組

您可以使用現有的 Azure AD 群組，或使用 Azure AD PowerShell 建立一個新群組。

1.  安裝 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 模組。

2.  使用  `Connect-AzureAD` 登入，執行下列 Cmdlet 以建立群組，並將其儲存在變數中：

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    結果如下列範例所示，同時也顯示變數值：

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  將 ADF 的受控識別新增至群組。 您可以依照文章 [Azure Data Factory 服務識別](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)，以取得主體服務識別的識別碼 (例如 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc，但是不要將服務識別應用程式識別碼用於此用途)。

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    您也可以之後再檢查群組成員資格。

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>設定 Azure SQL Database 伺服器的 Azure AD 驗證

您可以使用下列步驟， [透過 SQL 設定及管理 Azure AD 驗證](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)：

1.  在 Azure 入口網站中，選取左側導覽中的 [所有服務] **** -> [SQL 伺服器] 。

2.  選取要使用 Azure AD 驗證設定的 Azure SQL Database 伺服器。

3.  在刀鋒視窗的 [設定] ****  區段中，選取 [Active Directory 管理員] ****。

4.  在命令列中選取 [設定管理員] ****。

5.  選取要設為伺服器管理員的 Azure AD 使用者帳戶，然後選取 [選取] ****。

6.  在命令列上選取 [儲存] ****。

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>在 Azure SQL Database 伺服器中，建立一個代表 Azure AD 群組的內含使用者

在此後續步驟中，您需要  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS)。

1.  啟動 SSMS。

2.  在 [連線到伺服器] ****  對話方塊中，在 [伺服器名稱] ****  欄位中輸入您的 Azure SQL Database 伺服器名稱。

3.  在 [驗證] ****  欄位中，選取 [具 MFA 支援的 Active Directory - 通用] **** (您也可以使用另外兩種 Active Directory 驗證類型，請參閱[使用 SQL 設定及管理 Azure AD 驗證](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure))。

4.  在 [使用者名稱] ****  欄位中，輸入您設為伺服器管理員的 Azure AD 帳戶名稱，例如 testuser@xxxonline.com。

5.  選取 [連線] ****，並完成登入程序。

6.  在 [物件總管] **** 中，展開 [資料庫] **** -> [系統資料庫] 資料夾。

7.  在 [master] 資料庫上按一下滑鼠右鍵，然後選取 [新增查詢] ****。

8.  在查詢視窗中，輸入下列 T-SQL 命令，然後在工具列中選取 [執行] **** 。

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    命令應該會順利完成，建立代表群組的內含使用者。

9.  清除查詢視窗，輸入下列 T-SQL 命令，然後在工具列中選取 [執行]。

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    命令應該會順利完成，將建立資料庫 (SSISDB) 的能力授與內含的使用者。

10.  如果您的 SSISDB 是使用 SQL 驗證來建立的，而您想要切換成使用 Azure AD 驗證以便讓 Azure-SSIS IR 存取它，請在 [SSISDB] 資料庫上按一下滑鼠右鍵，然後選取 [新增查詢] ****。

11.  在查詢視窗中，輸入下列 T-SQL 命令，然後在工具列中選取 [執行] **** 。

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    命令應該會順利完成，建立代表群組的內含使用者。

12.  清除查詢視窗，輸入下列 T-SQL 命令，然後在工具列中選取 [執行]。

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    命令應該會順利完成，將存取 SSISDB 的能力授與內含的使用者。

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>為 Azure SQL Database 受控執行個體啟用 Azure AD

「Azure SQL Database 受控執行個體」支援直接使用 ADF 的受控識別來建立資料庫。 您不需要將 ADF的受控識別加入 Azure AD 群組，也不需要在受控執行個體中建立代表該群組的內含使用者。

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>為 Azure SQL Database 受控執行個體設定 Azure AD 驗證

1.   在 Azure 入口網站中，從左側導覽中選取 [所有服務] -> [SQL 伺服器]。

2.   選取要使用 Azure AD 驗證設定的受控執行個體。

3.   在刀鋒視窗的 [設定] 區段中，選取 [Active Directory 管理員]。

4.   在命令列中選取 [設定管理員]。

5.   選取要設定為伺服器管理員的 Azure AD 使用者帳戶，然後選取 [選取]。

6.   在命令列中，選取 [儲存]。

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>在 Azure SQL Database 受控執行個體中，以使用者身分為 ADF 新增受控識別

在此後續步驟中，您需要  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS)。

1.  啟動 SSMS。

2.  使用您的 SQL/Active Directory 管理員帳戶，連線到受控執行個體。

3.  在 [物件總管] 中，展開 [資料庫][系統資料庫] ->  資料夾。

4.  在 [master] 資料庫上按一下滑鼠右鍵，然後選取 [新增查詢]。

5.  取得 ADF 的受控識別。 您可以依照文章 [Azure Data Factory 服務識別](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)，以取得主體服務識別應用程式的識別碼 (但是不要將服務識別識別碼用於此用途)。

6.  在查詢視窗中，執行下列 T-SQL 指令碼，將 ADF 的受控識別轉換為二進位類型：

    ```sql
    DECLARE @applicationId uniqueidentifier = '{your SERVICE IDENTITY APPLICATION ID}'
    select CAST(@applicationId AS varbinary)
    ```
    
    此命令應該順利完成，以二進位格式顯示 ADF 的受控識別。

7.  清除查詢視窗，並執行下列 T-SQL 指令碼，以使用者的身分，新增 ADF 的受控識別

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID = {your SERVICE IDENTITY APPLICATION ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    命令應該會順利完成，將建立資料庫 (SSISDB) 的能力授與 ADF 的受控識別。

8.  如果您的 SSISDB 是使用 SQL 驗證來建立的，而您想要切換成使用 Azure AD 驗證以便讓 Azure-SSIS IR 存取它，請在 [SSISDB] 資料庫上按一下滑鼠右鍵，然後選取 [新增查詢] ****。

9.  在查詢視窗中，輸入下列 T-SQL 命令，然後在工具列中選取 [執行] **** 。

    ```sql
    CREATE USER [{the managed identity name}] FOR LOGIN [{the managed identity name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{the managed identity name}]
    ```

    命令應該會順利完成，將存取 SSISDB 的能力授與 ADF 的受控識別。

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>在 Azure 入口網站/ADF 應用程式中，佈建 Azure-SSIS IR

當您在 Azure 入口網站/ADF 應用程式中佈建 Azure-SSIS IR 時，請在 [SQL 設定] 頁面上，選取 [對您的 ADF 使用 AAD 驗證搭配受控識別] 選項。 下列螢幕擷取畫面顯示裝載 SSISDB 之 Azure SQL Database 伺服器的整合執行階段設定。 對於具有裝載 SSISDB 之受控執行個體的整合執行階段，**目錄資料庫服務層**和**允許 Azure 服務存取** 設定不適用，而其他設定皆相同。

如需關於如何建立 Azure-SSIS IR 的詳細資訊，請參閱[在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

![Azure-SSIS 整合執行階段的設定](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>使用 PowerShell 佈建 Azure-SSIS IR

若要使用 PowerShell 佈建 Azure-SSIS IR，請執行下列作業：

1.  安裝 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)  模組。

2.  在您的指令碼中，請勿設定 `CatalogAdminCredential` 參數。 例如︰

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
