---
title: 將 Azure SSIS 整合執行時間加入虛擬網路 |Microsoft Docs
description: 瞭解如何將 Azure SSIS 整合執行時間加入 Azure 虛擬網路。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 92687b7cb8cdad8612f5a44833efcca351c45a43
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114748"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>將 Azure-SSIS 整合執行階段加入虛擬網路
在 Azure Data Factory 中使用 SQL Server Integration Services (SSIS) 時, 您應該在下列案例中, 將您的 Azure SSIS 整合執行時間 (IR) 加入 Azure 虛擬網路: 

- 您想要從 Azure SSIS IR 上執行的 SSIS 套件連接到內部部署資料存放區, 而不需要設定或管理自我裝載 IR 作為 proxy。 

- 您想要從 Azure SSIS IR 上執行的 SSIS 套件, 連接至虛擬網路服務端點所支援的 Azure 服務資源。

- 您要在虛擬網路中使用虛擬網路服務端點或受控實例的 Azure SQL Database 中裝載 SSIS 目錄資料庫 (SSISDB)。 

Data Factory 可讓您將 Azure SSIS IR 加入透過傳統部署模型或 Azure Resource Manager 部署模型所建立的虛擬網路。 

> [!IMPORTANT]
> 傳統虛擬網路即將淘汰, 因此請改用 Azure Resource Manager 的虛擬網路。  如果您已經使用傳統虛擬網路, 請儘快切換到 Azure Resource Manager 的虛擬網路。

## <a name="access-to-on-premises-data-stores"></a>存取內部部署資料存放區
如果您的 SSIS 套件存取內部部署資料存放區, 您可以將您的 Azure SSIS IR 加入連線到內部部署網路的虛擬網路。 或者, 您可以設定或管理自我裝載 IR, 做為您的 Azure SSIS IR 的 proxy。 如需詳細資訊, 請參閱[設定自我裝載 ir 作為 AZURE SSIS IR 的 proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 

將您的 Azure SSIS IR 加入虛擬網路時, 請記住下列重點: 

- 如果沒有虛擬網路連線到您的內部部署網路, 請先建立一個[Azure Resource Manager 虛擬網路](../virtual-network/quick-create-portal.md#create-a-virtual-network), 讓您的 AZURE SSIS IR 加入。 然後, 設定從該虛擬網路到內部部署網路的站對站[VPN 閘道聯](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)機或[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md)連線。 

- 如果 Azure Resource Manager 的虛擬網路已連線到與您的 Azure SSIS IR 位於相同位置的內部部署網路, 則可以將 IR 加入該虛擬網路。 

- 如果傳統虛擬網路已從您的 Azure SSIS IR 在不同位置連線到您的內部部署網路, 您可以建立[Azure Resource Manager 虛擬網路](../virtual-network/quick-create-portal.md#create-a-virtual-network), 讓您的 AZURE ssis ir 加入。 然後，設定[傳統對 Azure Resource Manager 虛擬網路](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)連線。 
 
- 如果 Azure Resource Manager 的虛擬網路已從您的 Azure SSIS IR 連線到不同位置的內部部署網路, 您可以先建立[Azure Resource Manager 的虛擬網路](../virtual-network/quick-create-portal.md##create-a-virtual-network), 讓您的 AZURE ssis ir 能夠加入。 然後設定 Azure Resource Manager 對 Azure Resource Manager 的虛擬網路連線。 

## <a name="access-to-azure-services"></a>存取 Azure 服務
如果您的 SSIS 套件存取[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)所支援的 azure 服務資源, 而且您想要將這些資源保護到 AZURE ssis ir, 您可以將您的 AZURE ssis ir 加入使用虛擬網路設定的虛擬網路子網服務端點。 同時, 將虛擬網路規則新增至 Azure 服務資源, 以允許來自相同子網的存取。

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>在 SQL Database 中裝載 SSIS 目錄
如果您使用虛擬網路服務在 Azure SQL Database 中裝載您的 SSIS 目錄，請確定將您的 Azure-SSIS IR 加入到相同的虛擬網路和子網路。

若要將您的 Azure SSIS IR 加入與受控實例相同的虛擬網路, 請確定 Azure SSIS IR 位於與受控實例不同的子網中。 若要將您的 Azure SSIS IR 加入與受控實例不同的虛擬網路, 我們建議虛擬網路對等互連 (僅限於相同的區域) 或從虛擬網路到虛擬網路的連線。 如需詳細資訊, 請參閱[將您的應用程式連線至 Azure SQL Database 受控實例](../sql-database/sql-database-managed-instance-connect-app.md)。

在所有情況下, 只能透過 Azure Resource Manager 部署模型來部署虛擬網路。

下列各節提供更多詳細資料。 

## <a name="virtual-network-configuration"></a>虛擬網路設定

設定您的虛擬網路以符合下列需求: 

-   請確定在`Microsoft.Batch`裝載 Azure SSIS IR 的虛擬網路子網訂用帳戶下, 是已註冊的提供者。 如果您使用傳統虛擬網路, 也請加入`MicrosoftAzureBatch`該虛擬網路的傳統虛擬機器參與者角色。 

-   請確定您擁有必要權限。 如需詳細資訊, 請參閱[設定許可權](#perms)。

-   選取要裝載 Azure-SSIS IR 的適當子網路。 如需詳細資訊, 請參閱[選取子網](#subnet)。 

-   如果您在虛擬網路上使用自己的網域名稱系統 (DNS) 伺服器, 請參閱[設定 DNS 伺服器](#dns_server)。 

-   如果您在子網上使用網路安全性群組 (NSG), 請參閱[設定 NSG](#nsg)。 

-   如果您使用 Azure ExpressRoute 或使用者定義的路由 (UDR), 請參閱[使用 Azure expressroute 或 UDR](#route)。 

-   請確定虛擬網路的資源群組可以建立和刪除特定的 Azure 網路資源。 如需詳細資訊, 請參閱[設定資源群組](#resource-group)。 

-   如果您依照[自訂 AZURE SSIS ir 的設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)中所述自訂您的 AZURE ssis ir, 您的 AZURE ssis ir 節點將會從預先定義的172.16.0.0 範圍取得私人 IP 位址到172.31.255.255。 因此, 請確定您虛擬或內部部署網路的私人 IP 位址範圍不會與此範圍衝突。

下圖顯示您的 Azure SSIS IR 所需的連線:

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>設定許可權

建立 Azure SSIS IR 的使用者必須具有下列許可權:

- 如果您正要將 SSIS IR 加入 Azure Resource Manager 虛擬網路，您有兩個選項：

  - 使用內建網路參與者角色。 此角色隨附 _microsoft.network /\*_  權限，它的權限範圍大於必要的範圍。

  - 建立包含只有必要 _Microsoft.Network/virtualNetworks/\*/join/action_ 權限的自訂角色。 

- 如果您要將 SSIS IR 加入傳統虛擬網路, 建議您使用內建的傳統虛擬機器參與者角色。 否則，您必須定義自訂角色，以包含可加入虛擬網路的權限。

### <a name="subnet"></a> 選取子網路

當您選擇子網時: 

-   請勿選取 GatewaySubnet 以部署 Azure SSIS IR。 它專用於虛擬網路閘道。 

-   請確定您選取的子網具有足夠的可用位址空間, 以供 Azure SSIS IR 使用。 將可用的 IP 位址保留至少兩倍的 IR 節點編號。 Azure 會在每個子網路中保留一些 IP 位址。 無法使用這些位址。 子網的第一個和最後一個 IP 位址會保留給通訊協定一致性, 而其他三個位址則用於 Azure 服務。 如需詳細資訊，請參閱 [在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   請勿使用由其他 Azure 服務 (例如, SQL Database 受控實例、App Service 等等) 專門佔用的子網。 

### <a name="dns_server"></a>設定 DNS 伺服器 
如果您需要在您的 Azure SSIS IR 所加入的虛擬網路中使用自己的 DNS 伺服器, 請確定它可以解析全域 Azure 主機名稱 (例如, 名為`<your storage account>.blob.core.windows.net`的 Azure 儲存體 blob)。 

以下為建議步驟： 

-   設定自訂 DNS 將要求轉送至 Azure DNS。 您可以將未解析的 DNS 記錄轉送至您自己的 DNS 伺服器上的 Azure 遞迴解析程式的 IP 位址 (168.63.129.16)。 

-   將自訂 DNS 設定為虛擬網路的主要 DNS 伺服器。 將 Azure DNS 設定為次要 DNS 伺服器。 在您自己的 DNS 伺服器無法使用的情況下, 將 Azure 遞迴解析程式的 IP 位址 (168.63.129.16) 註冊為次要 DNS 伺服器。 

如需詳細資訊, 請參閱[使用您自己的 DNS 伺服器的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。 

### <a name="nsg"></a>設定 NSG
如果您需要為您的 Azure SSIS IR 所使用的子網執行 NSG, 請允許透過下列埠的輸入和輸出流量: 

| Direction | 傳輸通訊協定 | Source | 來源連接埠範圍 | 目的地 | Destination port range | 註解 |
|---|---|---|---|---|---|---|
| 傳入 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877 (如果您將 IR 加入 Resource Manager 虛擬網路) <br/><br/>10100、20100、30100 (如果您將 IR 加入傳統虛擬網路)| Data Factory 服務會使用這些埠與虛擬網路中的 Azure SSIS IR 節點進行通訊。 <br/><br/> 無論您是否建立子網層級 NSG, Data Factory 一律會在連接至裝載 Azure SSIS IR 之虛擬機器的網路介面卡 (Nic) 層級上設定 NSG。 該 NIC 層級的 NSG 僅允許來自指定連接埠上 Data Factory IP 位址的輸入流量。 即使您對子網層級的網際網路流量開啟這些埠, 來自不 Data Factory IP 位址的 IP 位址流量也會在 NIC 層級遭到封鎖。 |
| 傳出 | TCP | VirtualNetwork | * | AzureCloud | 443 | 虛擬網路中的 Azure SSIS IR 節點會使用此埠來存取 Azure 服務, 例如 Azure 儲存體和 Azure 事件中樞。 |
| 傳出 | TCP | VirtualNetwork | * | 網際網路 | 80 | 虛擬網路中 Azure SSIS IR 的節點會使用此埠, 從網際網路下載憑證撤銷清單。 |
| 傳出 | TCP | VirtualNetwork | * | Sql | 1433、11000-11999 | 虛擬網路中的 Azure SSIS IR 節點會使用這些埠來存取 SQL Database 伺服器所主控的 SSISDB。 如果您的 SQL Database 伺服器連線原則設定為 [ **Proxy** ] 而不是 [重新**導向**], 則只需要端口1433。 此輸出安全性規則不適用於虛擬網路中受控實例所裝載的 SSISDB。 |
||||||||

### <a name="route"></a>使用 Azure ExpressRoute 或 UDR
當您將[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)線路連線至虛擬網路基礎結構, 以將內部部署網路擴充至 Azure 時, 一般設定會使用強制通道 (向虛擬網路通告 BGP 路由, 0.0.0.0/0)。 此通道會強制從虛擬網路流量流向內部部署網路應用裝置的輸出網際網路流量, 以進行檢查和記錄。 
 
或者, 您也可以定義[udr](../virtual-network/virtual-networks-udr-overview.md) , 以強制將來自裝載 AZURE SSIS IR 之子網的輸出網際網路流量, 強制傳送至裝載網路虛擬裝置 (NVA) 作為防火牆或 Azure 防火牆進行檢查和記錄的另一個子網。 

在這兩種情況下, 流量路由會將必要的輸入連線中斷, 從相依的 Azure Data Factory 服務 (尤其是 Azure Batch 管理服務) 到虛擬網路中的 Azure SSIS IR。 若要避免這種情況, 請在包含 Azure SSIS IR 的子網上定義一或多個 Udr。 

您可以在 Azure ExpressRoute 案例中裝載 Azure SSIS IR 的子網上, 將 0.0.0.0/0 路由的 [下一個躍點類型] 設為 [**網際網路**]。 或者, 您可以在 NVA 案例中, 將現有的 0.0.0.0/0 路由從 [下一個躍點類型] 修改為 [**虛擬裝置**到**網際網路**]。

![新增路由](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

如果您擔心無法檢查該子網的輸出網際網路流量, 您可以定義特定 Udr, 只在 Azure Batch 管理服務和 Azure SSIS IR 之間路由傳送流量, 並將下一個躍點類型設為**網際網路**。

例如, 如果您的`UK South`Azure SSIS IR 位於, 您會`BatchNodeManagement.UKSouth`從[服務標記 ip 範圍下載連結](https://www.microsoft.com/en-us/download/details.aspx?id=56519)或透過服務標籤[探索 API](https://aka.ms/discoveryapi)取得服務標籤的 ip 範圍清單。 然後將下列相關 IP 範圍路由的 Udr 套用至下一個躍點類型為**網際網路**。

![Azure Batch UDR 設定](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> 這種方法會產生額外的維護成本。 定期檢查 IP 範圍, 並將新的 IP 範圍新增至您的 UDR, 以避免中斷 Azure SSIS IR。 我們建議您每月檢查 IP 範圍, 因為新 IP 出現在服務標籤時, IP 會花費另一個月生效。 

### <a name="resource-group"></a>設定資源群組
Azure-SSIS IR 需要在與虛擬網路相同的資源群組下，建立特定的網路資源。 這些資源包括:
   -   具有名稱 *\<Guid >-azurebatch-cloudserviceloadbalancer*的 Azure 負載平衡器。
   -   Azure 公用 IP 位址, 其名稱 *\<為 Guid >-azurebatch-cloudservicepublicip*。
   -   網路工作安全性群組, 其名稱 *\<為 Guid >-azurebatch-cloudservicenetworksecuritygroup*。 

當 IR 啟動時, 將會建立這些資源。 當 IR 停止時, 將會刪除它們。 若要避免封鎖紅外線停止, 請勿在您的其他資源中重複使用這些網路資源。 

請確定虛擬網路所屬的資源群組或訂用帳戶上沒有資源鎖定。 如果您設定唯讀鎖定或刪除鎖定, 啟動和停止 IR 可能會失敗, 或 IR 可能會停止回應。 

請確定您沒有 Azure 原則, 而無法在虛擬網路所屬的資源群組或訂用帳戶之下建立下列資源: 
   -   Microsoft.Network/LoadBalancers 
   -   Microsoft.Network/NetworkSecurityGroups 
   -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a>常見問題集

- 如何保護在 Azure SSIS IR 上針對輸入連接公開的公用 IP 位址？ 是否可以移除公用 IP 位址？
 
    現在, 當 Azure SSIS IR 加入虛擬網路時, 將會自動建立公用 IP 位址。 我們有一個 NIC 層級的 NSG, 只允許 Azure Batch 管理服務對 Azure SSIS IR 進行輸入連線。 您也可以指定輸入保護的子網層級 NSG。

    如果您不想公開公用 IP 位址, 請考慮將[自我裝載 IR 設定為 AZURE SSIS ir 的 proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) , 而不是虛擬網路 (如果適用于您的案例)。
 
- 我可以將 Azure SSIS IR 的靜態 IP 位址新增至資料來源的防火牆允許清單嗎？
 
    - 如果您的資料來源是內部部署, 則在將虛擬網路連線到內部部署網路, 並將您的 Azure SSIS IR 加入至虛擬網路子網之後, 您可以將該子網的 IP 範圍新增至允許清單。
    - 如果您的資料來源是虛擬網路服務端點所支援的 Azure 服務, 您可以在虛擬網路上設定虛擬網路服務點, 並將您的 Azure SSIS IR 加入該虛擬網路子網。 接著, 您可以使用 Azure 服務的虛擬網路規則, 而不是 IP 範圍來允許存取。
    - 如果您的資料來源是不同種類的雲端資料來源, 您可以使用 UDR, 將輸出流量從 Azure SSIS IR 路由傳送至 NVA, 或使用靜態公用 IP 位址, 將連至 Azure 防火牆。 您可以將 NVA 或 Azure 防火牆的公用 IP 位址新增至允許清單。
    - 如果先前的答案不符合您的需求, 請考慮將自我裝載[IR 設定為 AZURE SSIS IR 的 proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis), 以提供資料來源存取。 接著, 您可以將裝載自我裝載 IR 之電腦的 IP 位址新增至允許清單, 而不是將 Azure SSIS IR 加入虛擬網路。

## <a name="azure-portal-data-factory-ui"></a>Azure 入口網站 (Data Factory UI)
本節說明如何使用 Azure 入口網站和 Data Factory UI, 將現有的 Azure SSIS IR 加入虛擬網路 (傳統或 Azure Resource Manager)。 

將您的 Azure SSIS IR 加入虛擬網路之前, 您必須適當地設定虛擬網路。 請遵循適用于您的虛擬網路類型 (傳統或 Azure Resource Manager) 一節中的步驟。 然後依照第三節中的步驟, 將您的 Azure SSIS IR 加入虛擬網路。 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>設定 Azure Resource Manager 的虛擬網路

請先使用入口網站來設定 Azure Resource Manager 虛擬網路, 然後再嘗試將 Azure SSIS IR 加入其中。

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory UI。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

1. 選取 [更多服務]。 篩選並選取 [虛擬網路]。 

1. 篩選並選取清單中的 [虛擬網路]。 

1. 在 [虛擬網路] 頁面上，選取 [屬性]。 

1. 選取 [資源識別碼] 的 [複製] 按鈕，將虛擬網路的資源識別碼複製到剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。 

1. 在左側功能表上, 選取 [**子網**]。 請確定可用的位址數目大於您的 Azure SSIS IR 中的節點。 

1. 確認已在具有虛擬網路的 Azure 訂用帳戶中註冊 Azure Batch 提供者。 或註冊 Azure Batch 提供者。 如果您的訂用帳戶中已經有 Azure Batch 帳戶, 您的訂閱會註冊 Azure Batch。 (若在 Data Factory 入口網站中建立 Azure-SSIS IR，便會自動註冊 Azure Batch 提供者)。 

   a. 在 [Azure 入口網站] 的左側功能表上, 選取 [**訂閱**]。 

   b. 選取您的訂用帳戶。 

   c. 在左側選取 [**資源提供者**], 並確認 [ **Microsoft Batch** ] 是已註冊的提供者。 

   ![確認「已註冊」狀態](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果您在清單中未看到 **Microsoft.Batch**，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。 

### <a name="configure-a-classic-virtual-network"></a>設定傳統虛擬網路
請先使用入口網站來設定傳統虛擬網路, 再嘗試將 Azure SSIS IR 加入其中。 

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory UI。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

1. 選取 [更多服務]。 篩選並選取 [虛擬網路 (傳統)]。 

1. 篩選並選取清單中的 [虛擬網路]。 

1. 在 [虛擬網路 (傳統)] 頁面上，選取 [屬性]。 

   ![傳統虛擬網路資源識別碼](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. 選取 [資源識別碼] 的 [複製] 按鈕，將傳統網路的資源識別碼複製到剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。 

1. 在左側功能表上, 選取 [**子網**]。 請確定可用的位址數目大於您的 Azure SSIS IR 中的節點。 

   ![虛擬網路中的可用位址數目](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. 將 **MicrosoftAzureBatch** 加入虛擬網路的**傳統虛擬機器參與者**角色。 

    a. 在左側功能表上, 選取 [**存取控制 (IAM)** ], 然後選取 [**角色指派**] 索引標籤。 

    ![[存取控制] 和 [新增] 按鈕](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. 選取 [新增角色指派]。

    c. 在 [**新增角色指派**] 頁面上, 針對 [**角色**] 選取 [**傳統虛擬機器參與者**]。 在 [**選取**] 方塊中, 貼上 [ **ddbf3205-c6bd-46ae-8127-60eb93363864**], 然後從搜尋結果清單中選取 [ **Microsoft Azure Batch** ]。 

    ![[新增角色指派] 頁面上的搜尋結果](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. 選取 [**儲存**] 以儲存設定並關閉頁面。 

    ![儲存存取設定](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. 確認您在參與者清單中看到 **Microsoft Azure Batch**。 

    ![確認 Azure Batch 存取](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. 確認已在具有虛擬網路的 Azure 訂用帳戶中註冊 Azure Batch 提供者。 或註冊 Azure Batch 提供者。 如果您的訂用帳戶中已經有 Azure Batch 帳戶, 您的訂閱會註冊 Azure Batch。 (若在 Data Factory 入口網站中建立 Azure-SSIS IR，便會自動註冊 Azure Batch 提供者)。 

   a. 在 [Azure 入口網站] 的左側功能表上, 選取 [**訂閱**]。 

   b. 選取您的訂用帳戶。 

   c. 在左側選取 [**資源提供者**], 並確認 [ **Microsoft Batch** ] 是已註冊的提供者。 

   ![確認「已註冊」狀態](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果您在清單中未看到 **Microsoft.Batch**，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>將 Azure-SSIS IR 加入虛擬網路

設定 Azure Resource Manager 虛擬網路或傳統虛擬網路之後, 您可以將 Azure SSIS IR 加入至虛擬網路:

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory UI。 

1. 在[Azure 入口網站](https://portal.azure.com)的左側功能表中, 選取 [ **Data**factory]。 如果您在功能表上看不到 [ **Data** factory], 請選取 [**更多服務**], 然後在 [**智慧 + 分析**] 區段中選取 [ **Data**factory]。 

   ![資料處理站清單](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 在清單中選取具有 Azure SSIS IR 的 data factory。 您會看到資料處理站的首頁。 選取 [編寫與部署] 圖格。 您會在個別的索引標籤上看到 Data Factory UI。 

   ![Data Factory 首頁](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 在 Data Factory UI 中，切換至 [編輯] 索引標籤，選取 [連線]，然後切換至 [整合執行階段] 索引標籤。 

   ![[整合執行階段] 索引標籤](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. 如果您的 Azure SSIS IR 正在執行, 請在 [**整合運行**時間] 清單的 [**動作**] 欄中, 選取您的 azure Ssis IR 的 [**停止**] 按鈕。 您無法編輯 IR, 直到您將它停止為止。 

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 在 [**整合運行**時間] 清單的 [**動作**] 欄中, 選取您的 Azure SSIS IR 的 [**編輯**] 按鈕。 

   ![編輯整合執行階段](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 在 [ **Integration Runtime 安裝**] 面板上, 選取 [**下一步]** 按鈕, 以前進到 [**一般設定**] 和 [ **SQL 設定**] 頁面。 

1. 在 [**高級設定**] 頁面上: 

   a. 選取 [**選取 VNet**] 旁的核取方塊。 

   b. 針對 [訂用帳戶]，選取您的 Azure 訂用帳戶。 在訂用帳戶底下, 您可以選取現有的虛擬網路。 
  
   c. 針對 [VNet 名稱]，選取您的虛擬網路。 

   d. 針對 [子網路名稱]，選取您在虛擬網路中的子網路。 

   e. 如果您也想要將自我裝載 IR 設定或管理為 Azure SSIS IR 的 proxy, 請選取 [**設定自我**裝載] 核取方塊。 如需詳細資訊, 請參閱[設定自我裝載 ir 作為 AZURE SSIS IR 的 proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。

   f. 選取 [ **VNet 驗證**] 按鈕。 如果驗證成功, 請選取 [**下一步]** 按鈕。 

   ![IR 設定的進階設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. 在 [**摘要**] 頁面上, 檢查您的 AZURE SSIS IR 的所有設定。 然後選取 [**更新**] 按鈕。

1. 在您的 Azure SSIS IR 的 [**動作**] 資料行中選取 [**啟動**] 按鈕, 以啟動您的 azure ssis ir。 啟動加入虛擬網路的 Azure SSIS IR 需要約20到30分鐘的時間。 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>設定虛擬網路
您必須先設定虛擬網路, 才能將 Azure SSIS IR 加入虛擬網路。 若要為您的 Azure SSIS IR 自動設定虛擬網路許可權和設定, 以加入虛擬網路, 請新增下列腳本:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>建立 Azure-SSIS IR 並將其加入虛擬網路
您可以建立 Azure-SSIS IR，同時將其加入虛擬網路。 如需完整的腳本和指示, 請參閱[建立 AZURE SSIS IR](create-azure-ssis-integration-runtime.md#azure-powershell)。

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>將現有的 Azure-SSIS IR 加入虛擬網路
[建立 AZURE SSIS ir](create-azure-ssis-integration-runtime.md)一文會說明如何建立 AZURE ssis ir, 並在相同的腳本中將其加入虛擬網路。 如果您已經有 Azure SSIS IR, 請遵循下列步驟將其聯結至虛擬網路: 
1. 停止 Azure-SSIS IR。 
1. 設定要加入虛擬網路的 Azure-SSIS IR。 
1. 啟動 Azure-SSIS IR。 

### <a name="define-the-variables"></a>定義變數
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>停止 Azure-SSIS IR
您必須先停止 Azure SSIS IR, 才能將它加入虛擬網路。 此命令會釋出其所有節點並停止計費：

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>進行虛擬網路設定讓 Azure-SSIS IR 可以加入

若要設定 Azure SSIS 將加入之虛擬網路的設定, 請使用此腳本: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
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
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>設定 Azure-SSIS IR
若要設定 Azure SSIS IR 以加入虛擬網路, 請執行`Set-AzDataFactoryV2IntegrationRuntime`下列命令: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>啟動 Azure-SSIS IR
若要啟動 Azure SSIS IR, 請執行下列命令: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

此命令約需要 20 到 30 分鐘才能完成。

## <a name="next-steps"></a>後續步驟
如需有關 Azure SSIS IR 的詳細資訊, 請參閱下列文章: 
- [AZURE SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供有關 IRs 的一般概念性資訊, 包括 Azure SSIS IR。 
- [教學課程：將 SSIS 套件部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本教學課程提供逐步指示, 說明如何建立您的 Azure SSIS IR。 它會使用 Azure SQL Database 來裝載 SSIS 目錄。 
- [建立 AZURE SSIS IR](create-azure-ssis-integration-runtime.md)。 本文會針對教學課程進行擴充。 其中提供有關在虛擬網路中 Azure SQL Database 使用虛擬網路服務端點或受控實例來裝載 SSIS 目錄的指示。 說明如何將您的 Azure SSIS IR 加入虛擬網路。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文說明如何取得您的 Azure SSIS IR 的相關資訊。 它會提供所傳回信息的狀態原因。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文說明如何停止、啟動或刪除您的 Azure SSIS IR。 此外也會說明如何藉由新增節點來相應放大 Azure-SSIS IR。
