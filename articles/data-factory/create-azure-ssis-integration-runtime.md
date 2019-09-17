---
title: 在 Azure Data Factory 中建立 Azure-SSIS Integration Runtime | Microsoft Docs
description: 了解如何在 Azure Data Factory 中建立 Azure-SSIS Integration Runtime，讓您可以在 Azure 中部署並執行 SSIS 封裝。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 36cb4d306cd74dcde09fa55fc582a043bc324f65
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010006"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure Data Factory 中建立 Azure-SSIS Integration Runtime

本教學課程提供在 Azure Data Factory (ADF) 中佈建 Azure-SQL Server Integration Services (SSIS) 整合執行階段 (IR) 的步驟。 套件若部署到 Azure SQL Database 伺服器/受控執行個體 (專案部署模型) 所裝載的 SSIS 目錄 (SSISDB) 以及部署到檔案系統/檔案共用/Azure 檔案 (套件部署模型) 中，Azure-SSIS IR 都可支援執行這些套件。 佈建好 Azure SSIS IR 後，您可以接著使用 SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) 等熟悉的工具和命令列公用程式 (例如 `dtinstall`/`dtutil`/`dtexec`)，在 Azure 中部署並執行您的套件。

[教學課程：布建 azure ssis ir](tutorial-create-azure-ssis-runtime-portal.md)會示範如何透過 Azure 入口網站/ADF 應用程式建立 azure ssis ir，並選擇性地使用 Azure SQL Database server/受控執行個體來裝載 SSISDB。 本文會詳述此教學課程並示範如何執行下列作業：

- 選擇性地使用具有虛擬網路服務端點/受控執行個體的 Azure SQL Database server，搭配私人端點來裝載 SSISDB。 必要條件是您必須為您的 Azure SSIS IR 設定虛擬網路許可權/設定，以加入虛擬網路。

- 選擇性地使用 Azure Active Directory （AAD）驗證搭配您 ADF 的受控識別，以連接到 Azure SQL Database server/受控執行個體。 必要條件是，您必須將 ADF 的受控識別新增為能夠建立 SSISDB 的資料庫使用者。

- 選擇性地將您的 Azure SSIS IR 加入虛擬網路/設定自我裝載 IR 作為 Azure SSIS IR 的 proxy，以存取內部部署資料。

## <a name="overview"></a>總覽

本文示範各種佈建 Azure-SSIS IR 的方式：

- [Azure 入口網站](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager 範本](#azure-resource-manager-template)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 訂用帳戶**。 如果您還沒有訂用帳戶，則可建立一個[免費試用](https://azure.microsoft.com/pricing/free-trial/)帳戶。
- **Azure SQL Database server/受控執行個體（選擇性）** 。 如果您還沒有資料庫伺服器，請在 Azure 入口網站中建立一個，然後再開始。 ADF 接著會在此資料庫伺服器上建立 SSISDB。 建議於整合執行階段所在的相同 Azure 區域中建立資料庫伺服器。 此設定可讓整合執行階段將執行記錄寫入 SSISDB，而不需要跨 Azure 區域。 
    - 根據選取的資料庫伺服器，SSISDB 可代表您建立為單一資料庫、彈性集區的一部分，或建立在受控執行個體中，並且可在公用網路中或透過加入虛擬網路來存取。 如需有關選擇適當資料庫伺服器類型來裝載 SSISDB 的指引，請參閱[比較 Azure SQL Database 單一資料庫/彈性集區/受控執行個體](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 如果您使用具有虛擬網路服務端點/受控執行個體的 Azure SQL Database 伺服器搭配私人端點來裝載 SSISDB，或要求存取內部部署資料而不設定自我裝載的 IR，您必須將您的 Azure SSIS IR 加入虛擬網路。請參閱將[AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。
    - 確認資料庫伺服器的 [允許存取 Azure 服務] 設定已啟用。 當您使用具有虛擬網路服務端點/受控執行個體的 Azure SQL Database 伺服器搭配私人端點來裝載 SSISDB 時，這不適用。 如需詳細資訊，請參閱[保護 Azure SQL 資料庫資料庫](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)。 若要使用 PowerShell 來啟用此設定，請參閱 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。
    - 新增用戶端電腦的 IP 位址，或新增 IP 位址範圍，其中包含資料庫伺服器之防火牆設定中用戶端電腦 IP 位址到用戶端 IP 位址清單。 如需詳細資訊，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](../sql-database/sql-database-firewall-configure.md)
    - 若要連線至資料庫伺服器，您可以使用伺服器管理員認證來執行 SQL 驗證，或使用 ADF 受控識別來執行 Azure Active Directory (AAD) 驗證。  對於後者，您需要將 ADF 的受控識別新增至具有資料庫伺服器存取權限的 AAD 群組，請參閱[啟用 AZURE SSIS IR 的 AAD 驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。
    - 請確認您的資料庫伺服器尚未有 SSISDB。 Azure-SSIS IR 的佈建不支援使用現有的 SSISDB。
- **Azure Resource Manager 虛擬網路 (選擇性)** 。 如果下列至少一個條件成立，您就必須具備 Azure Resource Manager 虛擬網路：
    - 您在具有虛擬網路服務端點/受控執行個體和私人端點的 Azure SQL Database 伺服器上裝載 SSISDB。
    - 您想要從 Azure SSIS IR 上執行的 SSIS 套件連接到內部部署資料存放區，而不需設定自我裝載 IR。
- **Azure PowerShell （選擇性）** 。 如果您想要執行 PowerShell 腳本來布建您的 Azure SSIS IR，請遵循[如何安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps)的指示。

### <a name="region-support"></a>區域支援

如需目前可使用 ADF 和 Azure-SSIS IR 的 Azure 區域清單，請參閱[依區域的 ADF + SSIS IR 可用性](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)。

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>比較 SQL Database 單一資料庫/邏輯集區和 SQL Database 受控執行個體

下表會比較 Azure SQL Database 伺服器和受控執行個體的特定功能 (因為它們與 Azure-SSIR IR 有關)：

| 功能 | 單一資料庫/彈性集區| 受控執行個體 |
|---------|--------------|------------------|
| **排程** | 無法使用 SQL Server Agent。<br/><br/>請參閱[在 ADF 管線中排程封裝執行](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)。| 可取得受控執行個體代理程式。 |
| **驗證** | 您可以使用自主資料庫使用者來建立 SSISDB，該使用者代表具備您 ADF 之受控識別作為 **db_owner** 角色中成員的任何 AAD 群組。<br/><br/>請參閱[啟用 Azure AD 驗證以在 Azure SQL Database 伺服器中建立 SSISDB](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database)。 | 您可利用自主資料庫使用者的身分來建立 SSISDB，該使用者代表您 ADF 的受控識別。 <br/><br/>請參閱[啟用 Azure AD 驗證以在 Azure SQL Database 受控執行個體中建立 SSISDB](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance)。 |
| **服務層** | 當您使用 Azure SQL Database 伺服器建立 Azure-SSIS IR 時，可以選取 SSISDB 的服務層。 目前有多個服務層級。 | 當您使用受控執行個體建立 Azure-SSIS IR 時，無法選取 SSISDB 的服務層級。 您受控執行個體上的所有資料庫都會共用已配置給該執行個體的相同資源。 |
| **虛擬網路** | 如果您使用具有虛擬網路服務端點的 Azure SQL Database 伺服器，或需要存取內部部署資料存放區，而不需設定自我裝載 IR，則僅支援將 Azure SSIS IR 的 Azure Resource Manager 虛擬網路加入。 | 僅支援讓您的 Azure-SSIS IR 加入 Azure Resource Manager 虛擬網路。 當您未啟用受控執行個體的公用端點時，需要虛擬網路。<br/><br/>如果您將 Azure-SSIS IR 加入至與受控執行個體相同的虛擬網路，請確定您的 Azure-SSIS IR 和受控執行個體分屬於不同的子網路。 如果您的 Azure-SSIS IR 並不是加入至受控執行個體所在的虛擬網路，則建議虛擬網路對等互連或虛擬網路對虛擬網路連線。 請參閱[將您的應用程式連線到 Azure SQL Database 受控執行個體](../sql-database/sql-database-managed-instance-connect-app.md)。 |
| **分散式交易** | 透過彈性交易支援。 不支援 Microsoft Distributed Transaction Coordinator (MSDTC) 交易。 如果您的 SSIS 封裝使用 MSDTC 來協調分散式交易，請考慮移轉至 Azure SQL Database 的彈性交易。 如需詳細資訊，請參閱[跨雲端資料庫的分散式交易](../sql-database/sql-database-elastic-transactions-overview.md)。 | 不支援。 |
| | | |

## <a name="azure-portal"></a>Azure 入口網站

在本節中，您會使用 Azure 入口網站 (特別是 ADF 使用者介面 (UI)/應用程式) 來建立 Azure-SSIS IR。

### <a name="create-a-data-factory"></a>建立 Data Factory

若要透過 Azure 入口網站建立您的 ADF，請遵循透過[UI 建立 adf](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)一文中的逐步指示，然後選取 [**釘選到儀表板**]，同時在建立之後允許快速存取。 

建立 ADF 之後，請在 Azure 入口網站開啟其 [總覽] 頁面，然後按一下 [**作者 & 監視器**] 圖格，以在您可以繼續建立 AZURE SSIS IR 的另一個索引標籤上啟動其「**讓我們**開始使用」頁面。   

### <a name="provision-an-azure-ssis-integration-runtime"></a>佈建 Azure SSIS 整合執行階段

1. 在 [**現在就開始**吧] 頁面上，按一下 [**設定 SSIS Integration Runtime** ] 圖格。

   ![設定 SSIS 整合執行階段圖格](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. 在 [整合執行階段設定] 的 [一般設定] 頁面上，完成下列步驟：

   ![一般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. 針對 [名稱]，輸入取您整合執行階段的名稱。

    b. 針對 [描述]，輸入整合執行階段的描述。

    c. 針對 [位置]，選取整合執行階段的位置。 系統只會顯示支援的位置。 我們建議您選取裝載 SSISDB 的資料庫伺服器所在位置。

    d. 針對 [節點大小]，選取整合執行階段叢集中的節點大小。 系統只會顯示支援的節點大小。 如果您想要執行許多計算/記憶體密集套件，請選取大型節點大小 (相應增加)。

    e. 針對 [節點數目]，選取整合執行階段叢集中的節點數目。 系統只會顯示支援的節點數目。 如果您想要平行執行許多套件，請選取具有許多節點的大型叢集 (相應放大)。

    f. 針對 [版本/授權]，選取適合您整合執行階段的 SQL Server 版本/授權：[標準版] 或 [企業版]。 如果您想要在整合執行階段上使用進階/高階功能，請選取 [企業版]。

    g. 針對 [節省費用]，選取適合您整合執行階段的 Azure Hybrid Benefit (AHB) 選項：[是] 或 [否]。 如果您想要自備附有軟體保證的 SQL Server 授權，以混搭使用來享有節省費用的權益，請選擇 [是]。

    h. 按一下 [下一步]。

3. 在 [SQL 設定] 頁面上，完成下列步驟：

   ![SQL 設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. 針對要在 Azure-SSIS IR 上執行的套件，在 [建立 SSIS 目錄...] 核取方塊中上選取部署模型：套件部署到您資料庫伺服器所裝載 SSISDB 的專案部署模型，或套件部署到檔案系統/檔案共用/Azure 檔案的套件部署模型。 如果您核取該選項，您必須用自己的資料庫伺服器來裝載 SSISDB，而我們會為您建立及管理 SSISDB。
   
    b. 針對 [訂用帳戶]，選取具有資料庫伺服器可裝載 SSISDB 的 Azure 訂用帳戶。 

    c. 針對 [位置]，選取裝載 SSISDB 的資料庫伺服器所在位置。 我們建議您選取整合執行階段所在的相同位置。 

    d. 針對 [目錄資料庫伺服器端點]，選取裝載 SSISDB 的資料庫伺服器端點。 根據選取的資料庫伺服器，SSISDB 可代表您建立為單一資料庫、彈性集區的一部分，或建立在受控執行個體中，並且可在公用網路中或透過加入虛擬網路來存取。 如需有關選擇適當資料庫伺服器類型來裝載 SSISDB 的指引，請參閱[比較 Azure SQL Database 單一資料庫/彈性集區/受控執行個體](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 如果您選取 [具有虛擬網路服務端點的 Azure SQL Database 伺服器]/受控執行個體具有私人端點來裝載 SSISDB，或要求存取內部部署資料而不設定自我裝載的 IR，則需要將您的 Azure SSIS IR 加入虛擬網路，請參閱將[AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 

    e. 在 [使用 AAD 驗證...] 核取方塊上，選取您資料庫伺服器用來裝載 SSISDB 的驗證方法：使用您 ADF 受控識別的 SQL 驗證 AAD 驗證。 如果您核取此選項，您需要將 ADF 的受控識別新增至具有資料庫伺服器存取權限的 AAD 群組，請參閱[啟用 AZURE SSIS IR 的 AAD 驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。 

    f. 針對 [管理使用者名稱]，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證使用者名稱。 

    g. 針對 [管理密碼]，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證密碼。 

    h. 針對 [目錄資料庫服務層級]，選取您資料庫伺服器用來裝載 SSISDB 的服務層級：基本/標準/進階層或彈性集區名稱。 

    i. 按一下 [測試連線]，如果成功的話，按 [下一步]。 

4. 在 [進階設定] 頁面上，完成下列步驟：

    ![進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. 針對 [每節點的平行執行數上限]，選取執行階段叢集中每節點上可同時執行的套件數量上限。 系統只會顯示支援的套件數目。 如果您想要使用一個以上的核心，來執行計算/記憶體密集的單一大型/重量套件，請選取較低的數字。 如果您想要在單一核心中執行一個或多個小型/輕量套件，請選取較高的數字。

    b. 針對 [自訂安裝容器的 SAS URI]，選擇性地輸入 Azure 儲存體 Blob 容器的共用存取簽章 (SAS) 統一資源識別項 (URI)，該容器是您安裝指令碼且儲存指令碼相關檔案的地方，請參閱[自訂 Azure-SSIS IR 的安裝](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

5. 在 [選取虛擬網路...] 核取方塊上，選取是否要將整合執行階段加入虛擬網路。 如果您使用具有虛擬網路服務端點/受控執行個體的 Azure SQL Database server 搭配私人端點來裝載 SSISDB，或要求存取內部部署資料（也就是您的 SSIS 套件中有內部部署資料來源/目的地），請檢查它，而不需要設定自我裝載 IR 的連結，請參閱將[AZURE SSIS Ir 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 如果您核取該方塊，請完成下列步驟：

   ![虛擬網路進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. 針對 [訂用帳戶]，選取具有您的虛擬網路的 Azure 訂用帳戶。

    b. 針對 [位置]，選取整合執行階段的相同位置。

    c. 針對 [類型]，選取您虛擬網路的類型：[傳統] 或 [Azure Resource Manager]。 建議您選取 Azure Resource Manager 虛擬網路，因為傳統虛擬網路很快就會被取代。

    d. 針對 **[VNet 名稱]** ，選取您虛擬網路的名稱。 此虛擬網路應該與用於虛擬網路中具有虛擬網路服務端點/受控執行個體的 Azure SQL Database 伺服器相同，以裝載 SSISDB 或連線到內部部署網路的應用程式。

    e. 針對 [子網路名稱]，選取虛擬網路的子網路名稱。 這應該與用於虛擬網路中受控執行個體以裝載 SSISDB 的子網不同。

6. 在 [**設定自我裝載 ...** ] 核取方塊上，選取您是否要將自我裝載 ir 設定為 AZURE SSIS IR 的 proxy，請參閱[將自我裝載 ir 設為 proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 如果您核取該方塊，請完成下列步驟：

   ![使用自我裝載 IR 的 Advanced 設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

    a. 對於**自我**裝載的 Integration Runtime，請選取您現有的自我裝載 IR 作為 AZURE SSIS IR 的 proxy。

    b. 針對**暫存儲存體連結服務**，請選取現有的 Azure Blob 儲存體連結服務，或建立一個新的，以供預備環境使用。

    c. 針對 [**暫存路徑**]，請在您選取的 Azure Blob 儲存體中指定 blob 容器，或將其保留空白，以使用預設值來進行暫存。

7. 按一下 [ **VNet 驗證**]，或 **[下一步]** 。 

8. 在 [**摘要**] 頁面上，檢查所有布建設定、將建議的檔連結設為書簽，然後按一下 **[完成]** 以開始建立您的整合執行時間。

    > [!NOTE]
    > 排除任何自訂的安裝時間，此程式應在5分鐘內完成，但 Azure SSIS IR 加入虛擬網路可能需要大約 20-30 分鐘。
    >
    > 如果您使用 SSISDB，ADF 服務將會連線到您資料庫伺服器來準備 SSISDB。 它也會為您的虛擬網路設定許可權和設定（若有指定），並將您的 Azure SSIS IR 加入虛擬網路。
    > 
    > 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源，這些元件還提供對 Excel/Access 檔案及各種 Azure 資料來源的連線能力。 您也可以安裝其他元件，請參閱 [Azure-SSIS IR 的自訂安裝](how-to-configure-azure-ssis-ir-custom-setup.md)。

7. 如有必要，切換到 [連線] 視窗中的 [整合執行階段]。 按一下 [重新整理] 即可重新整理狀態。

   ![建立狀態](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. 使用 [動作] 資料行下方的連結來停止/啟動、編輯或刪除整合執行階段。 使用最後一個連結來檢視整合執行階段的 JSON 程式碼。 只有當 IR 停止時，才會啟用編輯和刪除按鈕。

   ![Azure SSIS IR - 動作](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>入口網站中的 Azure SSIS 整合執行階段

1. 在 Azure Data Factory 使用者介面中，切換至 [編輯] 索引標籤，按一下 [連線]，然後切換到 [整合執行階段] 索引標籤，以檢視資料處理站中的現有整合執行階段。

   ![檢視現有的 IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. 按一下 [新增] 建立新的 Azure-SSIS IR。

   ![整合執行階段功能表](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. 若要建立 Azure-SSIS 整合執行階段，按一下 [新增]，如圖中所示。

4. 在 [整合執行階段設定] 視窗中，選取 [隨即轉移現有的 SSIS 套件以在 Azure 中執行]，然後按一下 [下一步]。

   ![指定整合執行階段的類型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. 請參閱[佈建 Azure SSIS 整合執行階段](#provision-an-azure-ssis-integration-runtime)一節，以取得設定 Azure SSIS IR 的剩餘步驟。

## <a name="azure-powershell"></a>Azure PowerShell

在本節中，會使用 Azure PowerShell 建立 Azure-SSIS IR。

### <a name="create-variables"></a>建立變數

複製並貼上下列指令碼 - 指定變數的值。 

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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>登入並選取訂用帳戶

在腳本中新增下列程式碼以登入並選取您的 Azure 訂用帳戶：

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>驗證資料庫伺服器的連線

新增下列腳本來驗證您的 Azure SQL Database 伺服器/受控執行個體。

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

如果您的資源群組已經存在，請勿將此程式碼複製到您的指令碼。 

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

如果您不是使用 SSISDB，您可以省略 CatalogServerEndpoint、CatalogPricingTier 和 CatalogAdminCredential 參數。

如果您未使用具有虛擬網路服務端點/受控執行個體的 Azure SQL Database 伺服器來裝載 SSISDB，或要求存取內部部署資料，您可以省略 VNetId 和子網參數，或為其傳遞空白值。 如果您將自我裝載 IR 設定為 Azure SSIS IR 的 proxy 以存取內部部署資料，您也可以省略它們。 否則，您不能省略它們，而必須從您的虛擬網路組態傳遞有效值，請參閱[將 Azure-SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

如果您使用受控執行個體來裝載 SSISDB，您可以省略 CatalogPricingTier 參數或為其傳遞空白值。 否則，您無法省略它，而必須從 Azure SQL Database 支援的定價層清單傳遞有效值，請參閱 [SQL Database 資源限制](../sql-database/sql-database-resource-limits.md)。

如果您使用 Azure Active Directory （AAD）驗證搭配您 ADF 的受控識別來連線到資料庫伺服器，您可以省略 CatalogAdminCredential 參數，但您必須將 ADF 的受控識別新增到具有存取權的 AAD 群組資料庫伺服器的許可權，請參閱[啟用 AZURE SSIS IR 的 AAD 驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。 否則，您不能省略它，而且必須傳遞由伺服器系統管理員使用者名稱和密碼所構成的有效物件來進行 SQL 驗證。

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

執行下列命令以啟動 Azure-SSIS 整合執行階段。

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
> 排除任何自訂的安裝時間，此程式應在5分鐘內完成，但 Azure SSIS IR 加入虛擬網路可能需要大約 20-30 分鐘。
>
> 如果您使用 SSISDB，ADF 服務將會連線到您資料庫伺服器來準備 SSISDB。 它也會為您的虛擬網路設定許可權和設定（若有指定），並將您的 Azure SSIS IR 加入虛擬網路。
> 
> 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源，這些元件還提供對 Excel/Access 檔案及各種 Azure 資料來源的連線能力。 您也可以安裝其他元件，請參閱 [Azure-SSIS IR 的自訂安裝](how-to-configure-azure-ssis-ir-custom-setup.md)。

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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

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

2. 若要部署 Azure Resource Manager 範本，請執行 New-azresourcegroupdeployment 命令，如下列範例所示，其中 ADFTutorialResourceGroup 是您的資源群組的名稱，而 Adftutorialarm.json 則是包含 JSON 定義的檔案您的 data factory 和 Azure SSIS IR。

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    這個命令會建立資料處理站，並且在其中建立 Azure-SSIS IR，但不會啟動 IR。

3. 若要啟動您的 Azure SSIS IR，請執行 AzDataFactoryV2IntegrationRuntime 命令：

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> 排除任何自訂的安裝時間，此程式應在5分鐘內完成，但 Azure SSIS IR 加入虛擬網路可能需要大約 20-30 分鐘。
>
> 如果您使用 SSISDB，ADF 服務將會連線到您資料庫伺服器來準備 SSISDB。 它也會為您的虛擬網路設定許可權和設定（若有指定），並將您的 Azure SSIS IR 加入虛擬網路。
> 
> 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源，這些元件還提供對 Excel/Access 檔案及各種 Azure 資料來源的連線能力。 您也可以安裝其他元件，請參閱 [Azure-SSIS IR 的自訂安裝](how-to-configure-azure-ssis-ir-custom-setup.md)。

## <a name="deploy-ssis-packages"></a>部署 SSIS 套件

如果您使用 SSISDB，您可以使用透過伺服器端點來與資料庫伺服器連線的 SSDT/SSMS 工具，將套件部署到其中，並在 Azure-SSIS IR 上執行這些套件。  針對在中使用具有公用端點之私用端點/受控實例的 Azure SQL Database server/受控執行個體，伺服器端點格式`<server name>.database.windows.net`會分別為`<server name>.public.<dns prefix>.database.windows.net,3342` /。 `<server name>.<dns prefix>.database.windows.net` / 如果您不是使用 SSISDB，您可以使用 `dtinstall`/`dtutil`/`dtexec` 命令列公用程式，將套件部署到檔案系統/檔案共用/Azure 檔案，並在 Azure-SSIS IR 上執行這些套件。 如需詳細資訊，請參閱[部署 SSIS 套件](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)。 在這兩種情況下，您也可以使用 ADF 管線中的執行 SSIS 套件活動在 Azure-SSIS IR 上執行已部署的套件，請參閱[以頂級 ADF 活動的形式叫用 SSIS 套件執行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

## <a name="next-steps"></a>後續步驟

另請參閱本檔中的其他 Azure SSIS IR 主題：

- [Azure-SSIS 整合執行階段](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供整合執行時間的一般相關資訊，包括 Azure SSIS IR。
- [監視 AZURE SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 本文說明如何取得及瞭解 Azure SSIS IR 的相關資訊。
- [管理 AZURE SSIS IR](manage-azure-ssis-integration-runtime.md)。 本文說明如何停止、啟動或刪除您的 Azure SSIS IR-它也會示範如何藉由新增更多節點來相應放大您的 Azure SSIS IR。
- [將 AZURE SSIS IR 加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。 本文提供將您的 Azure SSIS IR 加入虛擬網路的相關資訊。