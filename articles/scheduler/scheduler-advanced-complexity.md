---
title: "如何使用 Azure 排程器建置複雜的排程和進階週期"
description: "了解如何使用 Azure 排程器建置複雜的排程和進階週期。"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4293442e13fc4bae871b1f32a3ed4231d9f32632
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>如何使用 Azure 排程器建置複雜的排程和進階週期

排程是 Azure 排程器作業的核心。 排程會決定排程器執行作業的時機和方式。 

您可以使用排程器為作業設定多個單次和週期排程。 單次排程在指定時間觸發一次。 單次排程實際上是只執行一次的週期排程。 週期排程會根據預先決定的頻率引發。

由於這種彈性，您可以將排程器用於各種商務案例：

* **定期資料清理**。 例如，每天刪除三個月之前的所有推文。
* **封存**。 例如，每個月將發票記錄推送到備份服務。
* **要求外部資料**。 例如，每 15 分鐘從 NOAA 提取新的滑雪氣象報告。
* **影像處理**。 例如，每個工作日的離峰時段期間，使用雲端運算來壓縮當天上傳的影像。

在本文中，我們會逐步引導您完成可使用排程器建立的範例作業。 我們提供描述每個排程的 JSON 資料。 如果您使用[排程器 REST API](https://msdn.microsoft.com/library/mt629143.aspx) \(英文\)，則可以使用相同的 JSON 來[建立排程器工作](https://msdn.microsoft.com/library/mt629145.aspx) \(英文\)。

## <a name="supported-scenarios"></a>支援的案例
本文中的範例說明排程器支援的案例範圍。 範例能廣泛說明如何建立適用於許多行為模式的排程，包括：

* 於特定日期和時間單次執行。
* 執行並重複特定次數。
* 立即執行並重複。
* 執行並從特定時間開始，每隔 *n* 分鐘、小時、天、週或個月重複執行一次。
* 執行並以每週或每月的頻率重複，但僅於當週的特定某天，或當月的特定某天。
* 執行並在期間內重複多次。 例如，在每個月的最後一個星期五和最後一個星期一執行，或是在每天的上午 5:15 和下午 5:15 執行。

## <a name="date-and-date-time"></a>日期和日期時間
排程器工作中的日期參考遵循 [ISO 8601 規格](http://en.wikipedia.org/wiki/ISO_8601)，並且只包含日期。

排程器工作中的日期時間參考遵循 [ISO 8601 規格](http://en.wikipedia.org/wiki/ISO_8601)，並且同時包含日期和時間。 系統會將未指定 UTC 時差的日期時間假設為 UTC。  

## <a name="use-json-and-the-rest-api-to-create-a-schedule"></a>使用 JSON 和 REST API 來建立排程
若要使用[排程器 REST API](https://msdn.microsoft.com/library/mt629143) \(英文\) 建立基本的排程，請先[向資源提供者註冊您的訂用帳戶](https://msdn.microsoft.com/library/azure/dn790548.aspx) \(英文\)。 排程器的提供者名稱是 **Microsoft.Scheduler**。 然後，請[建立工作集合](https://msdn.microsoft.com/library/mt629159.aspx) \(英文\)。 最後，請[建立作業](https://msdn.microsoft.com/library/mt629145.aspx) \(英文\)。 

當您建立作業時，可以使用 JSON 來指定排程與循環，如此範例所示：

    {
        "startTime": "2012-08-04T00:00Z", // Optional
         …
        "recurrence":                     // Optional
        {
            "frequency": "week",     // Can be "year", "month", "day", "week", "hour", or "minute"
            "interval": 1,                // How often to fire
            "schedule":                   // Optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // Optional (default to recur infinitely)
            "endTime": "2012-11-04",      // Optional (default to recur infinitely)
        },
        …
    }

## <a name="job-schema-basics"></a>作業結構描述的基本概念
下表提供您用於在作業中設定週期和排程之主要元素的高階概觀：

| JSON 名稱 | 說明 |
|:--- |:--- |
| **startTime** |日期時間值。 針對簡單的排程，**startTime** 為首次執行。 針對複雜的排程，作業一到 **startTime** 就會啟動。 |
| **recurrence** |指定作業的週期規則，以及作業執行的週期。 recurrence 物件支援下列元素：**frequency**、**interval**、**endTime**、**count** 和 **schedule**。 如果已定義 **recurrence**，就需要 **frequency**。 其他 **recurrence** 元素為選擇性。 |
| **frequency** |字串，代表作業重複頻率的單位。 支援的值有 "minute"、"hour"、"day"、"week" 和 "month"。 |
| **interval** |正整數。 **interval** 代表會決定工作多久執行一次之 **frequency** 值的間隔。 例如，如果 **interval** 為 3，而 **frequency** 為 "week"，則作業每隔三週重複執行一次。<br /><br />排程器支援的最大 **interval**，為最多 18 次的每月頻率、最多 78 次的每週頻率，以及最多 548 次的每日頻率。 對於 hour 和 minute 頻率，支援的範圍為 1 <= **interval** <= 1000。 |
| **endTime** |字串，指定超過之後就不執行作業的日期時間。 您可以將 **endTime** 的值設定為過去的時間。 如果未指定 **endTime** 和 **count**，則該作業會無限期執行。 您不能在同一個作業中包含 **endTime** 和 **count**。 |
| **count** |正整數 (大於零)，指定作業完成之前的執行次數。<br /><br />**count** 表示作業在被判斷為已完成之前的執行次數。 例如，針對每天執行的作業，若其 **count** 值為 5 且開始日期為星期一，則該作業會在星期五執行之後完成。 如果開始日期為過去日期，則會從建立時間計算第一次執行時間。<br /><br />如果未指定 **endTime** 或 **count**，則作業會無限期執行。 您不能在同一個作業中包含 **endTime** 和 **count**。 |
| **schedule** |具有指定頻率的工作會根據週期排程來改變其週期。 **schedule** 值包含根據分鐘數、小時數、週日數、月日數和週數的修改。 |

## <a name="job-schema-defaults-limits-and-examples"></a>作業結構描述的預設、限制及範例
我們稍後會在本文中詳細討論下列元素：

| JSON 名稱 | 值類型 | 必要？ | 預設值 | 有效值 | 範例 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** |字串 |否 |None |ISO 8601 日期時間 |`"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** |物件 |否 |None |recurrence 物件 |`"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **frequency** |字串 |yes |None |"minute"、"hour"、"day"、"week"、"month" |`"frequency" : "hour"` |
| **interval** |number |yes |None |1 到 1000 |`"interval":10` |
| **endTime** |字串 |否 |None |代表未來時間的日期時間值 |`"endTime" : "2013-02-09T09:30:00-08:00"` |
| **count** |number |否 |None |>= 1 |`"count": 5` |
| **schedule** |物件 |否 |None |schedule 物件 |`"schedule" : { "minute" : [30], "hour" : [8,17] }` |

## <a name="deep-dive-starttime"></a>深入探討：startTime
下表描述 **startTime** 如何控制作業的執行方式：

| startTime 值 | 無週期 | 週期、無排程 | 週期性有排程 |
|:--- |:--- |:--- |:--- |
| **没有開始時間** |立即執行一次。 |立即執行一次。 根據上次執行時間算出的時間執行後續的執行作業。 |立即執行一次。<br /><br />根據週期排程執行後續的執行作業。 |
| **過去的開始時間** |立即執行一次。 |計算開始時間之後的第一個未來執行時間，並在該時間執行。<br /><br />根據上次執行時間算出的時間執行後續的執行作業。 <br /><br />如需詳細資訊，請參閱此表格後面的範例。 |作業「一到」指定的開始時間即啟動。 第一次執行是根據從開始時間計算的排程。<br /><br />根據週期排程執行後續的執行作業。 |
| **在未來或目前時間的開始時間** |在指定的開始時間執行一次。 |在指定的開始時間執行一次。<br /><br />根據上次執行時間算出的時間執行後續的執行作業。|作業「一到」指定的開始時間即啟動。 第一次發生是根據排程，從開始時間計算。<br /><br />根據週期排程執行後續的執行作業。 |

讓我們看一個範例，了解當 **startTime** 是在過去且具有週期但沒有排程時，會發生什麼事。  假設目前的時間是 2015-04-08 13:00，**startTime** 是 2015-04-07 14:00，而 **recurrence** 是每二天 (以 **frequency**: day 和 **interval**: 2 定義)。請注意，**startTime** 是在過去，因此發生在目前時間之前。

根據這些條件，第一次執行將發生於 2015-04-09 的 14:00。 排程器引擎會從開始時間計算執行週期。 過去的任何執行個體都會遭到捨棄。 引擎會使用下一個在未來發生的執行個體。 在此案例中，**startTime** 為 2015-04-07 下午 2:00，所以下一個執行個體是在該時間的二天後，即 2015-04-09 下午 2:00。

請注意，無論 **startTime** 是 2015-04-05 14:00 或 2015-04-01 14:00\.，第一次執行的時間都相同。 在第一次執行之後，就會使用排程來算出後續的執行時間。 它們都會在 2015-04-11 下午 2:00 執行，然後在 2015-04-13 下午 2:00 執行，然後在 2015-04-15 下午 2:00 執行，依此類推。

最後，當作業具有排程時，如果未在排程中設定小時和分鐘，則該值會分別預設為第一次執行的小時和分鐘。

## <a name="deep-dive-schedule"></a>深入探討：排程
您可以使用 **schedule** 來*限制*作業執行的數目。 例如，如果作業的 **frequency** 為 "month" 且具有只在 31 日執行的排程，則該作業只在具有第三十一天的月份執行。

您也可以使用 **schedule** 來*擴充*作業執行的數目。 例如，如果作業的 **frequency** 為 "month" 且具有在當月 1 日和 2 日執行的排程，則該作業會在當月的第一天和第二天執行，而不是一個月只執行一次。

如果您指定了多個 schedule 元素，則評估的順序是從最大到最小：週數、月日、星期幾、小時和分鐘。

下表詳細說明 schedule 元素：

| JSON 名稱 | 說明 | 有效值 |
|:--- |:--- |:--- |
| **minutes** |作業在該小時的第幾分鐘執行。 |整數陣列。 |
| **hours** |作業在該日的第幾小時執行。 |整數陣列。 |
| **weekDays** |作業在該週的第幾天執行。 只能搭配 weekly 頻率指定。 |包含任何下列值的陣列 (最大的陣列大小為 7)：<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />不區分大小寫。 |
| **monthlyOccurrences** |決定將在當月哪幾天執行工作。 只能搭配 monthly 頻率指定。 |**monthlyOccurrences** 物件的陣列：<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day** 是作業在當週的第幾天執行。 例如，*{Sunday}* 是當月的每個星期日。 必要。<br /><br />**occurrence** 是當月期間的第幾天發生。 例如，*{Sunday, -1}* 是當月的最後一個星期日。 選用。 |
| **monthDays** |作業將在當月的哪一日執行。 只能搭配 monthly 頻率指定。 |包含下列值的陣列：<br />- <= -1 且 >= -31 的任何值<br />- >= 1 且 <= 31 的任何值|

## <a name="examples-recurrence-schedules"></a>範例：週期排程
下列範例顯示各種循環排程。 該範例著重於 schedule 物件和其子元素。

排程假設 **interval** 設定為 1\.。 此範例也會假設 **schedule** 中值的 **frequency** 值是正確的。 例如，在 **schedule** 中具有 **monthDays** 修改的情況下，您無法將 **frequency** 設為 "day" 。 我們稍早在文章中已描述這些限制。

| 範例 | 說明 |
|:--- |:--- |
| `{"hours":[5]}` |在每天的上午 5 點執行。<br /><br />排程器會將 "hours" 中的每個值與 "minutes" 中的每個值逐一比對，以建立一個清單，列出將執行工作的所有時間。 |
| `{"minutes":[15], "hours":[5]}` |在每天的上午 5:15 執行。 |
| `{"minutes":[15], "hours":[5,17]}` |在每天的上午 5:15 和下午 5:15 執行。 |
| `{"minutes":[15,45], "hours":[5,17]}` |在每天的上午 5:15、上午 5:45、下午 5:15 及下午 5:45 執行。 |
| `{"minutes":[0,15,30,45]}` |每隔 15 分鐘執行一次。 |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |每小時執行一次。<br /><br />這項工作每小時執行一次。 分鐘是由 **startTime** 的值控制 (若有指定)。 如果未指定 **startTime** 值，則分鐘是由建立時間控制。 比方說，如果開始時間或建立時間 (無論適用哪一個) 為下午 12:25，則工作將在 00:25、01:25、02:25、…、23:25 執行。<br /><br />該排程相當於具有 **frequency** 為 "hour"、**interval** 為 1 且沒有 **schedule** 值的作業。 差別在於，您可以使用此排程搭配不同的 **frequency** 和 **interval** 值來建立其他作業。 例如，如果 **frequency** 為 "month"，則該排程只會每個月執行一次，而不是每天 (如果 **frequency** 為 "day")。 |
| `{minutes:[0]}` |在每小時整點執行。<br /><br />此作業也會每小時執行，但會在整點執行 (上午 12 點、上午 1 點、上午 2 點，依此類推)。 這相當於具有 **frequency** 為 "hour"、**startTime** 值為零分鐘且沒有 **schedule** 的作業 (如果 frequency 為 "day")。 不過，如果 **frequency** 為 "week" 或 "month"，則排程分別只會一週或一個月執行一天。 |
| `{"minutes":[15]}` |在每小時整點過 15 分鐘後執行。<br /><br />每小時執行，從上午 00:15 開始，然後在上午 1:15、上午 2:15，依此類推。 它結束於下午 11:15。 |
| `{"hours":[17], "weekDays":["saturday"]}` |在每週於星期六的下午 5 點執行。 |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |在每週於星期一、星期三及星期五的下午 5 點執行。 |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |在每週於星期一、星期三及星期五的下午 5:15 和 5:45 執行。 |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |在每週於星期一、星期三及星期五的上午 5 點和下午 5 點執行。 |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |在每週於星期一、星期三及星期五的上午 5:15、上午 5:45、下午 5:15 和上午 5:45 執行。 |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |在工作日每隔 15 分鐘執行一次。 |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |在工作日的上午 9 點與下午 4:45 之間每隔 15 分鐘執行一次。 |
| `{"weekDays":["sunday"]}` |在星期日於開始時間執行。 |
| `{"weekDays":["tuesday", "thursday"]}` |在星期二和星期四於開始時間執行。 |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |在每個月的第二十八天於上午 6 點執行 (假設 **frequency** 為 "month")。 |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |在當月最後一天的上午 6 點執行。<br /><br />如果您要在當月最後一天執行工作，請使用 -1，而不是 28、29、30 或 31。 |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |在每個月第一天和最後一天的上午 6 點執行。 |
| `{monthDays":[1,-1]}` |在每個月第一天和最後一天於開始時間執行。 |
| `{monthDays":[1,14]}` |在每個月第一天和第十四天於開始時間執行。 |
| `{monthDays":[2]}` |在當月第二天於開始時間執行。 |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |在每個月第一個星期五的上午 5 點執行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |在每個月第一個星期五於開始時間執行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |在每個月倒數第三個星期五於開始時間執行。 |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |在每個月第一個和最後一個星期五的上午 5:15 執行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |在每個月第一個和最後一個星期五於開始時間執行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |在每個月第五個星期五於開始時間執行。<br /><br />如果當月沒有第五個星期五，該作業就不會執行。 如果您想要在當月最後一個出現的星期五執行作業，則可以考慮針對 occurrence 使用 -1，而不是 5。 |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |在月份中最後一個星期五每隔 15 分鐘執行一次。 |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |在每個月第三個星期三的上午 5:15、上午 5:45、下午 5:15 及下午 5:45 執行。 |

## <a name="see-also"></a>另請參閱

- [排程器是什麼？](scheduler-intro.md)
- [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)
- [在 Azure 入口網站中開始使用排程器](scheduler-get-started-portal.md)
- [Azure 排程器的計劃和計費](scheduler-plans-billing.md)
- [Azure 排程器 REST API 參考](https://msdn.microsoft.com/library/mt629143)
- [Azure 排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)
- [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)
- [Azure 排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)
- [Azure 排程器輸出驗證](scheduler-outbound-authentication.md)

