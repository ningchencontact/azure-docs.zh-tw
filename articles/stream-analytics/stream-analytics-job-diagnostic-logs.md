---
title: 使用析診斷記錄針對 Azure 串流分進行疑難排解
description: 本文說明如何分析 Azure 串流分析中的診斷記錄。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ff2930fbe0e53c4b3c1223f87919c0913296d07c
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515926"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>使用析診斷記錄對 Azure 串流分進行疑難排解

有時候，Azure 串流分析作業會非預期地停止處理。 請務必要設法解決這類事件的問題。 錯誤發生的原因可能是非預期的查詢結果、裝置的連線狀況，或未預期的服務中斷。 串流分析中的診斷記錄可協助您在事件發生當下找出問題原因，並減少復原時間。

## <a name="log-types"></a>記錄類型

串流分析提供開兩種記錄類型：

* [活動記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (永遠啟動) 可讓您對所執行作業有深入的了解。

* [診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (可設定) 可讓您更深入地了所有與作業相關的發生事件。 診斷記錄會在作業建立時開始執行，並在作業刪除時結束。 這些記錄涵蓋作業更新時與作業執行時的情形。

> [!NOTE]
> 您可以使用服務，例如 Azure 儲存體、 Azure 事件中樞和 Azure 監視器記錄來分析不一致的資料。 這些服務將會依據各自的計價模式向您收取費用。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>使用活動記錄進行偵錯

活動記錄預設會開啟，可以對 Stream Analytics 作業所執行的作業提供高層級的見解。 活動記錄中出現的資訊可能有助於找出影響您工作之問題的根本原因。 執行下列步驟，以在 Stream Analytics 中使用活動記錄：

1. 登入 Azure 入口網站，並在 [概觀]  下選取 [活動記錄]  。

   ![Stream Analytics 活動記錄](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. 您可以看到一份已執行的作業清單。 任何造成您的作業 (job) 失敗的任何作業 (operation) 都會出現紅色的資訊泡泡。

3. 按一下作業，以查看其摘要檢視。 這裡的資訊通常是有限的。 若要深入了解作業的相關資訊，請按一下 [JSON]  。

   ![Stream Analytics 活動記錄作業摘要](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. 向下捲動至 JSON 的 [屬性]  區段，該區段提供導致作業失敗的錯誤的詳細資料。 在此範例中，失敗是由於超出範圍的緯度值的執行階段錯誤。 資料處理的 Stream Analytics 作業中的差異會導致資料錯誤。 您可以了解不同[輸入和輸出資料錯誤和發生的原因](https://docs.microsoft.com/azure/stream-analytics/data-errors)。

   ![JSON 錯誤詳細資料](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. 您可以根據 JSON 中的錯誤訊息採取修正動作。 在此範例中，需要檢查以確保緯度值是介於 -90 度和 90 度之間。

6. 如果活動記錄檔中的錯誤訊息不是很有幫助識別根本原因，請啟用診斷記錄檔，並使用 Azure 監視器記錄檔。

## <a name="send-diagnostics-to-azure-monitor-logs"></a>將診斷傳送至 Azure 監視器記錄檔

強烈建議您開啟診斷記錄檔，並將它們傳送到 Azure 監視器記錄檔。 診斷記錄預設為 [關閉]  。 若要開啟診斷記錄，請完成下列步驟︰

1.  登入 Azure 入口網站，然後瀏覽至您的 Stream Analytics 作業。 在 [監視]  下，選取 [診斷記錄]  。 然後選取 [開啟診斷]  。

    ![瀏覽到診斷記錄的刀鋒視窗](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  在 [診斷設定]  中建立 [名稱]  ，然後核取 [傳送至 Log Analytics]  旁邊的方塊。 然後，新增現有的 Log Analytics 工作區或建立新的 **Log Analytics 工作區**。 核取 [記錄]  下的 [執行]  和 [編寫]  ，以及 [計量]  下的 [AllMetrics]  核取方塊。 按一下 [檔案]  。

    ![診斷記錄的設定](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. 當您的 Stream Analytics 作業啟動時，診斷記錄會路由傳送至 Log Analytics 工作區。 瀏覽至 Log Analytics 工作區，然後選擇 [一般]  區段下的 [記錄]  。

   ![在 [一般] 區段下的 azure 監視器記錄檔](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. 您可以[撰寫自己的查詢](../azure-monitor/log-query/get-started-portal.md)來搜尋字詞、識別趨勢、分析模式，以及提供以資料為基礎的深入解析。 例如，您可以撰寫查詢以篩選僅具有訊息「串流處理工作失敗」的診斷記錄。 Azure Stream Analytics 的診斷記錄會儲存在 **AzureDiagnostics** 資料表中。

   ![診斷查詢和結果](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. 如果您的查詢正在搜尋正確的記錄，請選取 [儲存]  並提供 [名稱] 和 [類別] 進行儲存。 然後，您可以透過選取 [新增警示規則]  來建立警示。 接下來，指定警示條件。 選取 [條件]  ，並輸入臨界值以及評估此自訂記錄搜尋的頻率。  

   ![診斷記錄搜尋查詢](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. 在建立警示規則之前，請選擇動作群組並指定警示的詳細資訊 (例如名稱和描述)。 您可以將各種作業的診斷記錄路由到相同的 Log Analytics 工作區。 這可讓您設定一次警示之後即適用於所有作業。  

## <a name="diagnostics-log-categories"></a>診斷記錄類別

Azure Stream Analytics 會擷取診斷記錄的兩個的類別：

* **編寫**：擷取與作業編寫作業相關之記錄事件，例如作業建立、新增及刪除輸入與輸出、新增及更新查詢、開始及停止作業。

* **執行**：擷取作業執行期間發生的事件。
    * 連線錯誤
    * 資料處理錯誤，包括：
        * 不符合查詢定義的事件 (不相符的欄位類型與值或遺漏欄位等)
        * 運算式評估錯誤
    * 其他事件和錯誤

## <a name="diagnostics-logs-schema"></a>診斷記錄結構描述

所有記錄會儲存為 JSON 格式。 每個項目皆包含下列常見的字串欄位︰

Name | 描述
------- | -------
time | 記錄的時間戳記 (UTC 時間)。
ResourceId | 作業執行資源的識別碼 (大寫)。 其中包含訂用帳戶識別碼、資源群組，以及作業名稱。 例如， **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**。
category | 記錄類別 (**執行**或**編寫**)。
operationName | 記錄的作業名稱。 例如，**傳送事件︰SQL 輸出寫入失敗至 mysqloutput**。
status | 作業的狀態。 例如，**失敗**或**成功**。
層級 | 記錄層級。 例如，**錯誤**、**警告**或**資訊**。
properties | 記錄項目特定詳細資料 (序列化為 JSON 字串)。 如需詳細資訊，請參閱本文中下列幾節。

### <a name="execution-log-properties-schema"></a>執行記錄屬性結構描述

執行記錄包含在執行串流分析作業期間所發生事件的資訊。 屬性結構描述會根據事件是資料錯誤，或是一般的事件而有所不同。

### <a name="data-errors"></a>資料錯誤

作業處理資料時發生的任何錯誤皆包含於此類記錄中。 這些記錄最常於資料讀取、序列化和寫入作業時建立。 這些記錄不包含連線錯誤。 連線錯誤視為一般事件。

Name | 描述
------- | -------
`Source` | 發生錯誤的作業輸入或輸出名稱。
Message | 與錯誤相關的訊息。
類型 | 錯誤類型。 例如，**DataConversionError**、**CsvParserError** 或 **ServiceBusPropertyColumnMissingError**。
資料 | 包含有助於正確找到錯誤來源的資料。 視其大小，資料可能會遭到截斷。

資料錯誤會隨 **operationName** 值的不同而有下列結構描述：

* **序列化事件**事件讀取作業期間發生。 當輸入上的資料不符合查詢結構描述時就會發生這類事件，發生原因可能是下列其中一個：

   * *事件 (還原) 序列化期間類型不相符*：找出造成錯誤的欄位。

   * *無法讀取事件，序列化無效*：針對發生錯誤的輸入資料位置列出相關資訊。 包含 Blob 輸入的 Blob 名稱、位移和資料範例。

* **將事件傳送**寫入作業期間發生。 這些事件會識別造成錯誤的串流事件。

### <a name="generic-events"></a>一般事件

一般事件涵蓋所有其他事件。

Name | 描述
-------- | --------
Error | (選用) 錯誤資訊。 這通常是例外狀況資訊 (如果有的話)。
Message| 記錄訊息。
類型 | 訊息類型。 對應至錯誤的內部分類。 例如，**JobValidationError**或 **BlobOutputAdapterInitializationFailure**。
相互關連識別碼 | 唯一識別作業執行的 [GUID (英文)](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 從作業開始直到作業停止的所有執行記錄項目皆具有同一個**相互關聯識別碼**值。

## <a name="next-steps"></a>後續步驟

* [串流分析介紹](stream-analytics-introduction.md)
* [開始使用串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整串流分析作業](stream-analytics-scale-jobs.md)
* [串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
