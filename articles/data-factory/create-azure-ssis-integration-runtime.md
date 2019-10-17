---
title: 在 Azure Data Factory 中建立 Azure SSIS 整合執行時間 |Microsoft Docs
description: 了解如何在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段，您就可以在 Azure 部署並執行 SSIS 套件。
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
ms.openlocfilehash: ddc91a3317d362f6b56e486556f2edf6cdb85131
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326684"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段

本文提供在 Azure Data Factory 中布建 Azure SQL Server Integration Services （SSIS）整合執行時間（IR）的步驟。 Azure SSIS IR 支援：

- 執行部署到 Azure SQL Database 伺服器或受控實例（專案部署模型）所裝載之 SSIS 目錄（SSISDB）中的封裝。
- 執行部署至檔案系統、檔案共用或 Azure 檔案儲存體（封裝部署模型）中的封裝。 

布建 Azure SSIS IR 之後，您可以使用熟悉的工具，在 Azure 中部署和執行您的套件。 這些工具組括 SQL Server Data Tools、SQL Server Management Studio 和命令列工具（例如 `dtinstall`、`dtutil` 和 `dtexec`）。

布建[AZURE SSIS ir](tutorial-create-azure-ssis-runtime-portal.md)教學課程會示範如何透過 Azure 入口網站或 Data Factory 應用程式來建立 AZURE ssis ir。 本教學課程也會說明如何選擇性地使用 Azure SQL Database 伺服器或受控實例來裝載 SSISDB。 本文會針對教學課程進行擴充，並說明如何執行這些選擇性工作：

- 使用具有虛擬網路服務端點的 Azure SQL Database 伺服器，或搭配私人端點來裝載 SSISDB 的受控實例。 必要條件是您必須為您的 Azure SSIS IR 設定虛擬網路許可權和設定，以加入虛擬網路。

- 使用 Azure Active Directory （Azure AD）驗證搭配受控識別，讓您的資料處理站連線到 Azure SQL Database 伺服器或受控實例。 必要條件是，您必須將 data factory 的受控識別新增為可建立 SSISDB 實例的資料庫使用者。

- 將您的 Azure SSIS IR 加入虛擬網路，或設定自我裝載 IR 作為 Azure SSIS IR 的 proxy，以存取內部部署資料。

本文說明如何使用 Azure 入口網站、Azure PowerShell 和 Azure Resource Manager 範本來布建 Azure SSIS IR。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 訂用帳戶**。 如果您還沒有訂用帳戶，您可以建立[免費試用](https://azure.microsoft.com/pricing/free-trial/)帳戶。
- **Azure SQL Database 伺服器或受控實例（選擇性）** 。 如果您還沒有資料庫伺服器，請在 Azure 入口網站中建立一個，然後再開始。 Data Factory 接著會在此資料庫伺服器上建立 SSISDB 實例。 

  建議於整合執行階段所在的相同 Azure 區域中建立資料庫伺服器。 此設定可讓整合執行階段將執行記錄寫入 SSISDB，而不需要跨 Azure 區域。

  請記住下列重點：

  - 根據選取的資料庫伺服器，SSISDB 實例可以代表您建立為單一資料庫，做為彈性集區的一部分，或在受控實例中建立。 它可以在公用網路中或藉由加入虛擬網路來存取。 如需選擇資料庫伺服器類型來裝載 SSISDB 的指引，請參閱本文中的[比較 Azure SQL Database 單一資料庫、彈性集區和受控實例](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)一節。 
  
    如果您使用具有虛擬網路服務端點的 Azure SQL Database 伺服器，或具有私人端點的受控實例來裝載 SSISDB，或如果您需要存取內部部署資料而不設定自我裝載的 IR，則需要將您的 Azure SSIS IR 加入虛擬網路. 如需詳細資訊，請參閱將[AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。
  - 確認資料庫伺服器的 [允許存取 Azure 服務] 設定已啟用。 當您使用具有虛擬網路服務端點的 Azure SQL Database 伺服器，或搭配私人端點來裝載 SSISDB 的受控實例時，不適用此設定。 如需詳細資訊，請參閱[保護 Azure SQL 資料庫資料庫](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)。 若要使用 PowerShell 來啟用此設定，請參閱 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。
  - 將用戶端電腦的 IP 位址或 IP 位址範圍（包括用戶端電腦的 ip 位址）新增至資料庫伺服器防火牆設定中的用戶端 IP 位址清單。 如需詳細資訊，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](../sql-database/sql-database-firewall-configure.md)
  - 您可以使用 SQL 驗證搭配伺服器管理員認證來連線到資料庫伺服器，或透過適用于您 data factory 的受控識別來使用 Azure AD 驗證。 對於後者，您需要將資料處理站的受控識別新增到具有資料庫伺服器存取權限的 Azure AD 群組中。 如需詳細資訊，請參閱[啟用 AZURE SSIS IR 的 Azure AD 驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。
  - 確認您的資料庫伺服器尚未擁有 SSISDB 實例。 Azure SSIS IR 的布建不支援使用現有的 SSISDB 實例。
- **Azure Resource Manager 虛擬網路 (選擇性)** 。 如果下列至少一個條件成立，您就必須具備 Azure Resource Manager 虛擬網路：
    - 您要將 SSISDB 裝載在具有虛擬網路服務端點的 Azure SQL Database 伺服器上，或使用私人端點的受控實例。
    - 您想要從 Azure SSIS IR 上執行的 SSIS 套件連接到內部部署資料存放區，而不需設定自我裝載 IR。
- **Azure PowerShell （選擇性）** 。 如果您想要執行 PowerShell 指令碼來佈建 Azure-SSIS IR，請遵循[如何安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps) 中的指示。

### <a name="regional-support"></a>區域支援

如需可使用 Data Factory 和 Azure SSIS IR 的 Azure 區域清單，請參閱[依區域的 Data Factory 和 SSIS ir 可用性](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)。

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>SQL Database 單一資料庫、彈性集區和受控實例的比較

下表比較 Azure SQL Database 伺服器和受控實例的特定功能，因為它們與 Azure SSIR IR 相關：

| 功能 | 單一資料庫/彈性集區| 受控執行個體 |
|---------|--------------|------------------|
| **排程** | SQL Server Agent 無法使用。<br/><br/>請參閱[在 Data Factory 管線中排程封裝執行](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)。| 受控執行個體代理程式可供使用。 |
| **驗證** | 您可以使用自主資料庫使用者建立 SSISDB 實例，其代表具有 data factory 受控識別的任何 Azure AD 群組，做為**db_owner**角色中的成員。<br/><br/>請參閱[啟用 Azure AD 驗證，以在 Azure SQL Database 伺服器上建立 SSISDB 實例](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database)。 | 您可以使用自主資料庫使用者來建立 SSISDB 實例，以代表您的 data factory 受控識別。 <br/><br/>請參閱[啟用 Azure AD 驗證，以在 Azure SQL Database 受控實例中建立 SSISDB 實例](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance)。 |
| **服務層** | 當您使用 Azure SQL Database 伺服器建立 Azure SSIS IR 時，可以選取 SSISDB 的服務層級。 目前有多個服務層級。 | 當您使用受控實例建立 Azure SSIS IR 時，您無法選取 SSISDB 的服務層級。 受控實例中的所有資料庫會共用配置給該實例的相同資源。 |
| **虛擬網路** | 只有當您使用具有虛擬網路服務端點的 Azure SQL Database 伺服器，或如果您需要存取內部部署資料存放區，而不需設定自我裝載的 IR 時，您的 Azure SSIS IR 才可以聯結 Azure Resource Manager 虛擬網路。 | 您的 Azure SSIS IR 只能聯結 Azure Resource Manager 虛擬網路。 當您未啟用受控實例的公用端點時，需要虛擬網路。<br/><br/>如果您將 Azure SSIS IR 加入與受控實例相同的虛擬網路，請確定您的 Azure SSIS IR 與受控實例位於不同的子網中。 如果您將 Azure SSIS IR 加入受控實例中的不同虛擬網路，建議使用虛擬網路對等互連或網路對網路連線。 請參閱[將您的應用程式連線至 Azure SQL Database 受控實例](../sql-database/sql-database-managed-instance-connect-app.md)。 |
| **分散式交易** | 透過彈性交易可支援這項功能。 不支援 Microsoft Distributed Transaction Coordinator (MSDTC) 交易。 如果您的 SSIS 套件使用 MSDTC 來協調分散式交易，請考慮遷移至彈性交易以進行 Azure SQL Database。 如需詳細資訊，請參閱[跨雲端資料庫的分散式交易](../sql-database/sql-database-elastic-transactions-overview.md)。 | 不支援。 |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>使用 Azure 入口網站建立整合執行時間

在本節中，您會使用 Azure 入口網站，特別是 Data Factory 的使用者介面（UI）或應用程式，來建立 Azure SSIS IR。

### <a name="create-a-data-factory"></a>建立 Data Factory

若要透過 Azure 入口網站建立您的資料處理站，請遵循透過[UI 建立 data factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)中的逐步指示。 執行此動作時，請選取 [**釘選到儀表板**]，以允許在建立後快速存取。 

建立 data factory 之後，請在 Azure 入口網站中開啟其 總覽 頁面。 選取 [**作者 & 監視器**] 圖格，以在另一個索引標籤上開啟其「**我們的**「開始使用」頁面。在這裡，您可以繼續建立您的 Azure SSIS IR。   

### <a name="provision-an-azure-ssis-integration-runtime"></a>佈建 Azure-SSIS 整合執行階段

1. 在 [讓我們開始吧] 頁面上，選取 [設定 SSIS 整合執行階段] 圖格。

   ![設定 SSIS 整合執行階段圖格](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. 在**Integration Runtime 設定** 的 **一般設定** 頁面上，完成下列步驟。

   ![一般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. 針對 [名稱]，輸入取您整合執行階段的名稱。

    b.這是另一個 C# 主控台應用程式。 針對 [描述]，輸入整合執行階段的描述。

    c. 針對 [位置]，選取整合執行階段的位置。 系統只會顯示支援的位置。 我們建議您選取裝載 SSISDB 的資料庫伺服器所在位置。

    d. 針對 [**節點大小**]，選取整合執行時間叢集中的節點大小。 系統只會顯示支援的節點大小。 如果您想要執行許多計算密集型或記憶體密集型套件，請選取大型節點大小（相應增加）。

    e. 針對 [節點數目]，選取整合執行階段叢集中的節點數目。 系統只會顯示支援的節點數目。 如果您想要平行執行許多套件，請選取具有許多節點（相應放大）的大型叢集。

    f. 針對 [**版本/授權**]，選取整合執行時間的 SQL Server 版本： [標準] 或 [企業]。 如果您想要在整合執行時間上使用 advanced 功能，請選取 [企業]。

    g. 針對 [**節省費用**]，選取整合執行時間的 [Azure Hybrid Benefit **] 選項： [是]** 或 [**否**]。 如果您想要攜帶自己的 SQL Server 授權與軟體保證，以利用混合式使用的成本節約，請選取 **[是]** 。

    h. 選取 [下一步]。

3. 在 [ **SQL 設定**] 頁面上，完成下列步驟。

   ![SQL 設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. 選取 [**建立 SSIS 目錄 ...** ] 核取方塊，選擇要在您的 AZURE SSIS IR 上執行之封裝的部署模型。 您將選擇專案部署模型，其中封裝會部署到您的資料庫伺服器所裝載的 SSISDB，或是封裝部署模型，其中套件會部署到檔案系統、檔案共用或 Azure 檔案儲存體。 
    
   如果您選取此核取方塊，您必須攜帶自己的資料庫伺服器，以裝載我們將代表您建立和管理的 SSISDB 實例。
   
   b.這是另一個 C# 主控台應用程式。 針對 [訂用帳戶]，選取具有資料庫伺服器可裝載 SSISDB 的 Azure 訂用帳戶。 

   c. 針對 [位置]，選取裝載 SSISDB 的資料庫伺服器所在位置。 我們建議您選取整合執行階段所在的相同位置。 

   d. 針對 [目錄資料庫伺服器端點]，選取裝載 SSISDB 的資料庫伺服器端點。 
    
   根據選取的資料庫伺服器，SSISDB 實例可以代表您建立為單一資料庫，做為彈性集區的一部分，或在受控實例中建立。 它可以在公用網路中或藉由加入虛擬網路來存取。 如需選擇資料庫伺服器類型來裝載 SSISDB 的指引，請參閱本文中的[比較 Azure SQL Database 單一資料庫、彈性集區和受控實例](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)一節。 
    
   如果您選取具有虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控實例來裝載 SSISDB，或如果您需要存取內部部署資料而不設定自我裝載的 IR，則需要將您的 Azure SSIS IR 加入至虛擬網路。 如需詳細資訊，請參閱將[AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 

   e. 選取 [對**您的 ADF 使用 AAD 驗證搭配受控識別**] 核取方塊，為您的資料庫伺服器選擇用來裝載 SSISDB 的驗證方法。 您將選擇 [SQL 驗證] 或 [使用您 data factory 的受控識別來 Azure AD 驗證]。 
    
   如果您選取此核取方塊，您必須將資料處理站的受控識別新增到具有資料庫伺服器存取權限的 Azure AD 群組中。 如需詳細資訊，請參閱[啟用 AZURE SSIS IR 的 Azure AD 驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。 

   f. 針對 [**管理員使用者名稱**]，輸入您的資料庫伺服器要裝載 SSISDB 的 SQL 驗證使用者名稱。 

   g. 針對 [**管理員密碼**]，輸入您的資料庫伺服器用來裝載 SSISDB 的 SQL 驗證密碼。 

   h. 針對 [**目錄資料庫服務層級**]，選取資料庫伺服器用來裝載 SSISDB 的服務層級。 選取 [基本]、[標準] 或 [Premium] 層，或選取彈性集區名稱。 

   i. 選取 [測試連線]。 如果測試成功，請選取 **[下一步]** 。 

4. 在 [ **Advanced Settings** ] （設定）頁面上，完成下列步驟。

   ![進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. 針對 [**每個節點的平行**執行數上限]，選取整合執行時間叢集中每個節點要同時執行的封裝數目上限。 系統只會顯示支援的套件數目。 如果您想要使用一個以上的核心來執行大量計算或需要海量儲存體的大型套件，請選取較低的數位。 如果您想要在單一核心中執行一或多個小型套件，請選取較高的數位。

   b.這是另一個 C# 主控台應用程式。 針對**自訂安裝容器 SAS URI**，選擇性地輸入您的安裝腳本及其相關檔案儲存所在之 Azure Blob 儲存體容器的共用存取簽章（SAS）統一資源識別項（URI）。 如需詳細資訊，請參閱[自訂 AZURE SSIS IR 的設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

5. 選取 [**選取要加入您的 AZURE SSIS Integration Runtime 的 VNet，並允許 ADF 建立特定的網路資源**] 核取方塊，以選擇是否要將整合執行時間加入虛擬網路。 

   如果您使用具有虛擬網路服務端點的 Azure SQL Database 伺服器或具有私人端點的受控實例來裝載 SSISDB，或您需要存取內部部署資料，請選取它。 （也就是說，您的 SSIS 套件中有內部部署資料來源或目的地，而不需設定自我裝載的 IR）。如需詳細資訊，請參閱將[AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 

   如果您選取此核取方塊，請完成下列步驟。

   ![虛擬網路進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

   a. 針對 [訂用帳戶]，選取具有您的虛擬網路的 Azure 訂用帳戶。

   b.這是另一個 C# 主控台應用程式。 針對 [位置]，選取整合執行階段的相同位置。

   c. 針對 [**類型**]，選取您的虛擬網路類型： [傳統] 或 [Azure Resource Manager]。 我們建議您選取 Azure Resource Manager 虛擬網路，因為傳統虛擬網路即將淘汰。

   d. 針對 **[VNet 名稱]** ，選取您虛擬網路的名稱。 此虛擬網路應該與虛擬網路服務端點或虛擬網路中的受控實例用來裝載 SSISDB 的 Azure SQL Database 伺服器相同。 或者，此虛擬網路應該與連線到內部部署網路的虛擬網路相同。

   e. 針對 [子網路名稱]，選取虛擬網路的子網路名稱。 這應該與用於虛擬網路中的受控實例用來裝載 SSISDB 的子網不同。

6. 選取 [**設定自我裝載的 Integration Runtime 做為您的 AZURE ssis Integration Runtime 的 proxy** ] 核取方塊，選擇是否要將自我裝載 ir 設定為 AZURE ssis IR 的 proxy。 如需詳細資訊，請參閱[設定自我裝載 IR 作為 proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 

   如果您選取此核取方塊，請完成下列步驟。

   ![使用自我裝載 IR 的 Advanced 設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

   a. 對於**自我**裝載的 Integration Runtime，請選取您現有的自我裝載 IR 作為 AZURE SSIS IR 的 proxy。

   b.這是另一個 C# 主控台應用程式。 針對**暫存儲存體連結服務**，請選取您現有的 Azure Blob 儲存體連結服務。 或者，建立一個新的預備環境。

   c. 針對 [**暫存路徑**]，請在您選取的 Azure blob 儲存體帳戶中指定 blob 容器。 或者，將它保留為空白，以使用預設值進行暫存。

7. 選取 [ **VNet 驗證** >  **] [下一步]** 。 

8. 在 [**摘要**] 頁面上，檢查所有布建設定、將建議的檔連結設為書簽，然後選取 **[完成]** 以開始建立您的整合執行時間。

    > [!NOTE]
    > 排除任何自訂的安裝時間，此程式應該會在5分鐘內完成。 但可能需要20-30 分鐘的時間，Azure SSIS IR 才能加入虛擬網路。
    >
    > 如果您使用 SSISDB，Data Factory 服務會連線到您的資料庫伺服器，以準備 SSISDB。 它也會為您的虛擬網路設定許可權和設定（若有指定），並將您的 Azure SSIS IR 加入虛擬網路。
    > 
    > 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源之外，這些元件還提供與 Excel 檔案、存取檔案和各種 Azure 資料來源的連線。 如需您可以安裝之其他元件的詳細資訊，請參閱[自訂 AZURE SSIS IR 的設定](how-to-configure-azure-ssis-ir-custom-setup.md)。

7. 如有必要，在 [連線] 索引標籤上，切換到 [整合執行階段]。 選取 [重新整理] 可重新整理狀態。

   ![建立狀態](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. 使用 [動作] 資料行中的連結來停止/啟動、編輯或刪除整合執行階段。 使用最後一個連結來檢視整合執行階段的 JSON 程式碼。 只有當 IR 停止時，才會啟用編輯和刪除按鈕。

   ![Azure SSIS IR 動作](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>入口網站中的 Azure SSIS 整合執行階段

1. 在 [Azure Data Factory] UI 中，切換至 [**編輯**] 索引標籤，然後選取 [**連接**]。 然後切換至 [**整合運行**時間] 索引標籤，以在您的 data factory 中查看現有的整合執行時間。

   ![檢視現有的 IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. 選取 [**新增**] 以建立新的 AZURE SSIS IR。

   ![整合執行階段功能表](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

4. 在 [整合執行階段設定] 視窗中，選取 [隨即轉移現有的 SSIS 套件以在 Azure 中執行]，然後選取 [下一步]。

   ![指定整合執行階段的類型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. 如需設定 Azure SSIS IR 的其餘步驟，請參閱布建[AZURE ssis 整合運行](#provision-an-azure-ssis-integration-runtime)時間一節。

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>使用 Azure PowerShell 建立整合執行時間

在本節中，您會使用 Azure PowerShell 來建立 Azure SSIS IR。

### <a name="create-variables"></a>建立變數

複製並貼上下列指令碼。 指定變數值。 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"
```

### <a name="sign-in-and-select-a-subscription"></a>登入並選取訂用帳戶

新增下列腳本以登入並選取您的 Azure 訂用帳戶。

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>驗證資料庫伺服器的連線

新增下列腳本來驗證您的 Azure SQL Database 伺服器或受控實例。

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
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

### <a name="configure-the-virtual-network"></a>設定虛擬網路

新增下列腳本，以自動設定虛擬網路許可權和設定，讓您的 Azure SSIS 整合執行時間加入。

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

使用[remove-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup)命令建立[Azure 資源群組](../azure-resource-manager/resource-group-overview.md)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。

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

如果您不使用 SSISDB，可以省略 `CatalogServerEndpoint`、`CatalogPricingTier` 和 @no__t 2 參數。

如果您未使用具有虛擬網路服務端點的 Azure SQL Database 伺服器，或具有私人端點的受控實例來裝載 SSISDB，或如果您需要存取內部部署資料，則可以省略 `VNetId` 和 @no__t 1 參數，或傳遞空值客戶. 如果您將自我裝載 IR 設定為 Azure SSIS IR 的 proxy 以存取內部部署資料，您也可以省略它們。 否則，您不能省略它們，而且必須從您的虛擬網路設定傳遞有效的值。 如需詳細資訊，請參閱將[AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

如果您使用受控實例來裝載 SSISDB，您可以省略 `CatalogPricingTier` 參數，或為其傳遞空白值。 否則，您不能省略它，而且必須從 Azure SQL Database 的支援定價層清單中傳遞有效的值。 如需詳細資訊，請參閱[SQL Database 資源限制](../sql-database/sql-database-resource-limits.md)。

如果您使用 Azure AD 驗證搭配受控識別，讓您的資料處理站連接到資料庫伺服器，您可以省略 `CatalogAdminCredential` 參數。 但是，您必須將資料處理站的受控識別新增到具有資料庫伺服器存取權限的 Azure AD 群組中。 如需詳細資訊，請參閱[啟用 AZURE SSIS IR 的 Azure AD 驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。 否則，您不能省略它，而且必須傳遞以伺服器管理員使用者名稱和密碼形成的有效物件，以進行 SQL 驗證。

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
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add the SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-the-integration-runtime"></a>啟動整合執行時間

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
> 排除任何自訂的安裝時間，此程式應該會在5分鐘內完成。 但可能需要20-30 分鐘的時間，Azure SSIS IR 才能加入虛擬網路。
>
> 如果您使用 SSISDB，Data Factory 服務會連線到您的資料庫伺服器，以準備 SSISDB。 它也會為您的虛擬網路設定許可權和設定（若有指定），並將您的 Azure SSIS IR 加入虛擬網路。
> 
> 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源之外，這些元件還提供與 Excel 檔案、存取檔案和各種 Azure 資料來源的連線。 如需您可以安裝之其他元件的詳細資訊，請參閱[自訂 AZURE SSIS IR 的設定](how-to-configure-azure-ssis-ir-custom-setup.md)。

### <a name="full-script"></a>完整指令碼

以下是建立 Azure SSIS 整合執行時間的完整腳本。

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
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

### Configure a virtual network
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

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add the SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>使用 Azure Resource Manager 範本來建立整合執行時間

在本節中，您會使用 Azure Resource Manager 範本來建立 Azure SSIS 整合執行時間。 以下是範例逐步解說：

1. 使用下列 Azure Resource Manager 範本建立 JSON 檔案。 將角括弧（預留位置）中的值取代為您自己的值。

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

2. 若要部署 Azure Resource Manager 範本，請執行 `New-AzResourceGroupDeployment` 命令，如下列範例所示。 在範例中，`ADFTutorialResourceGroup` 是資源群組的名稱。 `ADFTutorialARM.json` 是檔案，其中包含您的 data factory 和 Azure SSIS IR 的 JSON 定義。

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    此命令會在其中建立您的 data factory 和 Azure SSIS IR，但不會啟動 IR。

3. 若要啟動您的 Azure SSIS IR，請執行 `Start-AzDataFactoryV2IntegrationRuntime` 命令：

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> 排除任何自訂的安裝時間，此程式應該會在5分鐘內完成。 但可能需要20-30 分鐘的時間，Azure SSIS IR 才能加入虛擬網路。
>
> 如果您使用 SSISDB，Data Factory 服務會連線到您的資料庫伺服器，以準備 SSISDB。 它也會為您的虛擬網路設定許可權和設定（若有指定），並將您的 Azure SSIS IR 加入虛擬網路。
> 
> 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源之外，這些元件還提供與 Excel 檔案、存取檔案和各種 Azure 資料來源的連線。 如需您可以安裝之其他元件的詳細資訊，請參閱[自訂 AZURE SSIS IR 的設定](how-to-configure-azure-ssis-ir-custom-setup.md)。

## <a name="deploy-ssis-packages"></a>部署 SSIS 套件

如果您使用 SSISDB，您可以使用 SQL Server Data Tools 或 SQL Server Management Studio 工具，將套件部署到其中，並在 Azure SSIS IR 上執行。 這些工具會透過其伺服器端點連接到您的資料庫伺服器： 

- 針對具有私用端點的 Azure SQL Database 伺服器，伺服器端點格式為 `<server name>.database.windows.net`。
- 針對具有私用端點的受控實例，伺服器端點格式為 `<server name>.<dns prefix>.database.windows.net`。
- 針對具有公用端點的受控實例，伺服器端點格式為 `<server name>.public.<dns prefix>.database.windows.net,3342`。 

如果您不使用 SSISDB，可以將封裝部署到檔案系統、檔案共用或 Azure 檔案儲存體。 然後，您可以使用 `dtinstall`、`dtutil` 和 @no__t 2 命令列工具，在 Azure SSIS IR 上執行它們。 如需詳細資訊，請參閱[部署 SSIS 套件](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)。 

在這兩種情況下，您也可以使用 Data Factory 管線中的執行 SSIS 套件活動，在 Azure SSIS IR 上執行已部署的套件。 如需詳細資訊，請參閱[將 SSIS 封裝執行當做第一類 Data Factory 活動叫用](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

## <a name="next-steps"></a>後續步驟

請參閱本檔中的其他 Azure SSIS IR 主題：

- [Azure-SSIS 整合執行階段](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供整合執行時間的一般相關資訊，包括 Azure SSIS IR。
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文說明如何取得及瞭解 Azure SSIS IR 的相關資訊。
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文說明如何停止、啟動或刪除您的 Azure SSIS IR。 它也會示範如何藉由新增更多節點來相應放大您的 Azure SSIS IR。
- [將 Azure-SSIS IR 加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。 本文提供將您的 Azure SSIS IR 加入虛擬網路的相關資訊。
