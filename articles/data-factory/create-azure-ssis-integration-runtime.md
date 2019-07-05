---
title: 在 Azure Data Factory 中建立 Azure-SSIS Integration Runtime | Microsoft Docs
description: 了解如何在 Azure Data Factory 中建立 Azure-SSIS Integration Runtime，讓您可以在 Azure 中部署並執行 SSIS 封裝。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: fb5335c8dfd94006ba3f0d8d6b890869dd9f3717
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484820"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure Data Factory 中建立 Azure-SSIS Integration Runtime

本教學課程中提供佈建 Azure SQL Server Integration Services (SSIS) 整合執行階段 (IR) 在 Azure Data Factory (ADF) 的步驟。 Azure SSIS 整合執行階段支援在執行封裝部署到 Azure SQL Database 伺服器/受控執行個體 （專案部署模型） 所裝載的 SSIS 目錄 (SSISDB) 以及部署到檔案系統/檔案共用/Azure 檔案 （封裝部署模型）。 一旦佈建 Azure SSIS IR 時，您可以接著使用熟悉的工具，例如 SQL Server Data Tools (SSDT) / SQL Server Management Studio (SSMS) 和命令列公用程式，例如`dtinstall` / `dtutil` /`dtexec`至部署並在 Azure 中執行您的套件。

[教學課程：佈建 Azure SSIS IR](tutorial-create-azure-ssis-runtime-portal.md)示範如何建立 AZURE-SSIS 整合執行階段，透過 Azure 入口網站/ADF 應用程式，並選擇性地使用 Azure SQL Database 伺服器/受控執行個體來裝載 SSISDB。 本文會詳述此教學課程並示範如何執行下列作業：

- （選擇性） 使用虛擬網路服務端點/受控執行個體來裝載 SSISDB 的虛擬網路中的 Azure SQL Database 伺服器。 先決條件是，您需要設定您的 Azure SSIS IR 加入虛擬網路的虛擬網路權限/設定。

- 選擇性地使用 Azure Active Directory (AAD) 驗證您的 ADF 的受管理身分識別來連接到 Azure SQL Database 伺服器/受控執行個體。 先決條件是，您必須能夠建立 SSISDB 的資料庫使用者身分加入您的 ADF 的受管理的身分識別。

## <a name="overview"></a>概觀

本文示範各種佈建 Azure-SSIS IR 的方式：

- [Azure 门户](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager 範本](#azure-resource-manager-template)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 訂用帳戶**。 如果您還沒有訂用帳戶，則可建立一個[免費試用](https://azure.microsoft.com/pricing/free-trial/)帳戶。
- **Azure SQL Database 伺服器/受控執行個體 （選擇性）** 。 如果您還沒有資料庫伺服器，建立一個在 Azure 入口網站中開始之前。 此資料庫伺服器上，ADF 會反過來建立 SSISDB。 建議於整合執行階段所在的相同 Azure 區域中建立資料庫伺服器。 此設定可讓整合執行階段執行記錄寫入 SSISDB，而不需要跨 Azure 區域。 
    - 根據選取的資料庫伺服器，SSISDB 可代表您建立為單一資料庫、彈性集區的一部分，或建立在受控執行個體中，並且可在公用網路中或透過加入虛擬網路來存取。 如需選擇要裝載 SSISDB 的資料庫伺服器類型的指引，請參閱 <<c0> [ 比較的 Azure SQL Database 單一資料庫/彈性集區/受控執行個體](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 如果您使用與虛擬網路服務端點/受控執行個體的虛擬網路中的 Azure SQL Database 伺服器來裝載 SSISDB，或需要存取內部部署資料，您需要將 AZURE-SSIS IR 加入虛擬網路，請參閱[聯結 AZURE-SSIS IR虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。
    - 確認資料庫伺服器的 [允許存取 Azure 服務]  設定已啟用。 您可以使用 Azure SQL Database 伺服器與虛擬網路服務端點/受控執行個體來裝載 SSISDB 的虛擬網路中時，這並不適用。 如需詳細資訊，請參閱[保護 Azure SQL 資料庫資料庫](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)。 若要使用 PowerShell 來啟用此設定，請參閱 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。
    - 新增用戶端電腦的 IP 位址或 IP 位址範圍，其中包含用戶端電腦，用戶端 IP 位址清單中的資料庫伺服器的防火牆設定的 IP 位址。 如需詳細資訊，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](../sql-database/sql-database-firewall-configure.md)
    - 您可以連接至資料庫伺服器使用 SQL 驗證，使用您的伺服器系統管理員認證] 或 [Azure Active Directory (AAD) 驗證的受管理的身分識別您的 ADF。  對於後者，您需要針對您的 ADF 的受管理的身分識別加入至資料庫伺服器的存取權限的 AAD 群組，請參閱[AZURE-SSIS 整合執行階段啟用 AAD 驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。
    - 請確認您的資料庫伺服器不會已經無 SSISDB。 Azure SSIS IR 的佈建不支援使用現有的 SSISDB。
- **Azure Resource Manager 虛擬網路 (選擇性)** 。 如果下列至少一個條件成立，您就必須具備 Azure Resource Manager 虛擬網路：
    - 您要與虛擬網路服務端點/受控執行個體的虛擬網路中的 Azure SQL Database 伺服器上裝載 SSISDB。
    - 您想要從在 Azure-SSIS IR 上執行的 SSIS 封裝連線至內部部署資料存放區。
- **（選擇性） 的 azure PowerShell**。 遵循上的指示[如何安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps)，如果您想要執行 PowerShell 指令碼來佈建您的 AZURE-SSIS ir。

### <a name="region-support"></a>區域支援

如需目前可使用 ADF 和 Azure-SSIS IR 的 Azure 區域清單，請參閱[依區域的 ADF + SSIS IR 可用性](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)。

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>比較 SQL Database 單一資料庫/邏輯集區和 SQL Database 受控執行個體

下表會比較 Azure SQL Database 伺服器和受控執行個體的特定功能 (因為它們與 Azure-SSIR IR 有關)：

| 功能 | 單一資料庫/彈性集區| 受控執行個體 |
|---------|--------------|------------------|
| **排程** | 無法使用 SQL Server Agent。<br/><br/>請參閱[在 ADF 管線中排程封裝執行](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)。| 可取得受控執行個體代理程式。 |
| **驗證** | 您可以使用自主資料庫使用者來建立 SSISDB，該使用者代表具備您 ADF 之受控識別作為 **db_owner** 角色中成員的任何 AAD 群組。<br/><br/>請參閱[啟用 Azure AD 驗證以在 Azure SQL Database 伺服器中建立 SSISDB](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database)。 | 您可利用自主資料庫使用者的身分來建立 SSISDB，該使用者代表您 ADF 的受控識別。 <br/><br/>請參閱[啟用 Azure AD 驗證以在 Azure SQL Database 受控執行個體中建立 SSISDB](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance)。 |
| **服務層** | 當您使用 Azure SQL Database 伺服器建立 Azure-SSIS IR 時，可以選取 SSISDB 的服務層。 目前有多個服務層級。 | 當您使用受控執行個體建立 Azure-SSIS IR 時，無法選取 SSISDB 的服務層級。 您受控執行個體上的所有資料庫都會共用已配置給該執行個體的相同資源。 |
| **虛擬網路** | 如果您使用具有虛擬網路服務端點的 Azure SQL Database 伺服器，或者需要存取內部部署資料存放區，則僅支援讓您的 Azure-SSIS IR 加入 Azure Resource Manager 虛擬網路。 | 僅支援讓您的 Azure-SSIS IR 加入 Azure Resource Manager 虛擬網路。 一律需要虛擬網路。<br/><br/>如果您將 Azure-SSIS IR 加入至與受控執行個體相同的虛擬網路，請確定您的 Azure-SSIS IR 和受控執行個體分屬於不同的子網路。 如果您的 Azure-SSIS IR 並不是加入至受控執行個體所在的虛擬網路，則建議虛擬網路對等互連或虛擬網路對虛擬網路連線。 請參閱[將您的應用程式連線到 Azure SQL Database 受控執行個體](../sql-database/sql-database-managed-instance-connect-app.md)。 |
| **分散式交易** | 透過彈性交易支援。 不支援 Microsoft Distributed Transaction Coordinator (MSDTC) 交易。 如果您的 SSIS 封裝使用 MSDTC 來協調分散式交易，請考慮移轉至 Azure SQL Database 的彈性交易。 如需詳細資訊，請參閱[跨雲端資料庫的分散式交易](../sql-database/sql-database-elastic-transactions-overview.md)。 | 不支援。 |
| | | |

## <a name="azure-portal"></a>Azure 入口網站

在本節中，您會使用 Azure 入口網站 (特別是 ADF 使用者介面 (UI)/應用程式) 來建立 Azure-SSIS IR。

### <a name="create-a-data-factory"></a>建立 Data Factory

1. 啟動 **Microsoft Edge** 或 **Google Chrome** 網頁瀏覽器。 目前，只有 Microsoft Edge 和 Google Chrome 網頁瀏覽器支援 Data Factory UI。
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 按一下左邊功能表上的 [新增]  、[資料 + 分析]  ，再按一下 [Data Factory]  。

   ![新增->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. 在 [新增資料處理站]  頁面中，輸入 **MyAzureSsisDataFactory** 作為 [名稱]  。

   ![新增資料處理站頁面](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Azure Data Factory 的名稱必須是 **全域唯一的**。 如果您收到下列錯誤，請變更資料處理站的名稱 (例如 yournameMyAzureSsisDataFactory)，然後試著重新建立。 請參閱 [Data Factory - 命名規則](naming-rules.md)一文，以了解 Data Factory 成品的命名規則。

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。
1. 針對 [資源群組]  ，請執行下列其中一個步驟︰

   - 選取 [使用現有的]  ，然後從下拉式清單選取現有的資源群組。
   - 選取 [建立新的]  ，然後輸入資源群組的名稱。

   若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。

1. 針對 [版本]  選取 [V2]  。
1. 選取 Data Factory 的 [位置]  。 清單中只會顯示資料處理站建立所支援的位置。
1. 選取 [釘選到儀表板]  。
1. 按一下頁面底部的 [新增]  。
1. 在儀表板上，您會看到狀態如下的下列圖格︰**部署 Data Factory**。

    ![部署資料處理站圖格](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. 建立完成之後，您會看到如圖中所示的 [Data Factory]  頁面。

    ![Data Factory 首頁](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. 按一下 [編寫與監視]  ，以在另一個索引標籤中啟動 Data Factory 使用者介面 (UI)。

### <a name="provision-an-azure-ssis-integration-runtime"></a>佈建 Azure SSIS 整合執行階段

1. 在開始使用頁面中，按一下 [設定 SSIS 整合執行階段]  圖格。

   ![設定 SSIS 整合執行階段圖格](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. 在 [整合執行階段設定]  的 [一般設定]  頁面上，完成下列步驟：

   ![一般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. 針對 [名稱]  ，輸入取您整合執行階段的名稱。

    b. 針對 [描述]  ，輸入整合執行階段的描述。

    c. 針對 [位置]  ，選取整合執行階段的位置。 系統只會顯示支援的位置。 我們建議您選取裝載 SSISDB 的資料庫伺服器所在位置。

    d. 針對 [節點大小]  ，選取整合執行階段叢集中的節點大小。 系統只會顯示支援的節點大小。 如果您想要執行許多計算/記憶體密集套件，請選取大型節點大小 (相應增加)。

    e. 針對 [節點數目]  ，選取整合執行階段叢集中的節點數目。 系統只會顯示支援的節點數目。 如果您想要平行執行許多套件，請選取具有許多節點的大型叢集 (相應放大)。

    f. 針對 [版本/授權]  ，選取適合您整合執行階段的 SQL Server 版本/授權：[標準版] 或 [企業版]。 如果您想要在整合執行階段上使用進階/高階功能，請選取 [企業版]。

    g. 針對 [節省費用]  ，選取適合您整合執行階段的 Azure Hybrid Benefit (AHB) 選項：[是] 或 [否]。 如果您想要自備附有軟體保證的 SQL Server 授權，以混搭使用來享有節省費用的權益，請選擇 [是]。

    h.如果该值不存在，请单击“添加行”。 单击“下一步”  。

3. 在 [SQL 設定]  頁面上，完成下列步驟：

   ![SQL 設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. 在 **建立 SSIS 目錄...** 核取方塊，選取的封裝在您的 Azure SSIS 整合執行階段上執行的部署模型：專案部署模型封裝部署到您的資料庫伺服器所裝載的 SSISDB 的位置或封裝部署模型，將封裝部署到您的檔案系統/檔案的共用/Azure 檔案。 如果您檢查它，您必須將您自己的資料庫伺服器來裝載 SSISDB，我們會建立及管理，您的身分。
   
    b. 針對 [訂用帳戶]  ，選取具有資料庫伺服器可裝載 SSISDB 的 Azure 訂用帳戶。 

    c. 針對 [位置]  ，選取裝載 SSISDB 的資料庫伺服器所在位置。 我們建議您選取整合執行階段所在的相同位置。 

    d. 針對 [目錄資料庫伺服器端點]  ，選取裝載 SSISDB 的資料庫伺服器端點。 根據選取的資料庫伺服器，SSISDB 可代表您建立為單一資料庫、彈性集區的一部分，或建立在受控執行個體中，並且可在公用網路中或透過加入虛擬網路來存取。 如需選擇要裝載 SSISDB 的資料庫伺服器類型的指引，請參閱 <<c0> [ 比較的 Azure SQL Database 單一資料庫/彈性集區/受控執行個體](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 如果您選取 Azure SQL Database 伺服器與虛擬網路服務端點/受控執行個體來裝載 SSISDB，或需要存取內部部署資料的虛擬網路中時，您需要將 AZURE-SSIS IR 加入虛擬網路，請參閱[加入 AZURE-SSIS 整合執行階段虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 

    e. 在 [使用 AAD 驗證...]  核取方塊上，選取您資料庫伺服器用來裝載 SSISDB 的驗證方法：SQL 驗證或管理的身分識別為您的 ADF 的 AAD 驗證。 如果您檢查它，您需要針對您的 ADF 的受管理的身分識別新增至您的資料庫伺服器的存取權限的 AAD 群組，請參閱[AZURE-SSIS 整合執行階段啟用 AAD 驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。 

    f. 針對 [管理使用者名稱]  ，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證使用者名稱。 

    g. 針對 [管理密碼]  ，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證密碼。 

    h.如果该值不存在，请单击“添加行”。 針對 [目錄資料庫服務層級]  ，選取您資料庫伺服器用來裝載 SSISDB 的服務層級：基本/標準/進階層或彈性集區名稱。 

    i. 按一下 [測試連線]  ，如果成功的話，按 [下一步]  。 

4. 在 [進階設定]  頁面上，完成下列步驟：

    ![進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. 針對 [每節點的平行執行數上限]  ，選取執行階段叢集中每節點上可同時執行的套件數量上限。 系統只會顯示支援的套件數目。 如果您想要使用一個以上的核心，來執行計算/記憶體密集的單一大型/重量套件，請選取較低的數字。 如果您想要在單一核心中執行一個或多個小型/輕量套件，請選取較高的數字。

    b. 針對 [自訂安裝容器的 SAS URI]  ，選擇性地輸入 Azure 儲存體 Blob 容器的共用存取簽章 (SAS) 統一資源識別項 (URI)，該容器是您安裝指令碼且儲存指令碼相關檔案的地方，請參閱[自訂 Azure-SSIS IR 的安裝](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

5. 在 [選取虛擬網路...]  核取方塊上，選取是否要將整合執行階段加入虛擬網路。 如果您使用虛擬網路服務端點/受控執行個體來裝載 SSISDB 的虛擬網路中的 Azure SQL Database 伺服器，或需要存取內部部署資料，請檢查也就是您在您的 SSIS 套件中的內部部署資料來源/目的地，請參閱[加入至虛擬網路的 Azure SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 如果您核取該方塊，請完成下列步驟：

   ![虛擬網路進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. 針對 [訂用帳戶]  ，選取具有您的虛擬網路的 Azure 訂用帳戶。

    b. 針對 [位置]  ，選取整合執行階段的相同位置。

    c. 針對 [類型]  ，選取您虛擬網路的類型：[傳統] 或 [Azure Resource Manager]。 建議您選取 Azure Resource Manager 虛擬網路，因為傳統虛擬網路很快就會被取代。

    d. 針對 **[VNet 名稱]** ，選取您虛擬網路的名稱。 此虛擬網路應該是同一個虛擬網路服務端點/受控執行個體的虛擬網路中使用的 Azure SQL Database 伺服器來裝載 SSISDB 或連線到內部部署網路的一個。

    e. 針對 [子網路名稱]  ，選取虛擬網路的子網路名稱。 這應該是用於受控執行個體來裝載 SSISDB 的虛擬網路中的不同子網路。

6. 按一下 [VNet 驗證]  ，如果成功，按一下 [完成]  開始建立 Azure-SSIS 整合執行階段。

    > [!NOTE]
    > 但不包括任何自訂的安裝時間，此程序應在 5 分鐘內完成，但可能需要大約 20-30 分鐘的時間 AZURE-SSIS IR 加入虛擬網路。
    >
    > 如果您使用 SSISDB，ADF 服務將會連線到您的資料庫伺服器，以準備 SSISDB 中。 也會設定權限和設定虛擬網路，如果指定，並將您的 Azure SSIS IR 加入至虛擬網路。
    > 
    > 當您佈建 Azure SSIS IR 時，Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack 也會安裝。 這些元件提供 Excel/存取檔案和各種 Azure 資料來源，除了內建元件所支援的資料來源的連線。 您也可以安裝其他元件，請參閱[AZURE-SSIS 整合執行階段的自訂安裝程式](how-to-configure-azure-ssis-ir-custom-setup.md)。

7. 如有必要，切換到 [連線]  視窗中的 [整合執行階段]  。 按一下 [重新整理]  即可重新整理狀態。

   ![建立狀態](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. 使用 [動作]  資料行下方的連結來停止/啟動、編輯或刪除整合執行階段。 使用最後一個連結來檢視整合執行階段的 JSON 程式碼。 只有當 IR 停止時，才會啟用編輯和刪除按鈕。

   ![Azure SSIS IR - 動作](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>入口網站中的 Azure SSIS 整合執行階段

1. 在 Azure Data Factory 使用者介面中，切換至 [編輯]  索引標籤，按一下 [連線]  ，然後切換到 [整合執行階段]  索引標籤，以檢視資料處理站中的現有整合執行階段。

   ![檢視現有的 IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. 按一下 [新增]  建立新的 Azure-SSIS IR。

   ![整合執行階段功能表](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. 若要建立 Azure-SSIS 整合執行階段，按一下 [新增]  ，如圖中所示。

4. 在 [整合執行階段設定] 視窗中，選取 [隨即轉移現有的 SSIS 套件以在 Azure 中執行]  ，然後按一下 [下一步]  。

   ![指定整合執行階段的類型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. 請參閱[佈建 Azure SSIS 整合執行階段](#provision-an-azure-ssis-integration-runtime)一節，以取得設定 Azure SSIS IR 的剩餘步驟。

## <a name="azure-powershell"></a>Azure PowerShell

在本節中，會使用 Azure PowerShell 建立 Azure-SSIS IR。

### <a name="create-variables"></a>建立變數

複製和貼上下列指令碼-指定變數的值。 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance in a virtual network/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance in a virtual network

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>登入，並選取訂用帳戶

新增下列程式碼的指令碼，來登入並選取您的 Azure 訂用帳戶：

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>驗證連接到資料庫伺服器

新增下列程式碼以驗證您 Azure SQL Database 伺服器/受控執行個體。

```powershell
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-virtual-network"></a>設定虛擬網路

新增下列指令碼來自動設定虛擬網路權限/設定，讓您的 Azure-SSIS 整合執行階段可以加入。

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。

如果您的資源群組已經存在，不將此程式碼複製到您的指令碼中。 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>建立 Data Factory

執行下列命令來建立資料處理站。

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>建立整合執行階段

執行下列命令，以建立在 Azure 中執行 SSIS 套件的 Azure-SSIS 整合執行階段。

如果您不要使用 SSISDB，您可以省略 CatalogServerEndpoint、 CatalogPricingTier 和 CatalogAdminCredential 參數。

如果您不要使用虛擬網路服務端點/受控執行個體來裝載 SSISDB 的虛擬網路中的 Azure SQL Database 伺服器，或需要存取內部部署資料，您可以省略 VNetId 和 Subnet 參數，或將空值傳給它們。 否則，您不能省略它們，而必須從您的虛擬網路組態傳遞有效值，請參閱[將 Azure-SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

如果您使用受控執行個體來裝載 SSISDB，您可以省略 CatalogPricingTier 參數或為其傳遞空白值。 否則，您無法省略它，而必須從 Azure SQL Database 支援的定價層清單傳遞有效值，請參閱 [SQL Database 資源限制](../sql-database/sql-database-resource-limits.md)。

如果您使用 Azure Active Directory (AAD) 驗證為您的 ADF 管理的身分識別來連接到資料庫伺服器，您可以省略 CatalogAdminCredential 參數，但您也必須將受管理的身分識別為您的 ADF 中，新增至具有存取權的 AAD 群組權限至資料庫伺服器，請參閱[AZURE-SSIS 整合執行階段啟用 AAD 驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。 否則，您不能省略它，而且必須傳遞由伺服器系統管理員使用者名稱和密碼所構成的有效物件來進行 SQL 驗證。

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
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-integration-runtime"></a>啟動整合執行階段

執行下列命令來啟動 Azure SSIS 整合執行階段。

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> 但不包括任何自訂的安裝時間，此程序應在 5 分鐘內完成，但可能需要大約 20-30 分鐘的時間 AZURE-SSIS IR 加入虛擬網路。
>
> 如果您使用 SSISDB，ADF 服務將會連線到您的資料庫伺服器，以準備 SSISDB 中。 也會設定權限和設定虛擬網路，如果指定，並將您的 Azure SSIS IR 加入至虛擬網路。
> 
> 當您佈建 Azure SSIS IR 時，Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack 也會安裝。 這些元件提供 Excel/存取檔案和各種 Azure 資料來源，除了內建元件所支援的資料來源的連線。 您也可以安裝其他元件，請參閱[AZURE-SSIS 整合執行階段的自訂安裝程式](how-to-configure-azure-ssis-ir-custom-setup.md)。

### <a name="full-script"></a>完整指令碼

以下是建立 Azure-SSIS 整合執行階段的完整指令碼。

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance in a virtual network/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance in a virtual network

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

在本節中，會使用 Azure Resource Manager 範本來建立 Azure-SSIS 整合執行階段。 以下是範例逐步解說：

1. 使用下列 Azure Resource Manager 範本建立 JSON 檔案。 將角括弧 (預留位置) 中的值取代為您自己的值。

    ```json
    {
      "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. 若要部署 Azure Resource Manager 範本，請執行下列範例中，其中 ADFTutorialResourceGroup 是資源群組的名稱，而 ADFTutorialARM.json 是包含 JSON 定義的檔案中所示的新增 AzResourceGroupDeployment 命令您的 data factory 和 AZURE-SSIS ir。

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    這個命令會建立資料處理站，並且在其中建立 Azure-SSIS IR，但不會啟動 IR。

3. 若要啟動 Azure SSIS IR，請執行開始 AzDataFactoryV2IntegrationRuntime 命令：

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> 但不包括任何自訂的安裝時間，此程序應在 5 分鐘內完成，但可能需要大約 20-30 分鐘的時間 AZURE-SSIS IR 加入虛擬網路。
>
> 如果您使用 SSISDB，ADF 服務將會連線到您的資料庫伺服器，以準備 SSISDB 中。 也會設定權限和設定虛擬網路，如果指定，並將您的 Azure SSIS IR 加入至虛擬網路。
> 
> 當您佈建 Azure SSIS IR 時，Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack 也會安裝。 這些元件提供 Excel/存取檔案和各種 Azure 資料來源，除了內建元件所支援的資料來源的連線。 您也可以安裝其他元件，請參閱[AZURE-SSIS 整合執行階段的自訂安裝程式](how-to-configure-azure-ssis-ir-custom-setup.md)。

## <a name="deploy-ssis-packages"></a>部署 SSIS 套件

如果您使用 SSISDB 時，您可以將封裝部署到其中，並在 AZURE-SSIS 整合執行階段使用 SSDT/SSMS 工具連線到您的資料庫伺服器，透過其伺服器端點上加以執行。  對於 Azure SQL Database 伺服器/受控執行個體中具有公用端點的虛擬網路/受控執行個體，是伺服器端點格式`<server name>.database.windows.net` / `<server name>.<dns prefix>.database.windows.net` /`<server name>.public.<dns prefix>.database.windows.net,3342`分別。 如果您不要使用 SSISDB，您可以將封裝部署到檔案系統/檔案共用/Azure 檔案並執行它們使用的 Azure SSIS IR `dtinstall` / `dtutil` / `dtexec`命令列公用程式。 如需詳細資訊，請參閱 <<c0> [ 將 SSIS 套件部署](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)。 在這兩種情況下，您也可以執行您已部署的套件在 AZURE-SSIS IR 上使用 ADF 管線中的活動執行 SSIS 套件，請參閱[叫用 SSIS 封裝做為第一級的 ADF 活動的執行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

## <a name="next-steps"></a>後續步驟

另請參閱本文件中的其他 AZURE-SSIS IR 主題：

- [Azure-SSIS 整合執行階段](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供一般包括 AZURE-SSIS IR 的整合執行階段的相關資訊
- [監視 Azure SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 這篇文章會示範如何擷取與了解您的 Azure SSIS IR 的相關資訊
- [管理 AZURE-SSIS 整合執行階段](manage-azure-ssis-integration-runtime.md)。 這篇文章說明如何停止、 啟動或刪除您的 Azure SSIS IR-它也會顯示如何新增更多節點來相應放大您的 Azure SSIS IR。
- [將 Azure SSIS IR 加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。 本文提供將您的 Azure SSIS IR 加入虛擬網路的相關資訊。