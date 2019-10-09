---
title: 分析 Azure 春季雲端中的記錄和計量 |Microsoft Docs
description: 瞭解如何分析 Azure 春季雲端中的診斷資料
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038882"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>使用診斷設定來分析記錄和計量

Azure 春季雲端的診斷功能可讓您使用下列其中一項服務來分析記錄和計量：

* 使用 Azure Log Analytics 加以分析，其中資料會立即寫入 Azure Log Analytics，而不需要先將資料寫入儲存體。
* 將它們儲存到儲存體帳戶，以進行審核或手動檢查。 您可以指定保留時間（以天為單位）。
* 將它們串流至事件中樞，以供協力廠商服務或自訂分析解決方案進行內嵌。

若要開始使用，您必須啟用其中一項服務以接收資料。  若要瞭解如何設定 Log Analytics，請參閱[此教學](../azure-monitor/log-query/get-started-portal.md)課程。  

## <a name="configure-diagnostic-settings"></a>設定診斷設定

1. 在 Azure 入口網站中，移至您的 Azure 春季雲端實例。
1. 選取 [**診斷設定**] 功能表選項。
1. 選取 [**新增診斷設定**] 按鈕。
1. 輸入設定的名稱，然後選擇您要傳送記錄的位置。 您可以選取下列三個選項的任何組合：
    * 封存至儲存體帳戶
    * 串流至事件中樞
    * 傳送至 Log Analytics

1. 選擇您想要監視的記錄類別和計量類別，並指定保留時間（以天為單位）。 保留時間僅適用于儲存體帳戶。
1. 選取 [儲存] 來套用設定。

> [!NOTE]
> 當記錄或計量發出時，以及它們出現在儲存體帳戶/事件中樞/Log Analytics 時，可能會有15分鐘的時間。

## <a name="viewing-logs"></a>檢視記錄

### <a name="using-log-analytics"></a>使用 Log Analytics

1. 從 [Azure 入口網站] 的左側導覽功能表中，選取 [Log Analytics]。
1. 選取您在新增診斷設定時所選擇的 Log Analytics 工作區。
1. 選取 [`Logs`] 以開啟 [記錄搜尋] 分頁。
1. 在 [記錄搜尋] 方塊中輸入簡單的查詢。  例如:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. 選取 [`Run`] 以查看搜尋結果。
1. 您可以藉由設定篩選準則來搜尋特定應用程式或實例的記錄：

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

若要深入瞭解 Log Analytics 中使用的查詢語言，請參閱[這篇文章](../azure-monitor/log-query/query-language.md)

### <a name="using-logs-and-metrics-in-storage-account"></a>在儲存體帳戶中使用記錄和計量

1. 從 Azure 入口網站中，從左側導覽功能表中選取 儲存體帳戶。
1. 選取您在新增診斷設定時所選擇的儲存體帳戶。
1. 選取 [`Blobs`] 專案，以開啟 [Blob 容器] 分頁。
1. 尋找名為 `insights-logs-applicationconsole` 的容器，以審查應用程式記錄檔。
1. 尋找名為 `insights-metrics-pt1m` 的容器，以審查應用程式計量。

[深入瞭解如何將診斷資訊傳送至儲存體帳戶。](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>使用事件中樞

1. 從 Azure 入口網站中，從左側導覽功能表中選取 事件中樞。
1. 找出並選取您在新增診斷設定時所選擇的事件中樞。
1. 選取 [`Event Hubs`] 以開啟 [事件中樞清單] 分頁。
1. 尋找名為 `insights-logs-applicationconsole` 的事件中樞來審查應用程式記錄檔。
1. 尋找名為 `insights-metrics-pt1m` 的事件中樞來審查應用程式計量。

[深入瞭解如何將診斷資訊傳送到事件中樞。](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyzing-logs"></a>分析記錄

Azure Log Analytics 提供 Kusto，讓您可以查詢記錄以進行分析。  如需使用 Kusto 查詢記錄的快速簡介，請參閱[Log Analytics 教學](../azure-monitor/log-query/get-started-portal.md)課程。

應用程式記錄可提供有關應用程式健康情況、效能等的重要資訊。  以下是一些簡單的查詢，可協助您瞭解應用程式目前和過去的狀態。

### <a name="show-application-logs-from-azure-spring-cloud"></a>顯示 Azure 春季雲端的應用程式記錄

若要從 Azure 春季雲端審查應用程式記錄清單，請依時間排序，並先顯示最新的記錄：

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>顯示包含錯誤或例外狀況的記錄專案

此查詢可讓您查看提及錯誤或例外狀況的記錄專案。  結果不會排序。

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

使用此查詢來尋找錯誤，或修改查詢字詞以尋找特定的錯誤碼或例外狀況。  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>顯示過去一小時內，您的應用程式所報告的錯誤和例外狀況數目

此查詢會建立一個圓形圖，顯示您的應用程式在過去一小時內所記錄的錯誤數目和例外狀況：

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>深入瞭解如何查詢應用程式記錄

Azure 監視器提供使用 Log Analytics 查詢應用程式記錄的廣泛支援。  若要深入瞭解此服務，請參閱使用 Azure 監視器的[記錄查詢](../azure-monitor/log-query/get-started-queries.md)教學課程。 [Azure 監視器中的記錄查詢總覽](../azure-monitor/log-query/log-query-overview.md)提供有關建立查詢以分析應用程式記錄檔的詳細資訊。
