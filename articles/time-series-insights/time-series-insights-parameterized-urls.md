---
title: 使用參數化 URL 共用 Azure 時間序列深入解析自訂檢視 | Microsoft Docs
description: 本文說明如何在 Azure 時間序列深入解析中開發參數化 URL，以便輕鬆地共用客戶檢視。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: df60429a8b3d6fbdc504a7605d1502b4e084d386
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165309"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>使用參數化 URL 共用自訂檢視

若要共用自訂檢視時間序列深入解析總管中的，您可以以程式設計方式建立自訂檢視的參數化的 URL。

時間序列深入解析總管支援 URL 查詢參數，直接從 URL 中指定的檢視。 例如，僅使用 URL，您就可以指定目標環境、搜尋述詞，以及所需的時間範圍。 當使用者選取自訂的 URL 時，介面會直接與該資產在 Time Series Insights 入口網站中提供的連結。 適用資料存取原則。

> [!TIP]
> * 檢視免費[時間序列深入解析示範](https://insights.timeseries.azure.com/samples)。
> * 閱讀隨附[時間序列深入解析總管](./time-series-insights-explorer.md)文件。

## <a name="environment-id"></a>環境識別碼

`environmentId=<guid>` 參數可指定目標環境識別碼。 它是資料存取 FQDN 的元件，您就可以在 Azure 入口網站中環境概觀的右上角中找到它。 這是在 `env.timeseries.azure.com` 之前的所有內容。

範例環境識別碼參數為 `?environmentId=10000000-0000-0000-0000-100000000108`。

## <a name="time"></a>Time

您可以使用參數化 URL 來指定絕對或相對時間值。

### <a name="absolute-time-values"></a>絕對時間值

對於絕對時間值，使用 `from=<integer>` 和 `to=<integer>` 參數。

* `from=<integer>` 是搜尋範圍的開始時間值 (以 JavaScript 毫秒為單位)。
* `to=<integer>` 是搜尋範圍的結束時間值 (以 JavaScript 毫秒為單位)。

若要識別日期的 JavaScript 毫秒，請參閱 [Epoch 和 Unix 時間戳記轉換器](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)。

### <a name="relative-time-values"></a>相對時間值

對於相對時間值，使用 `relativeMillis=<value>`，其中 value  是後端最近資料中的時間值 (以 JavaScript 毫秒為單位)。

例如，`&relativeMillis=3600000` 可顯示最近 60 分鐘的資料。

接受值會對應至時間序列深入解析總管**快速時間** 功能表中，並且包含：

* `1800000` （過去 30 分鐘）
* `3600000` （過去 60 分鐘）
* `10800000` （過去 3 小時內）
* `21600000` （過去 6 小時）
* `43200000` （過去 12 小時）
* `86400000` （過去 24 小時）
* `604800000` （過去 7 天）
* `2592000000` （過去 30 小時）

### <a name="optional-parameters"></a>選擇性參數

`timeSeriesDefinitions=<collection of term objects>`參數指定的時間序列深入解析檢視條款：

| 參數 | URL 項目 | 描述 |
| --- | --- | --- |
| **name** | `\<string>` | term  的名稱。 |
| **splitBy** | `\<string>` | split by  的資料行名稱。 |
| **measureName** | `\<string>` | measure  的資料行名稱。 |
| **predicate** | `\<string>` | 用於伺服器端篩選的 where  子句。 |
| **useSum** | `true` | 指定使用總和作為您的量值的選擇性參數。 </br>  請注意，如果`Events`是選取的量值，預設會選取計數。  </br>  如果`Events`是未選取，平均值會選取預設值。 |

* `multiChartStack=<true/false>`索引鍵 / 值組，可讓 「 堆疊圖表中。
* `multiChartSameScale=<true/false>`索引鍵 / 值組的選擇性參數括住的詞彙之間啟用相同的 y 軸刻度。  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>`可讓您調整間隔滑桿，以提供更精細或更平滑、 更彙總的圖表檢視。  
* `timezoneOffset=<integer>`參數可讓您設定的時區與 UTC 的位移，在中檢視圖表。

| 組 | 描述 |
| --- | --- |
| `multiChartStack=false` | `true` 依預設會啟用因此傳遞`false`堆疊。 |
| `multiChartStack=false&multiChartSameScale=true` | 必須啟用堆疊，才能在不同時段使用相同的 Y 軸刻度。  它有`false`根據預設，所以傳遞 'true'，可讓這項功能。 |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | 單位 = 天、小時、分鐘、秒、毫秒。  單位一律大寫。 </br> 對 timeBucketSize 傳遞所需的整數，以定義單位數。  請注意，您最多可以平滑處理 7 天。  |
| `timezoneOffset=-<integer>` | 整數一律以毫秒為單位。 </br> 請注意，這項功能會稍有不同於我們在時間序列深入解析總管中，我們可讓您選擇本機 （瀏覽器時間） 或 UTC 所啟用的。 |

### <a name="examples"></a>範例

若要將時間序列定義新增至 Time Series Insights 環境做為 URL 參數，將附加：

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

使用範例時間序列定義使用於：

* 環境識別碼
* 過去的 60 分鐘的資料
* 詞彙 （F1PressureID、 F2TempStation 和 F3VibrationPL） 構成的選擇性參數

您可以建構下列參數化的 URL 以檢視：

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> 請參閱 [總管] 中即時[使用 URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}])。

上述的 URL 將告訴您，並建置時間序列深入解析總管檢視：

[![時間序列深入解析總管字詞](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

完整的檢視 （包括圖表）：

[![圖表檢視](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>後續步驟

* 了解如何[查詢的資料使用C# ](time-series-insights-query-data-csharp.md)。

* 深入了解[時間序列深入解析總管](./time-series-insights-explorer.md)。
