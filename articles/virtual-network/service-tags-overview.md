---
title: Azure 服務標記總覽
titlesuffix: Azure Virtual Network
description: 瞭解服務標記。 服務標籤有助於降低建立安全性規則的複雜性。
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 152b9f3974f24644e55bed68f5ed65faa90d7fe7
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851650"
---
# <a name="virtual-network-service-tags"></a>虛擬網路服務標籤 
<a name="network-service-tags"></a>

服務標記代表來自指定 Azure 服務的一組 IP 位址首碼。 它有助於將網路安全性規則頻繁更新的複雜度降到最低。 您可以使用服務標記來定義[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)或[Azure 防火牆](https://docs.microsoft.com/azure/firewall/service-tags)上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當 [*來源*] 或 [*目的地*] 欄位中指定服務標籤名稱（例如， **ApiManagement**），您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤所包含的位址前置詞，並隨著位址變更自動更新服務標記。 

## <a name="available-service-tags"></a>可用的服務標記
下表包含可用於[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)規則的所有服務標記。

這些資料行會指出標記是否為：

- 適用于涵蓋輸入或輸出流量的規則
- 支援[區域](https://azure.microsoft.com/regions)範圍 
- 可在[Azure 防火牆](https://docs.microsoft.com/azure/firewall/service-tags)規則中使用

根據預設，服務標記會反映整個雲端的範圍。  某些服務標籤也允許將對應的 IP 範圍限制為指定的區域，以提供更細微的控制。  例如，當服務標籤**儲存體**代表整個雲端的 Azure 儲存體時， **WestUS**會將其縮小為只有 WESTUS 區域的儲存體 IP 位址範圍。  下列每個服務標記的描述會指出它們是否支援這類區域範圍。  



| Tag | 目的 | 可以使用輸入或輸出嗎？ | 可以是地區嗎？ | 可以與 Azure 防火牆搭配使用嗎？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | APIM 專用部署的管理流量。 | 兩者 | 否 | 是 |
| **AppService**    | App Service 服務。 建議將此標記用於輸出安全性規則，以 WebApp 前端。 | 輸出 | 是 | 是 |
| **AppServiceManagement** | App Service 環境專用部署的管理流量。 | 兩者 | 否 | 是 |
| **AzureActiveDirectory** | Azure Active Directory 服務。 | 輸出 | 否 | 是 |
| **AzureActiveDirectoryDomainServices** | Azure Active Directory Domain Services 專用部署的管理流量。 | 兩者 | 否 | 是 |
| **AzureBackup** |Azure 備份服務。<br/><br/>*注意：* 此標記相依于**儲存體**和**AzureActiveDirectory**標記。 | 輸出 | 否 | 是 |
| **AzureCloud** | 所有[資料中心公用 IP 位址](https://www.microsoft.com/download/details.aspx?id=41653)。 | 輸出 | 是 | 是 |
| **AzureConnectors** | 適用于探查/後端連線的 Logic Apps 連接器。 | 輸入 | 是 | 是 |
| **AzureContainerRegistry** | Azure Container Registry 服務。 | 輸出 | 是 | 是 |
| **AzureCosmosDB** | Azure Cosmos 資料庫服務。 | 輸出 | 是 | 是 |
| **AzureDataLake** | Azure Data Lake 服務。 | 輸出 | 否 | 是 |
| **Hdinsightclustername>.azurehdinsight.net** | Azure HDInsight 服務。 | 輸入 | 是 | 否 |
| **AzureIoTHub** | Azure IoT 中樞服務。 | 輸出 | 否 | 否 |
| **AzureKeyVault** | Azure KeyVault 服務。<br/><br/>*注意：* 此標記與**AzureActiveDirectory**標記具有相依性。 | 輸出 | 是 | 是 |
| **AzureLoadBalancer** | Azure 的基礎結構負載平衡器。 此標記會轉譯成作為 Azure 健康情況探查來源的[主機虛擬 IP 位址](security-overview.md#azure-platform-considerations) (168.63.129.16)。 如果您未使用 Azure 負載平衡器，則可以覆寫此規則。 | 兩者 | 否 | 否 |
| **AzureMachineLearning** | Azure Machine Learning 服務。 | 輸出 | 否 | 是 |
| **AzureMonitor** | Log Analytics、App Insights、AzMon 和自訂計量（Gb 端點）。<br/><br/>*注意：* 若為 Log Analytics，此標記會相依于**儲存體**標記。 | 輸出 | 否 | 是 |
| **AzurePlatformDNS** | 基本基礎結構（預設） DNS 服務。<br/><br>您可以使用此標記來停用預設 DNS。 請小心使用此標記。 建議您閱讀[Azure 平臺考慮](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 使用此標記之前，建議您先進行測試。 | 輸出 | 否 | 否 |
| **AzurePlatformIMDS** | IMDS，這是基本的基礎結構服務。<br/><br/>您可以使用此標記來停用預設 IMDS。  請小心使用此標記。 建議您閱讀[Azure 平臺考慮](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 使用此標記之前，建議您先進行測試。 | 輸出 | 否 | 否 |
| **AzurePlatformLKM** | Windows 授權或金鑰管理服務。<br/><br/>您可以使用此標記來停用授權的預設值。 請小心使用此標記。  建議您閱讀[Azure 平臺考慮](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 使用此標記之前，建議您先進行測試。 | 輸出 | 否 | 否 |
| **AzureTrafficManaged** | Azure 流量管理員探查 IP 位址。<br/><br/>如需流量管理員探查 IP 位址的詳細資訊，請參閱 [Azure 流量管理員常見問題集](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)。 | 輸入 | 否 | 是 |  
| **BatchNodeManagement** | Azure Batch 專用部署的管理流量。 | 兩者 | 否 | 是 |
| **CognitiveServicesManagement** | 認知服務流量的位址範圍 | 輸出 | 否 | 否 |
| **Dynamics365ForMarketingEmail** | Dynamics 365 行銷電子郵件服務的位址範圍。 | 輸出 | 是 | 否 |
| **EventHub** | Azure EventHub 服務。 | 輸出 | 是 | 是 |
| **GatewayManager** | VPN/應用程式閘道專用部署的管理流量。 | 輸入 | 否 | 否 |
| **網際網路** | 位於虛擬網路外部且可由公用網際網路連線的 IP 位址空間。<br/><br/>此位址範圍也包括 [Azure 擁有的公用 IP 位址空間](https://www.microsoft.com/download/details.aspx?id=41653)。 | 兩者 | 否 | 否 |
| **MicrosoftContainerRegistry** | Microsoft Container Registry 服務。 | 輸出 | 是 | 是 |
| **ServiceBus** | 使用 Premium 服務層級的 Azure 服務匯流排服務。 | 輸出 | 是 | 是 |
| **ServiceFabric** | Service Fabric 服務。 | 輸出 | 否 | 否 |
| **Server** | Azure SQL Database、適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫和 Azure SQL 資料倉儲服務。<br/><br/>*注意：* 此標記代表服務，而不是服務的特定實例。 例如，標籤代表 SQL Database 或伺服器服務，但不代表特定的 Azure SQL Database。 | 輸出 | 是 | 是 |
| **SqlManagement** | 適用于 SQL 專用部署的管理流量。 | 兩者 | 否 | 是 |
| **儲存體** | Azure 儲存體服務。 <br/><br/>*注意：* 標記代表服務，而不是服務的特定實例。 例如，標籤代表 Azure 儲存體服務，但不代表特定的 Azure 儲存體帳戶。 | 輸出 | 是 | 是 |
| **VirtualNetwork** | 虛擬網路位址空間（針對虛擬網路定義的所有 IP 位址範圍）、所有已連線的內部部署位址空間、[對等互連](virtual-network-peering-overview.md)虛擬網路，或連線到[虛擬網路閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)的虛擬網路、[主機的虛擬 IP 位址](security-overview.md#azure-platform-considerations)，以及在[使用者定義的路由](virtual-networks-udr-overview.md)上使用的位址首碼。 請注意，此標記也可能包含預設路由。 | 兩者 | 否 | 否 |

>[!NOTE]
>在*傳統*（預先 Azure Resource Manager）環境中工作時，支援上述標記的一組選取。  這些會使用替代的拼寫：

| 傳統拼寫 | 對等 Resource Manager 標記 |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| 網際網路 | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 服務的服務標記代表所使用之特定雲端中的位址前置詞。 例如，對應至**Sql**標記值的基礎 IP 範圍在 azure 公用雲端和 azure 中國雲端之間會有所不同。

> [!NOTE]
> 如果您對服務 (例如 Azure 儲存體或 Azure SQL Database) 實作[虛擬網路服務端點](virtual-network-service-endpoints-overview.md)，Azure 會將[路由](virtual-networks-udr-overview.md#optional-default-routes)新增至服務的虛擬網路子網路。 路由中的位址前置詞為相同的位址前置詞，或 CIDR 範圍，以及對應的服務標籤。



## <a name="service-tags-in-on-premises"></a>內部部署中的服務標記  
您可以取得目前的服務標籤和範圍資訊，以納入內部部署防火牆設定的一部分。  這項資訊是對應到每個服務標籤之 IP 範圍的目前時間點清單。  您可以透過程式設計方式或透過 JSON 檔案下載來取得資訊，如下所示。

### <a name="service-tag-discovery-api-public-preview"></a>服務標記探索 API （公開預覽）
您可以使用 IP 位址範圍詳細資料，以程式設計方式取出服務標籤的目前清單：

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> 在公開預覽時，探索 API 可能會傳回與 JSON 下載（下方）不是最新的資訊。


### <a name="discover-service-tags-using-downloadable-json-files"></a>使用可下載的 JSON 檔案探索服務標記 
您可以下載 JSON 檔案，其中包含具有 IP 位址範圍詳細資料的目前服務標籤清單。 這些更新和每週發佈一次。  每個雲端的位置如下：

- [Azure 公用](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>先前已在[Azure 公用](https://www.microsoft.com/download/details.aspx?id=41653)、 [Azure 中國](https://www.microsoft.com/download/details.aspx?id=42064)和[azure 德國](https://www.microsoft.com/download/details.aspx?id=54770)的 XML 檔案中發佈這項資訊的子集。 這些 XML 下載將于2020年6月30日淘汰，而且在該日期之後將無法再使用。 如先前所述，請使用探索 API 或 JSON 檔案下載進行遷移。

### <a name="tips"></a>祕訣 
- 您可以透過 JSON 檔案中增加的*changeNumber*值，從一次發行到下一個發佈更新。 每個子區段（例如**WestUS**）都有自己的*changeNumber* ，會在發生變更時遞增。  當任何小節變更時，檔案*changeNumber*的最上層會遞增。
- 如需如何剖析服務標籤資訊的範例（例如，取得儲存體在 WestUS 中的所有位址範圍），請參閱[服務標記探索 API PowerShell](https://aka.ms/discoveryapi_powershell)檔。

## <a name="next-steps"></a>後續步驟
- 了解如何[建立網路安全性群組](tutorial-filter-network-traffic.md)。

