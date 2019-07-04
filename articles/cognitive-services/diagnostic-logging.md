---
title: 診斷記錄
titleSuffix: Azure Cognitive Services
description: 本指南提供逐步指示來啟用診斷記錄的 Azure 認知服務。 這些記錄檔會提供用於問題識別及偵錯的豐富、 經常性資料作業的資源。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: e1a6a44d7ff9d5786388fc47245ef5c79cb9be82
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155748"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>啟用診斷記錄的 Azure 認知服務

本指南提供逐步指示來啟用診斷記錄的 Azure 認知服務。 這些記錄檔會提供用於問題識別及偵錯的豐富、 經常性資料作業的資源。 在繼續之前，您必須具有至少一個的認知服務，訂用帳戶的 Azure 帳戶這類[Bing Web 搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview)，[語音服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)，或[LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)。

## <a name="prerequisites"></a>必要條件

若要啟用診斷記錄，您將需要將記錄檔資料儲存在某處。 本教學課程使用 Azure 儲存體和 Log Analytics。

* [Azure 儲存體](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs)-保留診斷記錄檔的原則稽核、 靜態分析或備份。 儲存體帳戶不一定要和資源發出記錄屬於相同的訂用帳戶，只要使用者有適當的設定可 RBAC 存取這兩個訂用帳戶即可。
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) -彈性的記錄搜尋和分析工具，可讓您的 Azure 資源所產生的未經處理記錄檔的分析。

> [!NOTE]
> 有其他組態選項。 若要進一步了解，請參閱[收集並取用來自 Azure 資源的記錄資料](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)。

## <a name="enable-diagnostic-log-collection"></a>啟用診斷記錄檔收集  

現在就開始啟用診斷記錄使用 Azure 入口網站。

> [!NOTE]
> 若要啟用這項功能，使用 PowerShell 或 Azure CLI，使用所提供的指示[收集並取用來自 Azure 資源的記錄資料](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs)。

1. 瀏覽至 Azure 入口網站。 然後尋找並選取 認知服務資源。 例如，Bing Web 搜尋訂用帳戶。   
2. 接下來，從左側導覽功能表中，找出**監視**，然後選取**診斷設定**。 此畫面包含此資源的所有先前建立的診斷設定。
3. 如果您想要使用先前建立的資源，您可以現在選取它。 否則，請選取 **+ 新增診斷設定**。
4. 輸入設定的名稱。 然後選取**封存至儲存體帳戶**並**傳送給 log Analytics**。
5. 出現提示時設定，請選取儲存體帳戶和您想要用來儲存診斷記錄的 OMS 工作區。 **注意**：如果您沒有儲存體帳戶或 OMS 工作區，請依照下列提示來建立一個。
6. 選取 **稽核**，**要求回應**，並**AllMetrics**。 然後設定您的診斷記錄資料的保留期限。 如果保留原則設定為零，會無限期地儲存該記錄類別的事件。
7. 按一下 [檔案]  。

可能需要最多兩個小時，才有可用來查詢及分析記錄資料。 因此別擔心，如果您沒有立即看到任何項目。

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>檢視和匯出從 Azure 儲存體的診斷資料

Azure 儲存體是最適合用於儲存大量非結構化資料的強固的物件儲存體解決方案。 在本節中，您將了解如何在 30 天的時間範圍來查詢您的儲存體帳戶的交易總數，並將資料匯出至 excel。

1. 從 Azure 入口網站中，找出您在上一節中建立的 Azure 儲存體資源。
2. 從左側導覽功能表中，找出**監視**，然後選取**計量**。
3. 您可以使用可用的下拉式清單來設定您的查詢。 此範例中，為了讓我們設定的時間範圍 」**過去 30 天**和度量**交易**。
4. 查詢完成時，您會看到在過去 30 天的交易的視覺效果。 若要匯出這項資料，請使用**匯出至 Excel**位於頁面頂端的按鈕。

深入了解您可以如何處理中的診斷資料[Azure 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)。

## <a name="view-logs-in-log-analytics"></a>檢視 Log Analytics 中的記錄

請遵循下列指示來探索資源的 log analytics 資料。

1. 從 Azure 入口網站中，找出，並選取**Log Analytics**左側導覽功能表。
2. 找出並選取您在建立時啟用診斷功能的資源。
3. 底下**一般**，找出並選取**記錄**。 從這個頁面上，您可以執行查詢，針對您的記錄檔。

### <a name="sample-queries"></a>範例查詢

以下是一些基本的 Kusto 查詢可用來瀏覽您的記錄資料。

從 Azure 認知服務一段指定的時間中執行這項查詢的所有診斷記錄檔：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

執行此查詢來查看 10 個最新的記錄：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

群組作業所要執行這項查詢**資源**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
執行這項查詢來尋找平均時間就能執行的作業：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

執行此查詢來檢視作業的磁碟區一段時間由 OperationName 分割以每隔 10 秒的分類收納的計數。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>後續步驟

* 若要了解如何啟用記錄，以及各種 Azure 服務所支援的計量和記錄類別，請閱讀[度量的概觀](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)Microsoft Azure 中及[Azure 診斷記錄概觀](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)文章。
* 閱讀下列文章來了解事件中樞：
  * [Azure 事件中樞是什麼？](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [開始使用事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* 閱讀[從 Azure 儲存體下載計量和診斷記錄](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs)。
* 讀取[了解 Azure 監視器記錄檔中的記錄檔搜尋](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new)。
