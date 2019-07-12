---
title: Azure 串流分析中常見的查詢模式
description: 本文說明一些常見的查詢模式和設計，在 Azure 串流分析作業中很實用。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: 88df7ae0d4e6054d82302ad5f0adabcf656cb0f5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620811"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>一般串流分析使用模式的查詢範例

Azure 串流分析的查詢會以類似 SQL 的查詢語言表達。 語言建構記載在[串流分析查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)指南中。 

查詢設計，可以表示簡單的傳遞邏輯，以將事件資料從一個輸入資料流移至輸出資料存放區中，或它可以執行豐富的模式比對和時態性分析，以計算彙總中的各種時間範圍[建置 IoT使用 Stream Analytics 的解決方案](stream-analytics-build-an-iot-solution-using-stream-analytics.md)指南。 您可以將資料從多個輸入合併資料流的事件，而且您可以執行查閱，針對要擴充的事件值的靜態參考資料。 您也可以將資料寫入多個輸出中。

這篇文章概述幾個真實世界案例為基礎的常見查詢模式的解決方案。

## <a name="work-with-complex-data-types-in-json-and-avro"></a>在 JSON 和 AVRO 中使用複雜資料類型

Azure 串流分析可處理資料格式為 CSV、JSON 和 Avro 的事件。

JSON 和 Avro 可能包含巢狀物件 (記錄) 或陣列等複雜類型。 如需有關使用這些複雜的資料類型的詳細資訊，請參閱[剖析 JSON 和 AVRO 資料](stream-analytics-parsing-json.md)文章。

## <a name="query-example-convert-data-types"></a>查詢範例：轉換資料類型

**描述**：在輸入資料流上定義屬性類型。 比方說，汽車重量即將在輸入資料流為字串，而必須轉換成**INT**來執行**總和**。

**輸入**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**輸出**：

| 請確定 | Weight |
| --- | --- |
| Honda |3000 |

**解決方案**：

```SQL
    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**說明**：使用 [Weight]  欄位中的 **CAST** 陳述式來指定其資料類型。 請參閱[資料類型 (Azure 串流分析)](/stream-analytics-query/data-types-azure-stream-analytics) 中的支援資料類型清單。

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>查詢範例：使用 LIKE/NOT 等進行模式比對

**描述**：檢查事件上的欄位值是否符合特定模式。
例如，檢查結果是否會傳回開頭為 A 且結尾為 9 的車牌。

**輸入**：

| 請確定 | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**輸出**：

| 請確定 | LicensePlate | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**解決方案**：

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**說明**：使用 **LIKE** 陳述式檢查 [LicensePlate]  欄位值。 它應該以字母 A 開頭然後有任何零或多個字元的字串數字 9 結尾。 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>查詢範例：針對不同的案例/值指定邏輯 (CASE 陳述式)

**描述**：根據特定準則，針對欄位提供不同的計算方式。 例如，針對通過的相同廠牌汽車中有多少部符合 1 的特殊情況提供字串描述。

**輸入**：

| 請確定 | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**輸出**：

| CarsPassed | Time |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**解決方案**：

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**說明**：**CASE** 運算式會比較運算式和一組簡單運算式來決定結果。 在此範例中，計數為 1 的車輛傳回的字串描述與計數並非為 1 的車輛所傳回者不同。

## <a name="query-example-send-data-to-multiple-outputs"></a>查詢範例：將資料傳送至多個輸出

**描述**：將資料從單一作業傳送到多個輸出目標。 例如，分析臨界值警示的資料，並將所有事件封存到 Blob 儲存體。

**輸入**：

| 請確定 | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**：

| 請確定 | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**：

| 請確定 | Time | Count |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**解決方案**：

```SQL
    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp() AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3
```

**說明**：**INTO** 子句會告訴串流分析要從此陳述式將資料寫入哪個輸出。 第一個查詢是傳遞至名為輸出接收的資料**ArchiveOutput**。 第二個的查詢會執行一些簡單的彙總及篩選，並將結果傳送至下游的警示系統**AlertOutput**。

請注意，您也可以在多個輸出陳述式中重複使用通用資料表運算式 (CTE) 的結果 (例如 **WITH** 陳述式)。 此選項多了一項優點，就是對輸入來源開放的讀取器較少。

例如: 

```SQL
    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'
```

## <a name="query-example-count-unique-values"></a>查詢範例：計算唯一值

**描述**：計算在某個時間範圍內於串流中出現之唯一欄位值的數目。 例如，在 2 秒鐘時間範圍內有多少部某一獨特廠牌的汽車通過收費亭？

**輸入**：

| 請確定 | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**輸出：**

| CountMake | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**解決方案：**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**說明：** 
**COUNT(DISTINCT Make)** 會傳回一個時間範圍內 **Make** 資料行的相異值數目。

## <a name="query-example-determine-if-a-value-has-changed"></a>查詢範例：判斷值是否已變更

**描述**：查看前一個值來判斷該值是否與目前的值不同。 例如收費道路上前一輛汽車的品牌，是否與目前汽車的品牌相同？

**輸入**：

| 請確定 | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**輸出**：

| 請確定 | Time |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**解決方案**：

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**說明**：使用 **LAG** 查看前一個事件的輸入資料流，並取得 **Make** 值。 然後將其和目前事件中的 **Make** 值比較，並且在兩者不同時輸出事件。

## <a name="query-example-find-the-first-event-in-a-window"></a>查詢範例：尋找時間範圍內的第一個事件

**描述**：每隔 10 分鐘尋找該時間範圍內的第一輛車。

**輸入**：

| LicensePlate | 請確定 | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**輸出**：

| LicensePlate | 請確定 | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**解決方案**：

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1
```

現在讓我們變更問題，並每隔 10 分鐘的間隔中尋找特定廠牌的第一輛車。

| LicensePlate | 請確定 | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**解決方案**：

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

## <a name="query-example-find-the-last-event-in-a-window"></a>查詢範例：尋找時間範圍內的最後一個事件

**描述**：每隔 10 分鐘尋找該時間範圍內的最後一輛車。

**輸入**：

| LicensePlate | 請確定 | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**輸出**：

| LicensePlate | 請確定 | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**解決方案**：

```SQL
    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime
```

**說明**：查詢中有兩個步驟。 第一個步驟會尋找 10 分鐘時間範圍內最新的時間戳記。 第二個步驟會將第一個查詢的結果與原始串流聯結在一起，在每個時間範圍內尋找符合最後一個時間戳記的事件。 

## <a name="query-example-detect-the-absence-of-events"></a>查詢範例：偵測不存在的事件

**描述**：檢查串流中是否沒有和特定準則相符的值。
例如，在最後的 90 秒內連續有 2 部相同廠牌的車輛進入收費道路？

**輸入**：

| 請確定 | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**輸出**：

| 請確定 | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**解決方案**：

```SQL
    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**說明**：使用 **LAG** 查看前一個事件的輸入資料流，並取得 **Make** 值。 將該值和目前事件中的 **MAKE** 值比較，如果相同則輸出該事件。 您也可以使用 **LAG** 取得上一輛車的相關資料。

## <a name="query-example-detect-the-duration-between-events"></a>查詢範例：偵測事件之間的持續時間

**描述**：尋找指定事件的持續時間。 例如，指定某一網頁點選流，以判斷在某一功能上花費的時間。

**輸入**：  

| 使用者 | 功能 | Event - 事件 | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Start |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**輸出**：  

| 使用者 | 功能 | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**解決方案**：

```SQL
    SELECT
        [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
```

**說明**：使用 **LAST** 函式來擷取事件類型為 **Start** 時的最後一個 **TIME** 值。 **LAST** 函式使用 **PARTITION BY [user]** 來表達會為個別使用者分別計算結果。 查詢的 **Start** 與 **Stop** 事件之間時間差的上限為 1 小時，但該值可以在必要時變更 **(LIMIT DURATION(hour, 1)** 。

## <a name="query-example-detect-the-duration-of-a-condition"></a>查詢範例：偵測某個條件的持續時間
**描述**：找出某個條件的持續時間。
例如，假設有個錯誤導致所有車輛的重量不正確 (超過 20,000 磅)，而且必須計算該錯誤的持續時間。

**輸入**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**輸出**：

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**解決方案**：

```SQL
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
```

**說明**：使用 **LAG** 來檢視 24 小時內的輸入資料流，並尋找其中 **StartFault** 和 **StopFault** 介於 weight (重量) < 20000 的案例。

## <a name="query-example-fill-missing-values"></a>查詢範例：填入遺漏值

**描述**：針對有遺漏值的事件串流，產生具有固定間隔的事件串流。 例如，每隔 5 秒產生事件，報告最近所見的資料點。

**輸入**：

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**輸出 (前 10 個資料列)** ：

| windowend | lastevent.t | lastevent.value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**解決方案**：

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**說明**：此查詢會每隔 5 秒產生事件，並輸出先前所收到的最後一個事件。 [跳動視窗](/stream-analytics-query/hopping-window-azure-stream-analytics)持續時間會決定回溯到多久此查詢會尋找以找出最新的事件 （在此範例中的 300 秒）。


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>查詢範例：將相同串流內的兩個事件類型相互關聯

**描述**：有時會需要根據在特定時間範圍內發生的多個事件類型來產生警示。 例如，在家用烤爐的 IoT 案例中，風扇溫度低於 40 且過去 3 分鐘的最大功率低於 10 時，必須發出警示。

**輸入**：

| time | deviceId | sensorName | value |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | "power" |8 |

**輸出**：

| eventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Short circuit heating elements" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Short circuit heating elements" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Short circuit heating elements" |15 |

**解決方案**：

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp() AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
```

**說明**：第一個查詢 `max_power_during_last_3_mins` 會使用[滑動視窗](/stream-analytics-query/sliding-window-azure-stream-analytics) \(英文\) 來尋找過去 3 分鐘內每個裝置的功率感應器最大值。 系統會將第二個查詢加入第一個查詢，以找出目前事件最近相關時間範圍內的功率值。 然後，假如條件符合，就會針對裝置產生警示。

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>查詢範例：獨立於裝置時鐘誤差 (子串流) 的處理事件

**描述**：事件會因事件產生器之間的時鐘誤差、分割之間的時鐘誤差或網路延遲，而導致延遲發生或順序錯誤。 在下列範例中，TollID 2 的裝置時鐘為 TollID 1 背後的五秒且裝置時鐘 TollID 3 是 10 秒背後 TollID 1。 

**輸入**：

| LicensePlate | 請確定 | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000Z | 3 |

**輸出**：

| TollID | Count |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**解決方案**：

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**說明**：[TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) \(英文\) 子句會使用子串流個別查看每個裝置時間軸。 計算時會產生每個 TollID 的輸出事件，這表示事件的順序均與每個 TollID 有關，而不會重新排列順序，就像所有裝置都依據同一個時鐘一般。

## <a name="query-example-remove-duplicate-events-in-a-window"></a>查詢範例：移除時間範圍內的重複事件

**描述**：執行如計算指定時間範圍內事件平均值的作業時，應該要將重複的事件篩選出來。 在下列範例中，第二個事件是第一個重複的。

**輸入**：  

| deviceId | Time | 屬性 | 值 |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |溫度 |50 |
| 1 |2018-07-27T00:00:01.0000000Z |溫度 |50 |
| 2 |2018-07-27T00:00:01.0000000Z |溫度 |40 |
| 1 |2018-07-27T00:00:05.0000000Z |溫度 |60 |
| 2 |2018-07-27T00:00:05.0000000Z |溫度 |50 |
| 1 |2018-07-27T00:00:10.0000000Z |溫度 |100 |

**輸出**：  

| AverageValue | deviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**解決方案**：

```SQL
With Temp AS (
    SELECT
        COUNT(DISTINCT Time) AS CountTime,
        Value,
        DeviceId
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Value,
        DeviceId,
        SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**說明**：[COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics) 會傳回時間範圍內 Time 資料行中的相異值數目。 您可以接著使用此步驟的輸出，透過捨棄重複項目來計算每個裝置的平均值。

## <a name="geofencing-and-geospatial-queries"></a>地理圍欄和地理空間查詢
Azure Stream Analytics 提供內建的地理空間函式，可用來實作案例，例如車隊管理，寫共用、 連網汽車及資產追蹤。 地理空間資料可以內嵌 GeoJSON 或 well-known text，WKT 格式，為事件資料流的一部分，或參考資料。 如需詳細資訊，請參閱[地理柵欄和地理空間彙總案例中的使用 Azure Stream Analytics](geospatial-scenarios.md)文章。

## <a name="language-extensibility-through-javascript-and-c"></a>透過 JavaScript 語言擴充性和C#
Azure Stream Ananlytics 查詢 langugae 可以擴充以 JavaScript 撰寫的自訂函式或C#語言。 如需詳細資訊，請參閱一併發行項：
* [Azure Stream Analytics JavaScript 使用者定義函式](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics JavaScript 使用者定義彙總](stream-analytics-javascript-user-defined-aggregates.md)
* [開發.NET Standard 的使用者定義函式，如 Azure Stream Analytics Edge 作業](stream-analytics-edge-csharp-udf-methods.md)

## <a name="get-help"></a>取得說明

如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

