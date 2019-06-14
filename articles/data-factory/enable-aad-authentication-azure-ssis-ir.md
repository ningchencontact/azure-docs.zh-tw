---
title: 啟用適用於 Azure-SSIS Integration Runtime 的 Azure Active Directory 驗證 | Microsoft Docs
description: 本文說明如何使用 Azure Data Factory 的受控識別啟用 Azure Active Directory 驗證，來建立 Azure-SSIS Integration Runtime。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 5/14/2019
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: a67436f09d6e28db8d19679e446ac4cf98383709
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65593792"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>啟用適用於 Azure-SSIS Integration Runtime 的 Azure Active Directory 驗證

這篇文章說明如何啟用 Azure Active Directory (Azure AD) 驗證的受管理的身分識別與您 Azure Data Factory (ADF)，並使用它來建立 Azure SSIS Integration Runtime (IR) 又會佈建，而不是 SQL 驗證SSIS 目錄資料庫 (SSISDB) 中 Azure SQL Database 伺服器/受控執行個體代表您。

如需受管理的身分識別，為您的 ADF 的詳細資訊，請參閱[Data factory 受控 identiy](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)。

> [!NOTE]
>-  在此案例中，受管理的身分識別與 Azure AD 驗證您的 ADF 只能用來建立和開啟佈建，會將 SSIS IR 的後續開始作業，並連線至 SSISDB。 SSIS 封裝執行，SSIS IR 仍然會連線至 SSISDB 佈建期間建立的完全受控帳戶以使用 SQL 驗證的 SSISDB。
>-  如果您已經建立您的 SSIS 整合執行階段使用 SQL 驗證，您可以不重新設定為使用 Azure AD 驗證，透過 PowerShell 在此階段中，但則可以透過 Azure 入口網站/ADF 應用程式。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

3.  將 ADF 的受控識別新增至群組。 您可以遵循本文[Data factory 受控 identiy](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)取得的主體管理身分識別物件識別碼 (例如 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc，但是不會針對此用途使用受控身分識別應用程式識別碼)。

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    您也可以之後再檢查群組成員資格。

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>設定 Azure SQL Database 伺服器的 Azure AD 驗證

您可以使用下列步驟， [透過 SQL 設定及管理 Azure AD 驗證](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)：

1.  在 Azure 入口網站中，從左側導覽中選取 [所有服務]   -> [SQL 伺服器]  。

2.  選取要使用 Azure AD 驗證設定的 Azure SQL Database 伺服器。

3.  在刀鋒視窗的 [設定]  區段中，選取 [Active Directory 管理員]  。

4.  在命令列中選取 [設定管理員]  。

5.  選取 Azure AD 使用者帳戶以成為系統管理員的伺服器，然後選取**選取。**

6.  在命令列中選取 [儲存]  。

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>在 Azure SQL Database 伺服器中，建立一個代表 Azure AD 群組的內含使用者

在此後續步驟中，您需要  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS)。

1. 啟動 SSMS。

2. 在 [**連接到伺服器**] 對話方塊中，輸入您的 Azure SQL Database 伺服器名稱，在**伺服器名稱**欄位。

3. 在 **驗證**欄位中，選取**Active Directory-與 MFA 支援通用**(您也可以使用其他兩個 Active Directory 驗證類型，請參閱[設定和管理Azure AD 驗證搭配 SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure))。

4. 在 **使用者名**欄位中，輸入您設定伺服器系統管理員，例如 Azure AD 帳戶的名稱testuser@xxxonline.com。

5. 選取  **Connect**並完成登入程序。

6. 在 [物件總管]  中，展開 [資料庫]  [系統資料庫] ->   資料夾。

7. 在 [master]  資料庫上按一下滑鼠右鍵，然後選取 [新增查詢]  。

8. 在 [查詢] 視窗中，輸入下列 T-SQL 命令，並選取**Execute**工具列上。

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   命令應該會順利完成，建立代表群組的內含使用者。

9. 清除查詢視窗，輸入下列 T-SQL 命令，然後在工具列中選取 [執行]  。

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   命令應該會順利完成，將建立資料庫 (SSISDB) 的能力授與內含的使用者。

10. 如果使用 SQL 驗證 」 來建立 「 您 SSISDB，而您想要切換至使用您的 Azure SSIS IR 的 Azure AD 驗證來存取它，以滑鼠右鍵按一下**SSISDB**資料庫，然後選取**新的查詢**。

11. 在 [查詢] 視窗中，輸入下列 T-SQL 命令，並選取**Execute**工具列上。

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    命令應該會順利完成，建立代表群組的內含使用者。

12. 清除查詢視窗，輸入下列 T-SQL 命令，然後在工具列中選取 [執行]  。

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    命令應該會順利完成，將存取 SSISDB 的能力授與內含的使用者。

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>為 Azure SQL Database 受控執行個體啟用 Azure AD

「Azure SQL Database 受控執行個體」支援直接使用 ADF 的受控識別來建立資料庫。 您不需要將 ADF的受控識別加入 Azure AD 群組，也不需要在受控執行個體中建立代表該群組的內含使用者。

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>為 Azure SQL Database 受控執行個體設定 Azure AD 驗證

1.   在 Azure 入口網站中，從左側導覽中選取 [所有服務]   -> [SQL 伺服器]  。

2.   選取要使用 Azure AD 驗證設定的受控執行個體。

3.   在刀鋒視窗的 [設定]  區段中，選取 [Active Directory 管理員]  。

4.   在命令列中選取 [設定管理員]  。

5.   選取要設定為伺服器管理員的 Azure AD 使用者帳戶，然後選取 [選取]  。

6.   在命令列中，選取 [儲存]  。

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>在 Azure SQL Database 受控執行個體中，以使用者身分為 ADF 新增受控識別

在此後續步驟中，您需要  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS)。

1.  啟動 SSMS。

2.  使用您的 SQL/Active Directory 管理員帳戶，連線到受控執行個體。

3.  在 [物件總管]  中，展開 [資料庫]  [系統資料庫] ->   資料夾。

4.  在 [master]  資料庫上按一下滑鼠右鍵，然後選取 [新增查詢]  。

5.  取得 ADF 的受控識別。 您可以遵循本文[Data factory 受控 identiy](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)來取得主體管理身分識別應用程式識別碼 （但不是會針對此目的使用受控身分識別的物件識別碼）。

6.  在查詢視窗中，執行下列 T-SQL 指令碼，將 ADF 的受控識別轉換為二進位類型：

    ```sql
    DECLARE @applicationId uniqueidentifier = '{your Managed Identity Application ID}'
    select CAST(@applicationId AS varbinary)
    ```
    
    此命令應該順利完成，以二進位格式顯示 ADF 的受控識別。

7.  清除查詢視窗，並執行下列 T-SQL 指令碼，以使用者的身分，新增 ADF 的受控識別

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID = {your Managed Identity Application ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    命令應該會順利完成，將建立資料庫 (SSISDB) 的能力授與 ADF 的受控識別。

8.  如果使用 SQL 驗證 」 來建立 「 您 SSISDB，而您想要切換至使用您的 Azure SSIS IR 的 Azure AD 驗證來存取它，以滑鼠右鍵按一下**SSISDB**資料庫，然後選取**新的查詢**。

9.  在 [查詢] 視窗中，輸入下列 T-SQL 命令，並選取**Execute**工具列上。

    ```sql
    CREATE USER [{the managed identity name}] FOR LOGIN [{the managed identity name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{the managed identity name}]
    ```

    命令應該會順利完成，將存取 SSISDB 的能力授與 ADF 的受控識別。

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>在 Azure 入口網站/ADF 應用程式中，佈建 Azure-SSIS IR

當您在 Azure 入口網站/ADF 應用程式中佈建 Azure-SSIS IR 時，請在 [SQL 設定]  頁面上，選取 [對您的 ADF 使用 AAD 驗證搭配受控識別]  選項。 下列螢幕擷取畫面顯示裝載 SSISDB 之 Azure SQL Database 伺服器的整合執行階段設定。 對於具有裝載 SSISDB 之受控執行個體的整合執行階段，**目錄資料庫服務層**和**允許 Azure 服務存取** 設定不適用，而其他設定皆相同。

如需關於如何建立 Azure-SSIS IR 的詳細資訊，請參閱[在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

![Azure-SSIS 整合執行階段的設定](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>使用 PowerShell 佈建 Azure-SSIS IR

若要使用 PowerShell 佈建 Azure-SSIS IR，請執行下列作業：

1.  安裝 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)  模組。

2.  在您的指令碼中，請勿設定 `CatalogAdminCredential` 參數。 例如:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
