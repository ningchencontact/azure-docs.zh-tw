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
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: ce8c74e5c194dbd971ecb65659c4fc8a7150146d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882912"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>使用參數化 URL 共用自訂檢視

若要在時間序列深入解析總管中共用自訂視圖, 您可以透過程式設計方式建立自訂視圖的參數化 URL。

時間序列深入解析總管支援 URL 查詢參數, 以直接從 URL 指定體驗中的 views。 例如，僅使用 URL，您就可以指定目標環境、搜尋述詞，以及所需的時間範圍。 當使用者選取自訂的 URL 時, 介面會在時間序列深入解析入口網站中直接提供該資產的連結。 適用資料存取原則。

> [!TIP]
> * 觀看免費的[時間序列深入解析示範](https://insights.timeseries.azure.com/samples)。
> * 閱讀隨附的[時間序列深入解析總管](./time-series-insights-explorer.md)檔。

## <a name="environment-id"></a>環境識別碼

`environmentId=<guid>` 參數可指定目標環境識別碼。 它是資料存取 FQDN 的元件, 您可以在 [環境] 的右上角 [Azure 入口網站] 中找到它。 這是在 `env.timeseries.azure.com` 之前的所有內容。

範例環境識別碼參數為 `?environmentId=10000000-0000-0000-0000-100000000108`。

## <a name="time"></a>Time

您可以使用參數化 URL 來指定絕對或相對時間值。

### <a name="absolute-time-values"></a>絕對時間值

對於絕對時間值，使用 `from=<integer>` 和 `to=<integer>` 參數。

* `from=<integer>` 是搜尋範圍的開始時間值 (以 JavaScript 毫秒為單位)。
* `to=<integer>` 是搜尋範圍的結束時間值 (以 JavaScript 毫秒為單位)。

若要識別日期的 JavaScript 毫秒，請參閱 [Epoch 和 Unix 時間戳記轉換器](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)。

### <a name="relative-time-values"></a>相對時間值

對於相對時間值，使用 `relativeMillis=<value>`，其中 value 是後端最近資料中的時間值 (以 JavaScript 毫秒為單位)。

例如，`&relativeMillis=3600000` 可顯示最近 60 分鐘的資料。

接受的值會對應至時間序列深入解析 explorer**快速時間**功能表, 包括:

* `1800000`(過去30分鐘)
* `3600000`(過去60分鐘)
* `10800000`(過去3小時)
* `21600000`(過去6小時)
* `43200000`(過去12小時)
* `86400000`(過去24小時)
* `604800000`(過去7天)
* `2592000000`(過去30小時)

### <a name="optional-parameters"></a>選擇性參數

`timeSeriesDefinitions=<collection of term objects>`參數會指定時間序列深入解析視圖的詞彙:

| 參數 | URL 專案 | 描述 |
| --- | --- | --- |
| **name** | `\<string>` | term 的名稱。 |
| **splitBy** | `\<string>` | split by 的資料行名稱。 |
| **measureName** | `\<string>` | measure 的資料行名稱。 |
| **predicate** | `\<string>` | 用於伺服器端篩選的 where 子句。 |
| **useSum** | `true` | 選擇性參數, 指定針對量值使用 sum。 </br>  請注意, `Events`如果是選取的量值, 則預設會選取 [計數]。  </br>  如果`Events`未選取, 則預設會選取 [平均]。 |

* 索引`multiChartStack=<true/false>`鍵/值組會啟用圖表中的堆疊。
* 索引`multiChartSameScale=<true/false>`鍵/值組可讓您在選擇性參數內的不同詞彙之間進行相同的 Y 軸縮放比例。  
* 可`timeBucketUnit=<Unit>&timeBucketSize=<integer>`讓您調整間隔滑杆, 以提供更精細或更平滑的圖表匯總視圖。  
* `timezoneOffset=<integer>`參數可讓您設定要在中視為 UTC 位移的圖表時區。

| 配對 (s) | 描述 |
| --- | --- |
| `multiChartStack=false` | `true`預設為啟用, 因此傳遞`false`至堆疊。 |
| `multiChartStack=false&multiChartSameScale=true` | 必須啟用堆疊，才能在不同時段使用相同的 Y 軸刻度。  `false`預設為, 因此傳遞「true」可啟用這項功能。 |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | 單位 = 天、小時、分鐘、秒、毫秒。  單位一律大寫。 </br> 對 timeBucketSize 傳遞所需的整數，以定義單位數。  請注意，您最多可以平滑處理 7 天。  |
| `timezoneOffset=-<integer>` | 整數一律以毫秒為單位。 </br> 請注意, 這項功能與我們在 [時間序列深入解析 explorer] 中啟用的方式稍有不同, 我們可以在這裡選擇 [本機 (瀏覽器時間)] 或 [UTC]。 |

### <a name="examples"></a>範例

若要將時間序列定義加入至時間序列深入解析環境做為 URL 參數, 請附加:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

使用的範例時間序列定義:

* 環境識別碼
* 過去60分鐘的資料
* 組成選擇性參數的詞彙 (F1PressureID、F2TempStation 和 F3VibrationPL)

您可以為視圖建立下列參數化 URL:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> 請參閱[使用 URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}])的即時瀏覽器。

上述 URL 描述並建立時間序列深入解析總管 view:

[![時間序列深入解析 explorer 詞彙](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

完整視圖 (包括圖表):

[![圖表視圖](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>後續步驟

* 瞭解如何[ C#使用來查詢資料](time-series-insights-query-data-csharp.md)。

* 深入瞭解[時間序列深入解析總管](./time-series-insights-explorer.md)。
