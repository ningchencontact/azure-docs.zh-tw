---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ce64047fd7490106790ea8bb1ad7963d82a87c24
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238448"
---
| Resource | 免費 | 共用 | 基本 | 標準 | 進階 (v2) | 隔離 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web、 行動或 API 應用程式](https://azure.microsoft.com/services/app-service/)每個[Azure App Service 方案](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |無限制<sup>2</sup> |無限制<sup>2</sup> |無限制<sup>2</sup> |無限制<sup>2</sup>|
| [App Service 計劃](../articles/app-service/overview-hosting-plans.md) |每個區域 10 個 |每個資源群組 10 個 |每個資源群組 100 個 |每個資源群組 100 個 |每個資源群組 100 個 |每個資源群組 100 個|
| 計算執行個體類型 |共用 |共用 |專用<sup>3</sup> |專用<sup>3</sup> |專用<sup>3</sup></p> |專用<sup>3</sup>|
| [相應放大](../articles/app-service/web-sites-scale.md)（最大執行個體） |1 個共用 |1 個共用 |3 個專用<sup>3</sup> |10 個專用<sup>3</sup> |20 個專用<sup>3</sup>|100 個專用<sup>4</sup>|
| 儲存體<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU 時間 （5 分鐘）<sup>6</sup> |3 分鐘 |3 分鐘 |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a>|
| CPU 時間 (天)<sup>6</sup> |60 Minuten |240 Minuten |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |
| 記憶體 (1 小時) |每個 App Service 方案 1024 MB |每個應用程式的 1,024 MB |N/A |N/A |N/A |N/A |
| 頻寬 |165 MB |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |
| 應用程式架構 |32 位元 |32 位元 |32 位元/64 位元 |32 位元/64 位元 |32 位元/64 位元 |32 位元/64 位元 |
| Web 通訊端，每個執行個體<sup>7</sup> |5 |35 |350 |無限 |無限 |無限制 |
| 並行 [偵錯工具連接數](../articles/app-service/troubleshoot-dotnet-visual-studio.md) (每個應用程式) |1 |1 |1 |5 |5 |5 |
| 每個訂用帳戶的 app Service 憑證<sup>10</sup>| 不支援 | 不支援 |10 |10 |10 |10 |
| 每個應用程式的自訂網域</a> |0 (僅限 azurewebsites.net 子網域)|500 |500 |500 |500 |500 |
| 自訂網域 [SSL 支援](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |不支援，萬用字元憑證 *。 預設為可用的 azurewebsites.net|不支援，萬用字元憑證 *。 預設為可用的 azurewebsites.net|無限制的 SNI SSL 連線 |包含無限制的 SNI SSL 和 1 個 IP SSL 連線 |包含無限制的 SNI SSL 和 1 個 IP SSL 連線 | 包含無限制的 SNI SSL 和 1 個 IP SSL 連線|
| 整合的負載平衡器 | |X |X |X |X |X<sup>9</sup> |
| [永遠開啟](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [排定的備份](../articles/app-service/manage-backup.md) | | | | 排程的備份每隔 2 小時，最多 12 每天的備份 （手動 + 排程） | 排程每小時備份，最多 50 每天的備份 （手動 + 排程） | 排程每小時備份，最多 50 每天的備份 （手動 + 排程） |
| [Autoscale](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Azure 排程器](https://azure.microsoft.com/services/scheduler/) 支援 | |X |X |X |X |X |
| [端點監視](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [預備位置](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99.9% |99.95%|99.95%|99.95%|  

<sup>1</sup>應用程式和儲存體配額是依每個 App Service 方案為準，除非另有指示。  
<sup>2</sup>您可以在這些電腦上裝載的實際應用程式數目，會視應用程式的活動、電腦執行個體的大小，及對應的資源使用率而定。  
<sup>3</sup>專用的執行個體可有不同的大小。 如需詳細資訊，請參閱 [App Service 價格](https://azure.microsoft.com/pricing/details/app-service/)。  
<sup>4</sup>收到要求時允許更多。  
<sup>5</sup>儲存體限制是跨相同 App Service 方案中所有應用程式的目前總大小。  
<sup>6</sup>這些資源都會受到專用執行個體 (執行個體大小和執行個體數目) 上的實體資源限制。  
<sup>7</sup>如果您將基本層的一個應用程式調整為兩個執行個體，則其中每個執行個體有 350 個並行連線。  
<sup>8</sup>在您的 App Service 執行個體中，以背景工作的方式隨選、依照排程或連續執行自訂可執行檔和/或指令碼。 若要連續執行 WebJobs，「永遠開啟」是必要選項。 若是排程 WebJobs，則 Azure 排程器免費或標準版本是必要項目。 沒有任何預先定義的限制，可以在 App Service 執行個體中執行的 Webjob 數目。 沒有實際限制取決於應用程式程式碼嘗試執行。  
<sup>9</sup>app Service 隔離式 Sku 能夠在內部達成負載平衡 (ILB) 透過 Azure Load Balancer，這樣就沒有公用網際網路連線。 因此，ILB 隔離式 App Service 的某些功能必須從具有 ILB 網路端點直接存取權的電腦才能使用。  
<sup>10</sup>透過支援要求，以最大限制為 200，可以增加每個訂用帳戶的 App Service 憑證配額限制。  