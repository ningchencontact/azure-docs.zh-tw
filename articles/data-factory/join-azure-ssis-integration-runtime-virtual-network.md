---
title: 將 Azure-SSIS 整合執行階段加入虛擬網路 | Microsoft Docs
description: 了解如何將 Azure-SSIS 整合執行階段加入 Azure 虛擬網路。
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
ms.openlocfilehash: 3a1e272fa332c0bf0ee4e5ececa3edd83aec1d46
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543158"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>將 Azure-SSIS 整合執行階段加入虛擬網路
使用 Azure Data Factory (ADF) 中的 SQL Server Integration Services (SSIS) 時, 您應該在下列案例中, 將您的 Azure SSIS Integration Runtime (IR) 加入 Azure 虛擬網路: 

- 您想要從 Azure SSIS IR 上執行的 SSIS 套件連接到內部部署資料存放區, 而不需設定/管理自我裝載 IR 作為 proxy。 

- 您想要從 Azure SSIS IR 上執行的 SSIS 套件, 連接至虛擬網路服務端點所支援的 azure 服務資源。

- 您在虛擬網路中使用虛擬網路服務端點/受控執行個體, 在 Azure SQL Database 中裝載 SSIS 目錄資料庫 (SSISDB)。 

ADF 可讓您將 Azure SSIS IR 加入透過傳統部署模型或 Azure Resource Manager 部署模型所建立的虛擬網路。 

> [!IMPORTANT]
> 傳統虛擬網路目前已淘汰，請改為使用 Azure Resource Manager 虛擬網路。  如果您已經使用傳統虛擬網路，請盡快切換成使用 Azure Resource Manager 虛擬網路。

## <a name="access-to-on-premises-data-stores"></a>存取內部部署資料存放區
如果您的 SSIS 套件存取內部部署資料存放區, 您可以將 Azure SSIS IR 加入連線到內部部署網路的虛擬網路, 或為您的 Azure SSIS IR 設定/管理自我裝載 IR 作為 proxy, 請參閱[設定自我裝載 ir 作為適用于 Azure SSIS IR 的 proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)文章。 將您的 Azure SSIS IR 加入至虛擬網路時, 有幾個要注意的重要事項: 

- 如果目前沒有虛擬網路連線至您的內部部署網路，請先建立 Azure-SSIS 整合執行階段要加入的 [Azure Resource Manager 虛擬網路](../virtual-network/quick-create-portal.md#create-a-virtual-network)或[傳統虛擬網路](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然後，設定從該虛擬網路到內部部署網路的站對站 [VPN 閘道連線](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)或 [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 連線。 

- 如果在與 Azure-SSIS 整合執行階段相同的位置中有現有的 Azure Resource Manager 或傳統虛擬網路連線至您的內部部署網路，您可以將整合執行階段加入該虛擬網路。 

- 如果在與 Azure-SSIS 整合執行階段不同的位置中有現有的傳統虛擬網路連線至您的內部部署網路，您可以先建立 Azure-SSIS IR 要加入的[傳統虛擬網路](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然後，設定[傳統對傳統虛擬網路](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md)連線。 或者，您可以建立 [Azure Resource Manager 虛擬網路](../virtual-network/quick-create-portal.md#create-a-virtual-network)，讓您的 Azure-SSIS 整合執行階段加入。 然後，設定[傳統對 Azure Resource Manager 虛擬網路](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)連線。 
 
- 如果在與 Azure-SSIS IR 不同的位置中有現有的 Azure Resource Manager 虛擬網路連線至您的內部部署網路，您可以先建立 Azure-SSIS IR 要加入的 [Azure Resource Manager 虛擬網路](../virtual-network/quick-create-portal.md##create-a-virtual-network)。 然後，設定 Azure Resource Manager 對 Azure Resource Manager 虛擬網路連線。 或者，您可以建立[傳統虛擬網路](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)，讓您的 Azure-SSIS IR 加入。 然後，設定[傳統對 Azure Resource Manager 虛擬網路](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)連線。 

## <a name="access-to-azure-services-with-virtual-network-service-endpoints"></a>存取具有虛擬網路服務端點的 azure 服務
如果您的 SSIS 套件存取[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)所支援的 azure 服務資源, 而且您想要將這些資源保護到 AZURE ssis ir, 您可以將您的 AZURE ssis ir 加入使用虛擬網路設定的虛擬網路子網服務端點, 並同時將虛擬網路規則新增至 azure 服務資源, 以允許來自相同子網的存取。

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance"></a>將 SSIS 目錄資料庫裝載於具有虛擬網路服務端點/受控執行個體的 Azure SQL Database 中
如果您使用虛擬網路服務在 Azure SQL Database 中裝載您的 SSIS 目錄，請確定將您的 Azure-SSIS IR 加入到相同的虛擬網路和子網路。

如果您將 Azure-SSIS IR 加入至受控執行個體所在的虛擬網路上，請確定 Azure-SSIS IR 和受控執行個體分屬不同的子網路。 如果您的 Azure-SSIS IR 並不是加入至受控執行個體所在的虛擬網路，我們建議虛擬網路對等互連 (僅限相同的區域) 或虛擬網路對虛擬網路連線。 請參閱[將您的應用程式連線到 Azure SQL Database 受控執行個體](../sql-database/sql-database-managed-instance-connect-app.md)。

在此情況下，只能透過 Azure Resource Manager 部署模型來部署虛擬網路。

下列各節提供更多詳細資料。 

## <a name="requirements-for-virtual-network-configuration"></a>虛擬網路設定的需求
-   在裝載 Azure-SSIS IR 的虛擬網路子網路訂用帳戶之下，確認 `Microsoft.Batch` 是已註冊的提供者。 若您使用傳統虛擬網路，也請為該虛擬網路將 `MicrosoftAzureBatch` 加入「傳統虛擬機器參與者」角色。 

-   請確定您擁有必要權限。 請參閱[必要權限](#perms)。

-   選取要裝載 Azure-SSIS IR 的適當子網路。 請參閱[選取子網路](#subnet)。 

-   若在虛擬網路上使用自己的網域名稱服務 (DNS) 伺服器，請參閱[網域名稱服務伺服器](#dns_server)。 

-   若在子網路上使用「網路安全性群組」(NSG)，請參閱[網路安全性群組](#nsg) 

-   若使用 Azure Express Route 或設定使用者定義的路由 (UDR)，請參閱[使用 Azure ExpressRoute 或使用者定義的路由](#route)。 

-   請確認虛擬網路的資源群組可以建立及刪除特定的 Azure 網路資源。 請參閱[資源群組的需求](#resource-group)。 

-   如果您依照[AZURE SSIS ir 的自訂設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)一文所述自訂您的 AZURE ssis ir, 您的 AZURE ssis ir 節點將會從預先定義的 172.16.0.0-172.31.255.255 範圍配置私人 ip 位址, 因此請確定私人 ip虛擬/內部部署網路的位址範圍不會與此範圍衝突。

下圖顯示 Azure-SSIS IR 所需的連線：

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>必要權限

建立 Azure-SSIS Integration Runtime 的使用者必須具有下列權限：

- 如果您正要將 SSIS IR 加入 Azure Resource Manager 虛擬網路，您有兩個選項：

  - 使用內建的「網路參與者」角色。 此角色隨附 _microsoft.network /\*_  權限，它的權限範圍大於必要的範圍。

  - 建立包含只有必要 _Microsoft.Network/virtualNetworks/\*/join/action_ 權限的自訂角色。 

- 如果您要將 SSIS IR 加入傳統虛擬網路中，我們建議您使用內建的「傳統虛擬機器參與者」角色。 否則，您必須定義自訂角色，以包含可加入虛擬網路的權限。

### <a name="subnet"></a> 選取子網路
-   請勿選取 GatewaySubnet 來部署 Azure-SSIS Integration Runtime，因為它是虛擬網路閘道所專用。 

-   確定您選取的子網路有足夠的可用位址空間，以供 Azure-SSIS IR 使用。 在可用的 IP 位址中保留至少 2 * IR 節點數目。 Azure 會在每個子網路中保留一些 IP 位址，但這些位址無法使用。 子網路的第一個和最後一個 IP 位址會保留給相容的通訊協定，以及用於 Azure 服務的額外 3 個位址。 如需詳細資訊，請參閱[在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   請勿使用由其他 Azure 服務 (例如 SQL Database 受控執行個體、App Service 等) 所獨佔的子網路。 

### <a name="dns_server"></a> 網域名稱服務伺服器 
如果您需要在您的 Azure SSIS 整合執行時間所加入的虛擬網路中使用自己的功能變數名稱服務 (DNS) 伺服器, 請確定它可以解析全域 Azure 主機名稱 (例如, Azure 儲存體 blob 名稱`<your storage account>.blob.core.windows.net`)。 

以下為建議步驟： 

-   設定自訂 DNS 將要求轉送至 Azure DNS。 您可以在自己的 DNS 伺服器上，將無法解析的 DNS 記錄轉送至 Azure 遞迴解析程式的 IP 位址 (168.63.129.16)。 

-   將虛擬網路的主要 DNS 設為自訂 DNS，次要 DNS 設為 Azure DNS。 將 Azure 遞迴解析程式的 IP 位址 (168.63.129.16) 註冊為次要 DNS 伺服器，以便您自己的 DNS 伺服器無法使用時可備援。 

如需詳細資訊，請參閱[使用自有 DNS 伺服器的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。 

### <a name="nsg"></a> 網路安全性群組
如果您需要在 Azure-SSIS 整合執行階段所使用的子網路上實作網路安全性群組 (NSG)，請允許通過下列連接埠的輸入/輸出流量： 

| Direction | 傳輸通訊協定 | Source | 來源連接埠範圍 | 目的地 | 目的地連接埠範圍 | 註解 |
|---|---|---|---|---|---|---|
| 傳入 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877 (如果您將 IR 加入 Azure Resource Manager 虛擬網路) <br/><br/>10100、20100、30100 (如果您將 IR 加入傳統虛擬網路)| Data Factory 服務會使用這些連接埠與虛擬網路中的 Azure SSIS 整合執行階段節點進行通訊。 <br/><br/> 無論您是否有建立子網路層級的 NSG，Data Factory 一律會在連結至 Azure-SSIS IR 主控虛擬機器的網路介面卡 (NIC) 的層級上設定 NSG。 該 NIC 層級的 NSG 僅允許來自指定連接埠上 Data Factory IP 位址的輸入流量。 即使您在子網路層級上對網際網路流量開啟這些連接埠，只要流量不是來自 Data Factory 的 IP 位址，就會在 NIC 層級上遭到封鎖。 |
| 傳出 | TCP | VirtualNetwork | * | AzureCloud | 443 | 虛擬網路中的 Azure-SSIS 整合執行階段節點會使用此連接埠來存取 Azure 服務，例如 Azure 儲存體和 Azure 事件中樞。 |
| 傳出 | TCP | VirtualNetwork | * | 網際網路 | 80 | 虛擬網路中的 Azure-SSIS 整合執行階段節點會使用此連接埠從網際網路下載憑證撤銷清單。 |
| 傳出 | TCP | VirtualNetwork | * | Sql | 1433、11000-11999 | 虛擬網路中的 Azure-SSIS 整合執行階段節點會使用這些連接埠來存取 Azure SQL Database 伺服器所裝載的 SSISDB。 如果您的 Azure SQL Database 伺服器連線原則設定為 [ **Proxy** ] 而不是 [重新**導向**], 則只需要端口1433。 此輸出安全性規則不適用於您的受控執行個體在虛擬網路中所裝載的 SSISDB。 |
||||||||

### <a name="route"></a> 使用 Azure ExpressRoute 或使用者定義的路由
當您將[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)線路連線至虛擬網路基礎結構, 以將內部部署網路擴充至 Azure 時, 常見的設定是使用強制通道 (通告 BGP 路由, 0.0.0.0/0 到虛擬網路), 這會強制從虛擬網路流向內部部署網路應用裝置的輸出網際網路流量, 以進行檢查和記錄。 
 
或者, 您也可以定義[使用者定義的路由 (udr)](../virtual-network/virtual-networks-udr-overview.md) , 以強制將來自裝載 AZURE SSIS IR 之子網的輸出網際網路流量, 裝載至另一個子網, 以將虛擬網路設備 (NVA) 作為防火牆或 Azure 防火牆來進行檢查和記錄。
 
在這兩種情況下, 流量路由會將必要的輸入連線中斷, 從相依的 Azure Data Factory 服務 (特別是 Azure Batch 管理服務) 到虛擬網路中的 Azure SSIS IR。 
 
解決方案是在包含 Azure SSIS IR 的子網上, 定義一 (或多個) 使用者定義的路由 (Udr)。 

- 您可以在裝載 Azure ExpressRoute 案例中 Azure SSIS IR 的子網上, 將 0.0.0.0/0 路由的下一個躍點類型設為**網際網路**, 或從下一個躍點類型修改現有的 0.0.0.0/0 路由, 做為 NVA 中的**虛擬應用裝置**對**網際網路**案例.

![新增路由](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)
- 如果您擔心無法檢查該子網的輸出網際網路流量。 您可以定義特定 Udr, 只將 Azure Batch 管理服務和 Azure SSIS IR 之間的流量路由傳送到下一個躍點類型為**網際網路**。
例如 如果您的 Azure SSIS IR `UK South`位於, 您將需要`BatchNodeManagement.UKSouth`從服務標籤[ip 範圍下載連結](https://www.microsoft.com/en-us/download/details.aspx?id=56519)或透過服務標籤[探索 API](https://aka.ms/discoveryapi)取得服務標籤的 ip 範圍清單。 然後將下方的相關 ip 範圍路由 Udr 套用至下一個躍點類型為**網際網路**。

![AzureBatch UDR 設定](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)
> [!NOTE]
> 這種方法有額外的維護成本, 您需要定期檢查 ip 範圍, 並將新的 ip 範圍新增至您的 UDR, 以避免中斷 Azure SSIS IR。 當服務標籤中出現新的 IP 時, 將需要另一個月, 新的 IP 才會生效。 因此, 建議您每月檢查 ip 範圍。 

### <a name="resource-group"></a> 資源群組的需求
-   Azure-SSIS IR 需要在與虛擬網路相同的資源群組下，建立特定的網路資源。 這些資源包括下列各項：
    -   具有名稱 *\<Guid >-azurebatch-cloudserviceloadbalancer*的 Azure 負載平衡器。
    -   Azure 公用 IP 位址, 其名稱 *\<為 Guid >-azurebatch-cloudservicepublicip*。
    -   網路工作安全性群組, 其名稱 *\<為 Guid >-azurebatch-cloudservicenetworksecuritygroup*。 

    當 ir 停止時, 會建立 IR 啟動和刪除這些資源。 請不要在其他資源中重複使用它們, 否則會封鎖紅外線停止。 

-   請確認在虛擬網路所屬的資源群組或訂用帳戶上，沒有任何鎖定的資源。 如果您設定的是唯讀鎖定或刪除鎖定, 啟動和停止 IR 可能會失敗或停止回應。 

-   請確認沒有任何 Azure 原則會禁止在虛擬網路所屬資源群組或訂用帳戶之下建立下列資源： 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a>常見問題集

- 如何保護在 Azure SSIS IR 上針對輸入連線公開的公用 ip 位址？ 可以移除公用 ip 位址嗎？
 
    現在, 當 Azure SSIS IR 加入 VNet 時, 將會自動建立公用 ip 位址。 我們有 NIC 層級的 NSG, 僅允許 Azure Batch 管理服務連線至 Azure SSIS IR, 而且您也可以針對輸入保護指定子網層級的 NSG。

    如果您不想公開公用 ip 位址, 您可以考慮[將自我裝載 IR 設定為 AZURE SSIS ir 的 proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) , 而不是 VNet (如果適用于您的案例)。
 
- 是否有 Azure SSIS IR 的靜態 ip 位址可以放入允許的防火牆清單以存取資料來源？
 
    - 如果您的資料來源是內部部署, 則在將虛擬網路連線到內部部署網路, 並將您的 Azure SSIS IR 加入該虛擬網路子網之後, 您可以將該子網的 ip 範圍放入允許清單。
    - 如果您的資料來源是虛擬網路服務端點所支援的 azure 服務, 您可以在虛擬網路上設定虛擬網路服務點, 並將您的 Azure SSIS IR 加入該虛擬網路子網, 然後您就應該能夠使用虛擬網路規則的 azure 服務, 而不是允許存取的 ip 範圍。
    - 如果您的資料來源是其他雲端資料來源, 您可以使用 UDR, 將來自 Azure SSIS IR 的輸出流量路由傳送至 NVA 或具有靜態公用 ip 位址的 Azure 防火牆, 因此您可以將該公用 ip 位址的 NVA 或 Azure 防火牆放入允許清單中。
    - 如果上述全都不符合您的需求, 您可以透過[設定自我裝載 ir 作為 AZURE SSIS IR 的 proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)來評估您的資料來源存取是否可以完成, 然後您可以將裝載自我裝載 ir 的電腦 ip 位址放入允許清單, 而不是將 Azure SSIS IR 加入 VNet。

## <a name="azure-portal-data-factory-ui"></a>Azure 入口網站 (Data Factory UI)
本節說明如何使用 Azure 入口網站及 Data Factory UI，將現有的 Azure SSIS 執行階段加入虛擬網路 (傳統或 Azure Resource Manager)。 首先，在將 Azure SSIS IR 加入虛擬網路之前，必須先適當設定虛擬網路。 根據您的虛擬網路類型 (傳統或 Azure Resource Manager)，執行下列兩節的其中一節。 然後，繼續執行第三節，將您的 Azure SSIS IR 加入虛擬網路。 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>使用入口網站設定 Azure Resource Manager 虛擬網路
您必須先設定虛擬網路，才能將 Azure-SSIS IR 加入虛擬網路。 

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory UI。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

1. 選取 [更多服務]。 篩選並選取 [虛擬網路]。 

1. 篩選並選取清單中的 [虛擬網路]。 

1. 在 [虛擬網路] 頁面上，選取 [屬性]。 

1. 選取 [資源識別碼] 的 [複製] 按鈕，將虛擬網路的資源識別碼複製到剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。 

1. 選取左側功能表上的 [子網路]。 確定 [可用的位址] 數目大於 Azure-SSIS 整合執行階段中的節點數。 

1. 確認已在具有虛擬網路的 Azure 訂用帳戶中註冊 Azure Batch 提供者。 或者，註冊 Azure Batch 提供者。 如果您的訂用帳戶中已經有 Azure Batch 帳戶，則會註冊 Azure Batch 的訂用帳戶。 (若在 Data Factory 入口網站中建立 Azure-SSIS IR，便會自動註冊 Azure Batch 提供者)。 

   a. 在 Azure 入口網站中，選取左側功能表上的 [訂用帳戶]。 

   b. 選取您的訂用帳戶。 

   c. 選取左側的 [資源提供者]，並確認 **Microsoft.Batch** 是已註冊的提供者。 

   ![確認「已註冊」狀態](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果您在清單中未看到 **Microsoft.Batch**，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>使用入口網站設定傳統虛擬網路
您必須先設定虛擬網路，才能將 Azure-SSIS IR 加入虛擬網路。 

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory UI。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

1. 選取 [更多服務]。 篩選並選取 [虛擬網路 (傳統)]。 

1. 篩選並選取清單中的 [虛擬網路]。 

1. 在 [虛擬網路 (傳統)] 頁面上，選取 [屬性]。 

   ![傳統虛擬網路資源識別碼](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. 選取 [資源識別碼] 的 [複製] 按鈕，將傳統網路的資源識別碼複製到剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。 

1. 選取左側功能表上的 [子網路]。 確定 [可用的位址] 數目大於 Azure-SSIS 整合執行階段中的節點數。 

   ![虛擬網路中的可用位址數目](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. 將 **MicrosoftAzureBatch** 加入虛擬網路的**傳統虛擬機器參與者**角色。 

    a. 選取左側功能表上的 [存取控制 (IAM)]，然後選取 [角色指派] 索引標籤。 

    ![[存取控制] 和 [新增] 按鈕](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. 選取 [新增角色指派]。

    c. 在 [新增角色指派] 頁面上，針對 [角色] 選取 [傳統虛擬機器參與者]。 在 [選取] 方塊中複製 **ddbf3205-c6bd-46ae-8127-60eb93363864**，然後從搜尋結果清單中選取 [Microsoft Azure Batch]。 

    ![[新增角色指派] 頁面上的搜尋結果](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. 選取 [儲存] 以儲存設定並關閉頁面。 

    ![儲存存取設定](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. 確認您在參與者清單中看到 **Microsoft Azure Batch**。 

    ![確認 Azure Batch 存取](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. 確認已在具有虛擬網路的 Azure 訂用帳戶中註冊 Azure Batch 提供者。 或者，註冊 Azure Batch 提供者。 如果您的訂用帳戶中已經有 Azure Batch 帳戶，則會註冊 Azure Batch 的訂用帳戶。 (若在 Data Factory 入口網站中建立 Azure-SSIS IR，便會自動註冊 Azure Batch 提供者)。 

   a. 在 Azure 入口網站中，選取左側功能表上的 [訂用帳戶]。 

   b. 選取您的訂用帳戶。 

   c. 選取左側的 [資源提供者]，並確認 **Microsoft.Batch** 是已註冊的提供者。 

   ![確認「已註冊」狀態](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果您在清單中未看到 **Microsoft.Batch**，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>將 Azure-SSIS IR 加入虛擬網路
1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory UI。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取左側功能表中的 [資料處理站]。 如果在功能表上未看到 [資料處理站]，請選取 [更多服務]，然後選取 [智慧 + 分析] 區段中的 [資料處理站]。 

   ![資料處理站清單](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 在清單中選取具有 Azure-SSIS 整合執行階段的資料處理站。 您會看到資料處理站的首頁。 選取 [編寫與部署] 圖格。 您會在個別的索引標籤上看到 Data Factory UI。 

   ![Data Factory 首頁](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 在 Data Factory UI 中，切換至 [編輯] 索引標籤，選取 [連線]，然後切換至 [整合執行階段] 索引標籤。 

   ![[整合執行階段] 索引標籤](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. 如果您的 Azure-SSIS 整合執行階段正在執行，在整合執行階段清單中，請在 [動作] 資料行中針對您的 Azure SSIS IR 選取 [停止] 按鈕。 您必須停止整合執行階段才能進行編輯。 

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 在整合執行階段清單中，在 [動作] 資料行中針對您的 Azure-SSIS IR 選取 [編輯] 按鈕。 

   ![編輯整合執行階段](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 在 [ **Integration Runtime 安裝**] 面板上, 按一下 [**下一步]** 按鈕, 以前進到 [**一般設定**] 和 [ **SQL 設定**] 頁面。 

1. 在 [進階設定] 頁面上，執行下列動作： 

   a. 勾選 [**選取 VNet ...** ] 核取方塊。 

   b. 針對 [**訂**用帳戶], 選取您的 Azure 訂用帳戶, 您可以在此選取現有的虛擬網路。 
  
   c. 針對 [VNet 名稱]，選取您的虛擬網路。 

   d. 針對 [子網路名稱]，選取您在虛擬網路中的子網路。 

   e. 如果您也想要針對您的 Azure SSIS IR 設定/管理自我裝載的 IR 作為 proxy, 請勾選 [**設定自我裝載 ...** ] 核取方塊, 並參閱[將自我裝載 Ir 設定為 azure ssis ir 的 proxy 一](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)文。

   f. 按一下 [ **VNet 驗證**] 按鈕, 如果成功, 請按 [**下一步]** 按鈕。 

   ![IR 設定的進階設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. 在 [**摘要**] 頁面上, 檢查您的 AZURE SSIS IR 的所有設定, 然後按一下 [**更新**] 按鈕。

1. 現在, 您可以在 Azure SSIS IR 的 [**動作**] 資料行中, 按一下 [**開始**] 按鈕來啟動您的 azure ssis ir。 啟動您的 Azure SSIS IR 來加入虛擬網路需要大約20到30分鐘的時間。 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>設定虛擬網路
您必須先設定虛擬網路，才能將 Azure-SSIS IR 加入虛擬網路。 若要自動設定虛擬網路權限/設定，讓您的 Azure-SSIS 整合執行階段加入虛擬網路，請新增下列指令碼：

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
您可以建立 Azure-SSIS IR，同時將其加入虛擬網路。 在完整的指令碼和指示，請參閱[建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md#azure-powershell)。

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>將現有的 Azure-SSIS IR 加入虛擬網路
[建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)一文中的指令碼會說明如何以相同的指令碼建立 Azure-SSIS IR 並將其加入虛擬網路。 如果您有現有的 Azure-SSIS IR，請執行下列步驟將其加入虛擬網路： 
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
必須先停止 Azure-SSIS 整合執行階段，才能將其加入虛擬網路。 此命令會釋出其所有節點並停止計費：

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>進行虛擬網路設定讓 Azure-SSIS IR 可以加入
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
若要設定要加入虛擬網路的 Azure-SSIS 整合執行階段，請執行 `Set-AzDataFactoryV2IntegrationRuntime` 命令： 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>啟動 Azure-SSIS IR
若要啟動 Azure-SSIS 整合執行階段，請執行下列命令： 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

此命令約需要 20 到 30 分鐘才能完成。

## <a name="next-steps"></a>後續步驟
如需 Azure SSIS 整合執行時間的詳細資訊, 請參閱下列主題: 
- [Azure-SSIS 整合執行階段](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供整合執行時間一般的概念資訊, 包括 Azure SSIS IR。 
- [教學課程：將 SSIS 套件部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本教學課程提供逐步指示, 說明如何建立您的 Azure SSIS IR。 它會使用 Azure SQL Database 來裝載 SSIS 目錄。 
- [建立 Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md)。 這篇文章會詳述教學課程, 並提供使用虛擬網路中虛擬網路服務端點/受控執行個體的 Azure SQL Database 來裝載 SSIS 目錄, 並將您的 Azure SSIS IR 加入虛擬網路的指示。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文說明如何取出 Azure SSIS IR 的相關資訊, 並在傳回的資訊中提供狀態原因。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文說明如何停止、啟動或刪除您的 Azure SSIS IR。 此外也會說明如何藉由新增節點來相應放大 Azure-SSIS IR。
