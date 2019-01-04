---
title: Azure 時間序列深入解析疑難排解 - 在 Azure 時間序列深入解析中診斷與解決問題 | Microsoft Docs
description: 本文描述如何在 Azure 時間序列深入解析環境中診斷、疑難排解與解決可能會遇到的常見問題。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.custom: seodec18
ms.openlocfilehash: 8e9a2bc8378f71e510d11b3e28438489d620ff6f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322323"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>在時間序列深入解析環境中診斷與解決問題

本文描述您在 Azure 時間序列深入解析環境中可能遇到的一些問題。 文章中提供可能的原因和解決方案以便排除問題。

## <a name="video"></a>視訊： 

在本影片中，我們將說明常見的時間序列深入解析客戶挑戰和緩和措施：</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-1-no-data-is-shown"></a>問題 1：未顯示資料

有幾個常見原因可能造成 [Azure 時間序列深入解析總管](https://insights.timeseries.azure.com)中沒有資料：

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>原因 A：事件來源資料不是 JSON 格式

Azure 時間序列深入解析只支援 JSON 資料。 如需 JSON 範例，請參閱[支援的 JSON 樣貌](./how-to-shape-query-json.md)。

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>原因 B：可能原因 B：事件來源機碼遺漏必要的權限

* 針對 Azure IoT 中樞的 IoT 中樞，您必須提供有**服務連線**權限的機碼。 **iothubowner** 或 **service** 原則都可以，因為它們都有**服務連線**權限。

   ![IoT 中樞服務連線權限](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)


* 針對 Azure 事件中樞中的事件中樞，您必須提供有**接聽**權限的機碼。 **read** 或 **manage** 原則都可以，因為它們都有**接聽**權限。

   ![事件中樞接聽權限](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>原因 C：提供的取用者群組不是時間序列深入解析專用的

當您註冊 IoT 中樞或事件中樞時，請務必設定您想要用來讀取資料的取用者群組。 此取用者群組「不可是共用的」。 如果取用者群組是共用的，基礎 IoT 中樞或事件中樞會自動且隨機地將其中一個讀者中斷連線。 提供唯一的取用者群組讓時間序列深入解析讀取。

## <a name="problem-2-some-data-is-shown-but-data-is-missing"></a>問題 2：某些資料會顯示，但遺漏資料

當資料僅部分出現，且資料似乎有延遲的情況，您應該考慮幾個可能性。

### <a name="cause-a-your-environment-is-being-throttled"></a>原因 A：您的環境正在進行節流

建立有資料的事件來源之後，在佈建環境時，節流是常見的問題。 Azure IoT 中樞和 Azure 事件中樞會將資料儲存長達七天。 時間序列深入解析一律會從事件來源中最舊的事件 (「先進先出」或 *FIFO*) 開始讀取。 

例如，當您連線到某個單一單位 S1 時間序列深入解析環境時，如果事件來源中有 5 百萬個事件，時間序列深入解析每天會讀取約 1 百萬個事件。 這樣看起來像時間序列深入解析有五天的延遲。 不過，實際上是環境正在進行節流。 

如果您的事件來源中有舊的事件，您可以使用以下兩種方式之一進行節流：

- 變更您事件來源的保留期限制，以協助移除您不想要顯示在時間序列深入解析的舊事件。
- 佈建較大的環境大小 (單位數)，以增加舊事件的輸送量。 使用上述範例，如果您將相同的 S1 環境增加到一天五個單位，該環境應該會在一天內趕上。 如果您的穩定狀態事件生產是每天 1 百萬個事件或更少，則您可以在趕上之後，將事件的容量減少到一個單位。

節流限制會根據環境 SKU 類型和容量來強制執行。 環境中所有的事件來源皆共用此容量。 如果IoT 中樞或事件中樞的事件來源推送超過強制限制的資料，您就會看到節流和延遲情形。

下圖顯示有 S1 SKU 且容量為 3 的時間序列深入解析環境。 該環境可以每日輸入 3 百萬個事件。

![環境 SKU 目前容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

例如，假設此環境從事件中樞內嵌訊息。 下圖顯示輸入速率：

![事件中樞的範例輸入率](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

每日輸入速率為 ~67,000 個訊息。 此速率可轉譯為大約每分鐘 46 個訊息。 如果將每個事件中樞訊息壓平合併為單一時間序列深入解析事件，則不會發生節流。 如果每個事件中樞訊息被壓平合併為 100 個時間序列深入解析事件，則每分鐘應內嵌 4,600 個事件。 容量為 3 的 S1 SKU 環境每分鐘只能輸入 2,100 個事件 (每天 1 百萬個事件 = 每三單位每分鐘 700 個事件 = 每分鐘 2,100 個事件)。 對於此設定，因為節流的關係，您會看到延遲。 

若要概略了解壓平合併邏輯的運作方式，請參閱[支援的 JSON 樣貌](./how-to-shape-query-json.md)。

#### <a name="recommended-resolutions-for-excessive-throttling"></a>過度節流的建議解決方法

若要修正延遲情形，請增加您環境的 SKU 容量。 如需詳細資訊，請參閱[調整您的時間序列深入解析環境規模](time-series-insights-how-to-scale-your-environment.md)。

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>原因 B：歷史資料初始內嵌使輸入變慢

如果您連線至現有的事件來源，則您的 IoT 中樞可能已經包含資料。 環境會從事件來源訊息保留期間開始時開始提取資料。 這是預設處理，無法覆寫它。 您可以進行節流。 節流可能需要一些時間才會趕上，因為它要內嵌歷史資料。

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>大型初始擷取的建議解決方法

修正延遲：

1. 將 SKU 容量增加到最大允許值 (在此案例中是 10)。 增加容量之後，輸入程序會開始更快地趕上。 增加容量需支付費用。 您可以從[時間序列深入解析總管](https://insights.timeseries.azure.com)的可用性圖表中看出趕上的速度有多快。 

2. 趕上延遲時間之後，即可將 SKU 容量降到正常的輸入速率。

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>問題 3：我的事件來源的時間戳記屬性名稱設定沒有作用

請確保時間戳記屬性名稱和值符合下列規則：
* 時間戳記屬性名稱會區分大小寫。
* 來自事件來源的時間戳記屬性值 (JSON 字串) 格式應是 _yyyy-MM-ddTHH:mm:ss.FFFFFFFK_。 例如，**2008-04-12T12:53Z**。

若要確保系統已擷取時間戳記屬性名稱且正常運作，最簡單方式是使用時間序列深入解析總管。 在時間序列深入解析總管中，使用圖表，當您輸入時間戳記屬性名稱之後，選取一段時間。 以滑鼠右鍵按一下選取範圍，然後選取 [探索事件] 選項。 

第一個資料行標頭應該是您的時間戳記屬性名稱。 在文字 **Timestamp** 旁邊，您應該會看到 **($ts)**。 

您應該不會看到下列值：
- *(abc)*：表示時間序列深入解析將資料值讀取為字串。
- *行事曆圖示*：表示時間序列深入解析將資料值讀取為「日期時間」。
- *#*：表示時間序列深入解析將資料值讀取為整數。


## <a name="next-steps"></a>後續步驟

- 如需協助，請在 [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights)或 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights) \(英文\) 中開始交談。 
- 如需輔助支援選項，請使用 [Azure 支援](https://azure.microsoft.com/support/options/)。
