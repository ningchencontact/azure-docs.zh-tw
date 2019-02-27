---
title: Azure 防火牆服務標籤概觀
description: 本文會概略說明 Azure 防火牆服務標籤。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/21/2019
ms.author: victorh
ms.openlocfilehash: 9ecc5d3779ac6632f4a5c05914cbb0e756c79e91
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56458215"
---
# <a name="azure-firewall-service-tags"></a>Azure 防火牆服務標籤

服務標籤表示一組 IP 位址前置詞，有助於降低建立安全性規則的複雜性。 您無法建立自己的服務標籤，或指定標籤中包含哪些 IP 位址。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

防火牆服務標籤可以用於網路規則目的地的欄位。 可以使用這些標籤來取代特定的 IP 位址。

> [!NOTE]
> 服務標籤會逐步新增到各個區域，不久後所有區域均可使用。

## <a name="supported-service-tags"></a>支援的服務標籤

下列服務標籤可用於 Azure 防火牆網路規則：

* **AzureCloud** (僅限 Resource Manager)：此標記代表包含所有[資料中心公用 IP 位址](https://www.microsoft.com/download/details.aspx?id=41653)的 Azure IP 位址空間。 如果您指定 *AzureCloud* 作為值，就會允許或拒絕 Azure 公用 IP 位址的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 AzureCloud，您可以指定區域。 例如，如果您只想要在美國東部區域允許存取 Azure AzureCloud，您可以指定 *AzureCloud.EastUS* 作為服務標記。 
* **AzureTrafficManager** (僅限 Resource Manager)：此標記代表 Azure 流量管理員探查 IP 位址的 IP 位址空間。 如需流量管理員探查 IP 位址的詳細資訊，請參閱 [Azure 流量管理員常見問題集](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)。 
* **Storage** (僅限 Resource Manager)：此標記代表 Azure 儲存體服務的 IP 位址空間。 如果您指定 *Storage* 值，則會允許或拒絕儲存體的流量。 如果您只想要允許存取特定[地區](https://azure.microsoft.com/regions)中的儲存體，您可以指定地區。 例如，如果您只想要允許存取美國東部地區的 Azure 儲存體，您可以指定 *Storage.EastUS* 作為服務標籤。 標籤代表服務，但不代表服務的特定執行個體。 例如，標籤代表 Azure 儲存體服務，但不代表特定的 Azure 儲存體帳戶。
* **Sql** (僅限 Resource Manager)：此標記代表 Azure SQL Database 和 Azure SQL 資料倉儲服務的位址前置詞。 如果您指定 Sql 作為值，就會允許或拒絕 Sql 的流量。 如果您只需要允許存取特定[地區](https://azure.microsoft.com/regions)中的 Sql，可以指定地區。 例如，如果您只想要允許存取美國東部地區的 Azure SQL Database，您可以指定 *Sql.EastUS* 作為服務標籤。 標籤代表服務，但不代表服務的特定執行個體。 例如，標籤代表 SQL Database 或伺服器服務，但不代表特定的 Azure SQL Database。
* **AzureCosmosDB** (僅限 Resource Manager)：此標記代表 Azure Cosmos Database 服務的位址前置詞。 如果您指定 *AzureCosmosDB* 作為值，就會允許或拒絕 AzureCosmosDB 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 AzureCosmosDB，您可以用 AzureCosmosDB.[region name] 的格式指定區域。
* **AzureKeyVault** (僅限 Resource Manager)：此標記代表 Azure KeyVault 服務的位址前置詞。 如果您指定 *AzureKeyVault* 作為值，就會允許或拒絕 AzureKeyVault 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 AzureKeyVault，您可以用 AzureKeyVault.[region name] 的格式指定區域。
* **EventHub** (僅限 Resource Manager)：此標記代表 Azure EventHub 服務的位址前置詞。 如果您指定 EventHub 作為值，就會允許或拒絕 EventHub 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 EventHub，您可以用 EventHub.[region name] 的格式指定區域。 
* **ServiceBus** (僅限 Resource Manager)：此標記代表 Azure ServiceBus 服務的位址前置詞。 如果您指定 ServiceBus 作為值，就會允許或拒絕 ServiceBus 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 ServiceBus，您可以用 ServiceBus.[region name] 的格式指定區域。
* **MicrosoftContainerRegistry** (僅限 Resource Manager)：此標記代表 Microsoft Container Registry 服務的位址前置詞。 如果您指定 MicrosoftContainerRegistry 作為值，就會允許或拒絕 MicrosoftContainerRegistry 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 MicrosoftContainerRegistry，您可以用 MicrosoftContainerRegistry.[region name] 的格式指定區域。
* **AzureContainerRegistry** (僅限 Resource Manager)：此標記代表 Azure Container Registry 服務的位址前置詞。 如果您指定 AzureContainerRegistry 作為值，就會允許或拒絕 AzureContainerRegistry 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 AzureContainerRegistry，您可以用 AzureContainerRegistry.[region name] 的格式指定區域。 
* **AppService** (僅限 Resource Manager)：此標記代表 Azure AppService 服務的位址前置詞。 如果您指定 AppService 作為值，就會允許或拒絕 AppService 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 AppService，您可以用 AppService.[region name] 的格式指定區域。 
* **AppServiceManagement** (僅限 Resource Manager)：此標記代表 Azure AppService Management 服務的位址前置詞。 如果您指定 AppServiceManagement 作為值，就會允許或拒絕 AppServiceManagement 的流量。 
* **ApiManagement** (僅限 Resource Manager)：此標記代表 Azure API Management 服務的位址前置詞。 如果您指定 ApiManagement 作為值，就會允許或拒絕 ApiManagement 的流量。  
* **AzureConnectors** (僅限 Resource Manager)：此標記代表 Azure Connectors 服務的位址前置詞。 如果您指定 AzureConnectors 作為值，就會允許或拒絕 AzureConnectors 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 AzureConnectors，您可以用 AzureConnectors.[region name] 的格式指定區域。
* **AzureDataLake** (僅限 Resource Manager)：此標記代表 Azure Data Lake 服務的位址前置詞。 如果您指定 AzureDataLake 作為值，就會允許或拒絕 AzureDataLake 的流量。
* **AzureActiveDirectory** (僅限 Resource Manager)：此標記代表 AzureActiveDirectory 服務的位址前置詞。 如果您指定 AzureActiveDirectory 作為值，就會允許或拒絕 AzureActiveDirectory 的流量。
* **AzureMonitor** (僅限 Resource Manager)：此標記代表 AzureMonitor 服務的位址前置詞。 如果您指定 AzureMonitor 作為值，就會允許或拒絕 AzureMonitor 的流量。
* **ServiceFabric** (僅限 Resource Manager)：此標記代表 ServiceFabric 服務的位址前置詞。 如果您指定 ServiceFabric 作為值，就會允許或拒絕 ServiceFabric 的流量。
* **AzureMachineLearning** (僅限 Resource Manager)：此標記代表 AzureMachineLearning 服務的位址前置詞。 如果您指定 AzureMachineLearning 作為值，就會允許或拒絕 AzureMachineLearning 的流量。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 防火牆規則，請參閱 [Azure 防火牆規則處理邏輯](rule-processing.md)。