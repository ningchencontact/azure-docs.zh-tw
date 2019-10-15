---
title: Azure Spring Cloud 的常見問題集 | Microsoft Docs
description: 檢閱 Azure Spring Cloud 的常見問題集
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 09970468a277dcaf3f28b4f5065572568089a12e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038487"
---
# <a name="frequently-asked-questions"></a>常見問題集

此文章說明關於 Azure Spring Cloud 的常見問題。 

## <a name="general"></a>一般

### <a name="why-azure-spring-cloud"></a>為何選擇 Azure Spring Cloud？

Azure Spring Cloud 為 Spring 開發人員提供平台即服務 (PaaS)。 Azure Spring Cloud 會管理您的應用程式基礎結構，以便您專注在應用程式程式碼和商務邏輯上。 建置在 Azure Spring Cloud 中的核心功能包括 Eurek、設定伺服器、服務登錄伺服器、Pivotal 建置服務、Blue-Green 部署等等。 此服務也讓開發人員能夠繫結其應用程式與 Azure 服務，例如 CosmosDB、MySQL 和 Azure Cache for Redis。

Azure Spring Cloud 可藉由整合 Azure 監視器、Application Insights 和 Log Analytics，來強化開發人員和操作員的應用程式診斷體驗。

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure Spring Cloud 提供哪些服務方案？

Azure Spring Cloud 在預覽期間是免費的。

資源 | Amount
------- | -------
vCPU | 4
記憶體 | 8 GB
應用程式執行個體 (每個 Spring 應用程式) | 20
總應用程式執行個體 (每個 Azure Spring Cloud 服務執行個體) | 50*
Azure Spring Cloud 服務執行個體 (每個訂用帳戶在每個區域) | 2*
永續性磁碟區 | 10 x 50 GB

*建立[支援票證](https://azure.microsoft.com/support/faq/)來提高限制。 

如需詳細資訊，請參閱 [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud 的安全性如何？

安全性和隱私權是 Azure 和 Azure Spring Cloud 客戶的首要考量之一。 Azure 會將應用程式資料、記錄檔或設定都安全地加密，以確保只有客戶才能存取這些資料。 Azure Spring Cloud 中的所有服務執行個體都彼此互相隔離。

Azure Spring Cloud 提供完整的 SSL 和憑證管理。

OpenJDK 和 Spring Cloud 執行階段的重大安全性修補，都會盡快套用至 Azure Spring Cloud。

### <a name="which-regions-azure-spring-cloud-are-available"></a>在哪些區域可以使用 Azure Spring Cloud？

美國東部、美國西部 2、歐洲西部和東南亞。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure Spring Cloud 的已知限制為何

以下是 Azure Spring Cloud 在預覽狀態時的已知限制。

* 用來建立每個應用程式的應用程式名稱會覆寫 `spring.application.name`。
* 來自 Git 存放庫的組態檔中不允許 `server.port`。 在組態檔中新增該項目可能會使您的應用程式無法從其他應用程式或網際網路存取。
* Azure 入口網站和 Resource Manager 範本不支援上傳應用程式封裝。 這只能藉由使用 Azure CLI 進行應用程式部署來完成。
* 如需配額限制的資訊，請參閱 [Azure Spring Cloud 提供哪些服務方案](#what-service-plans-does-azure-spring-cloud-offer)。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>如何提供意見反應和回報問題？

如果您已經在 Azure Spring Cloud 中建立 Spring 服務執行個體，您可以[建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) \(部分機器翻譯\)。 如果您還沒在 Azure Spring Cloud 上線，您可以移至 [Azure 意見反應](https://feedback.azure.com/) \(英文\) 來要求功能或提供意見反應。

## <a name="development"></a>開發

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>我是 Spring 開發人員，但不熟悉 Azure，我可以學習如何開發 Azure Spring Cloud 應用程式的最快方法為何？

開始使用 Azure Spring Cloud 的最快方式是遵循[此快速入門](spring-cloud-quickstart-launch-app-portal.md)。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud 支援的 Java 執行階段為何？

Azure Spring Cloud 支援 Java 8 和 11。

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>我可以在哪裡看到 Spring 應用程式記錄和計量？

在 [應用程式概觀] 索引標籤和 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) \(部分機器翻譯\) 索引標籤中尋找計量。

Azure Spring Cloud 支援將您的 Spring 應用程式記錄檔和計量匯出至 Azure 儲存體、EventHub 和 [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries) \(部分機器翻譯\)。 Log Analytics 中的資料表名稱為 `AppPlatformLogsforSpring`。 若要啟用它，請檢閱這篇有關[診斷服務](diagnostic-services.md)的文章。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud 是否支援分散式追蹤？

是，如需詳細資訊，請造訪[分散式追蹤](spring-cloud-tutorial-distributed-tracing.md)。

### <a name="what-resource-types-does-service-binding-support"></a>服務繫結支援的資源類型為何？

目前支援三個服務：Azure Cosmos DB、適用於 MySQL 的 Azure 資料庫和 Azure Cache for Redis。

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>我可以從應用程式內檢視/新增/移動永續性磁碟區嗎？
是。

## <a name="deployment"></a>部署

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring Cloud 是否支援 Blue-Green 部署？
是，如需詳細資訊，請造訪[預備環境指南](spring-cloud-howto-staging-environment.md)。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>我是否可以存取 Kubernetes 來操作我的應用程式容器？

沒有。  Azure Spring Cloud 將基礎架構對開發人員抽象化，讓您能夠專注於應用程式程式碼和商務邏輯。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud 是否支援從來源建立容器？

是，如需詳細資訊，請造訪[從來源部署](spring-cloud-launch-from-source.md)。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud 是否支援應用程式執行個體自動調整？

沒有。

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>將現有 Spring 微服務移轉至 Azure Spring Cloud 的最佳做法為何？

將現有 Spring 微服務移轉至 Azure Spring Cloud 之前，
* 必須解析所有應用程式相依性。
* 準備您的設定項目、環境變數和 JVM 參數，讓您可以將它們與 Azure Spring Cloud 中的部署進行比較。
* 如果您想要使用服務繫結，請瀏覽您的 Azure 服務，並確定您已設定適當的存取權限。
* 我們建議您移除或停用可能與 Azure Spring Cloud 管理的服務 (例如我們的服務探索服務、設定伺服器等) 發生衝突的內嵌服務。
*-* 建議您使用官方和穩定的 Pivotal Spring 程式庫。 非官方、搶鮮版 (Beta) 或分支版本的 Pivotal Spring 程式庫不提供 SLA 支援。

移轉之後，請監視 CPU/RAM 計量和網路流量，以確保應用程式執行個體會適當地調整。

## <a name="next-steps"></a>後續步驟

[如果您有更多問題，請參閱疑難排解指南](spring-cloud-troubleshoot.md)。