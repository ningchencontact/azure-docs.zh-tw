---
title: Azure 春季雲端的常見問題 |Microsoft Docs
description: 回顧 Azure 春季雲端的常見問題
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 1fb241ab1335e6b9c9be55acbbe2e21378831b69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607234"
---
# <a name="frequently-asked-questions"></a>常見問題集

本文說明 Azure 春季雲端的常見問題。 

## <a name="general"></a>一般

### <a name="why-azure-spring-cloud"></a>為何選擇 Azure 春季雲端？

Azure 春季雲端為春天開發人員提供平臺即服務（PaaS）。 Azure 春季雲端會管理您的應用程式基礎結構，讓您可以專注于應用程式的程式碼和商務邏輯。 Azure 春季雲端內建的核心功能，包括 Eureka、設定伺服器、服務登錄伺服器、Pivotal 組建服務、藍綠部署等等。 此服務也可讓開發人員將其應用程式與 Azure 服務（例如 CosmosDB、MySQL 和 Azure Cache for Redis）系結。

Azure 春季雲端藉由整合 Azure 監視器、Application Insights 和 Log Analytics，為開發人員和操作員增強應用程式診斷體驗。

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure 春季雲端提供哪些服務方案？

Azure 春季雲端會在預覽期間提供一個服務方案。  春季雲端部署包含16個 vCPU 核心和32GB 的記憶體。  部署中每個微服務實例的上限是4個具有 8 GB 記憶體的 vCPU 核心。

資源 | Amount
------- | -------
每次春季應用程式的應用程式實例 | 20
每個 Azure 春季雲端服務實例的應用程式實例總數 | 50 *
每個訂用帳戶每個區域的 Azure 春季雲端服務實例 | 2
永續性磁碟區 | 10 x 50 Gb

*_開啟[支援票證](https://azure.microsoft.com/support/faq/)來提高限制。_

如需詳細資訊，請參閱[Azure 支援常見問題](https://azure.microsoft.com/support/faq/)。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure 春季雲端有多安全？

安全性和隱私權是 Azure 和 Azure 春季雲端客戶的首要優先順序之一。 Azure 會確保只有客戶可以透過安全地加密所有的資料、記錄或設定來存取應用程式資料、記錄或設定。 Azure 春季雲端中的所有服務實例會彼此隔離。

Azure 春季雲端提供完整的 SSL 和憑證管理。

OpenJDK 和春季雲端執行時間的重大安全性修補程式會儘快套用至 Azure 春季雲端。

### <a name="which-regions-azure-spring-cloud-are-available"></a>Azure 春季雲端有哪些區域可供使用？

美國東部、美國西部2、西歐和東南亞。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure 春季雲端的已知限制為何

以下是 Azure 春季雲端在服務處於預覽狀態時的已知限制。

* `spring.application.name` 將會被用來建立每個應用程式的應用程式名稱覆寫。
* Git 存放庫的設定檔中不允許 `server.port`。 將它新增至設定檔，可能會導致無法從其他應用程式或網際網路連線到您的應用程式。
* Azure 入口網站和 Resource Manager 範本不支援上傳應用程式套件。 這只能透過 Azure CLI 的應用程式部署來完成。
* 如需配額限制，請參閱[Azure 春季雲端供應專案的服務方案](#what-service-plans-does-azure-spring-cloud-offer)。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>如何提供意見反應和報告問題？

如果您遇到 Azure 春季雲端的任何問題，請建立[Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 如需功能要求，請前往[Azure 意見](https://feedback.azure.com/forums/34192--general-feedback)反應以要求功能或提供意見反應。

## <a name="development"></a>開發

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>我是春季的開發人員，但剛開始使用 Azure，我最快的方法是瞭解如何開發 Azure 春季雲端應用程式？

開始使用 Azure 春季雲端最快速的方式是遵循[此快速入門](spring-cloud-quickstart-launch-app-portal.md)。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure 春季雲端支援的 JAVA 執行時間為何？

Azure 春季雲端支援 JAVA 8 和11。

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>我可以在哪裡看到春季應用程式記錄和計量？

在 [應用程式總覽] 索引標籤和 [ [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics)] 索引標籤中尋找計量。

Azure 春季雲端支援將您的春季應用程式記錄和計量匯出至 Azure 儲存體、EventHub 和[Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)。 Log Analytics 中的資料表名稱是 `AppPlatformLogsforSpring`。 若要啟用它，請參閱這篇有關我們的[診斷服務](diagnostic-services.md)的文章。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure 春季雲端是否支援分散式追蹤？

是，如需詳細資訊，請造訪[分散式追蹤](spring-cloud-tutorial-distributed-tracing.md)。

### <a name="what-resource-types-does-service-binding-support"></a>服務系結支援哪些資源類型？

目前支援三項服務： Azure Cosmos DB、適用於 MySQL 的 Azure 資料庫和 Azure Cache for Redis。

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>我可以從應用程式內查看/新增/移動持續性磁片區嗎？
是。

## <a name="deployment"></a>部署

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure 春季雲端是否支援藍綠部署？
是，請造訪[預備環境指南](spring-cloud-howto-staging-environment.md)以取得詳細資料。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>我可以存取 Kubernetes 來操作我的應用程式容器嗎？

不會。  Azure 春季雲端會從基礎架構抽象化開發人員，讓您能夠專注于應用程式的程式碼和商務邏輯。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure 春季雲端支援從來源建立容器嗎？

是，如需詳細資訊，請流覽[從來源部署](spring-cloud-launch-from-source.md)。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure 春季雲端是否支援在應用程式實例中自動調整？

不會。

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>將現有的春季微服務遷移至 Azure 春季雲端的最佳作法為何？

將現有的春季微服務遷移至 Azure 春季雲端之前，
* 所有的應用程式相依性都必須加以解析。
* 準備您的設定專案、環境變數及 JVM 參數，讓您可以將它們與 Azure 春季雲端中的部署進行比較。
* 如果您想要使用服務系結，請流覽您的 Azure 服務，並確定您已設定適當的存取權限。
* 我們建議您移除或停用可能與 Azure 春季雲端管理的服務（例如我們的服務探索服務、設定伺服器等）發生衝突的內嵌服務。
*-* 建議您使用官方和穩定的 Pivotal 彈簧程式庫。 非官方、搶鮮版（Beta）或分支版的 Pivotal 春季程式庫不提供 SLA 支援。

在遷移之後，監視 CPU/RAM 計量和網路流量，以確保應用程式實例會適當地調整。

## <a name="next-steps"></a>後續步驟

[如果您有其他問題，請參閱疑難排解指南](spring-cloud-troubleshoot.md)。