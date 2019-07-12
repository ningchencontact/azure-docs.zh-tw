---
title: Azure 串流分析中的異常偵測
description: 本文說明如何一起使用 Azure 串流分析和 Azure Machine Learning 來偵測異常行為。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: e2fd226f1c605821f0fd595832b2cbe26d994fb4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612345"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure 串流分析中的異常偵測

Azure 串流分析 (在雲端和 Azure IoT Edge 中都可取得) 提供內建的機器學習型異常偵測功能，可用來監視兩個最常出現的異常狀況：暫存和持續性。 利用 **AnomalyDetection_SpikeAndDip** 和 **AnomalyDetection_ChangePoint** 函式，您可以直接在串流分析作業中執行異常偵測。

機器學習模型假設有一個均勻取樣的時間序列。 如果此時間序列不均勻，您可以在呼叫異常偵測之前，透過輪轉視窗插入彙總步驟。

機器學習服務作業不支援季節性趨勢或多重變量相互關聯這一次。

## <a name="model-behavior"></a>模型行為

一般而言，在滑動視窗中使用更多資料可改善模型的精確度。 所指定滑動視窗中的資料會被視為屬於該時間範圍的正常值範圍。 此模型只會考慮整個滑動視窗的事件歷程記錄，以檢查目前的事件是否異常。 隨著滑動視窗移動，系統會從模型的訓練中收回舊值。

函式的運作方式為根據其目前為止所看到的內容建立某種標準。 與所建立的標準進行比較 (在信賴等級內)，藉此識別極端值。 視窗大小應該以針對正常行為定型模型所需的事件數目下限為基礎，如此在發生異常狀況時，才能夠加以辨識。

模型的回應時間會隨著歷程記錄的大小，因為它需要用來比較過去的事件數目。 建議只包含所需的事件數目，以獲得較佳效能。

時間序列中的間距可能是未能在特定時間點及時收到事件的模型結果。 Stream Analytics 中使用插補邏輯會處理這種情況。 相同滑動視窗的歷程記錄大小，以及持續時間用來計算事件預計抵達的的平均速率。

可用的異常狀況產生器[此處](https://aka.ms/asaanomalygenerator)可用來摘要資料的不同的異常模式的 Iot 中樞。 ASA 工作可以設定與這些異常偵測函式來讀取此 Iot 中樞，並偵測異常狀況。

## <a name="spike-and-dip"></a>峰值和谷值

時間序列事件資料流中的暫存異常狀況也稱為峰值和谷值。 您可以使用 Machine Learning 型運算子[AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
) 來監視峰值和谷值。

![峰值和谷值異常的範例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

在相同的滑動視窗中，如果第二個峰值小於第一個峰值，則較小峰值的計算分數可能不足以與指定的信賴等級內第一個峰值的分數比較。 您可以嘗試降低模型的信心層級，來偵測這類異常狀況。 不過，如果您開始取得太多警示，則可使用較高的信賴區間。

下列範例查詢假設在歷程記錄有 120 個事件的 2 分鐘滑動視窗中，每秒一個事件的均勻輸入速率。 最終的 SELECT 陳述式會擷取並輸出 95% 信賴等級的分數和異常狀態。

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>變更點

時間序列事件資料流中的持續性異常狀況是事件資料流中值分佈的變更，如等級變更和趨勢。 在串流分析中，使用 Machine Learning 型 [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) 運算子偵測這類異常。

持續性變更會比峰值和谷值持續更久，並可能表示發生重大事件。 肉眼通常無法看見持續性變更，但可以使用 **AnomalyDetection_ChangePoint** 運算子進行偵測。

下圖是等級變更的範例：

![等級變更異常的範例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

下圖是趨勢變更的範例：

![趨勢變更異常的範例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

下列範例查詢假設在歷程記錄大小為 1200 個事件的 20 分鐘滑動視窗中，每秒一個事件的均勻輸入速率。 最終的 SELECT 陳述式會擷取並輸出 80% 信賴等級的分數和異常狀態。

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>效能特性

這些模型的效能取決於歷程記錄的大小、 視窗持續期間、 事件負載，以及是否使用函式層級資料分割。 本節會討論這些設定，並提供如何維持 1k 5k 及每秒 10k 事件的擷取速率的範例。

* **歷程記錄的大小**-這些模型使用執行線性**歷程記錄的大小**。 時間歷程記錄的大小，較長的模型會採用要評分的新事件。 這是因為模型比較與過去的事件記錄緩衝區中的每個新的事件。
* **視窗持續期間**-**視窗持續期間**應該反映收到所指定的記錄大小的多個事件需要多久。 沒有在視窗中的許多事件，Azure Stream Analytics 會推算遺漏值。 因此，CPU 耗用量是記錄大小的函式。
* **事件負載**-越大**事件負載**，多工作，就會執行的模型，其會影響 CPU 耗用率。 作業可相應放大，從而窘迫平行的假設是合理的商務邏輯，以使用更多的輸入資料分割。
* **函式層級資料分割** - **函式層級資料分割**是藉由使用```PARTITION BY```異常偵測函式呼叫中。 這種類型的資料分割會加入額外負荷，因為狀態需要維護多個模型，在相同的時間。 使用案例，例如裝置層級資料分割中的函式層級分割。

### <a name="relationship"></a>關聯性
以下列方式相關的歷程記錄的大小、 視窗持續期間，與總事件負載：

windowDuration （以毫秒為單位） = 1000年 * historySize / (總輸入事件每秒 / 輸入分割區計數)

當 deviceid，資料分割函數，將 「 分割 DEVICEID 」 的異常偵測函式呼叫。

### <a name="observations"></a>觀察
下表包含非資料分割案例的單一節點 (6 個 SU) 的輸送量觀察值：

| 記錄大小 （事件） | 視窗持續期間 （毫秒） | 每秒的輸入的事件總數 |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

下表包含資料分割案例的單一節點 (6 個 SU) 的輸送量觀察值：

| 記錄大小 （事件） | 視窗持續期間 （毫秒） | 每秒的輸入的事件總數 | 裝置計數 |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

若要執行上述的非資料分割組態的範例程式碼位於[串流在擴展存放庫](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh)的 Azure 範例。 程式碼會建立串流分析作業中的任何函式層級資料分割，會使用事件中樞作為輸入和輸出。 輸入的負載會產生使用測試用戶端。 每個輸入的事件是 1KB json 文件。 事件模擬 IoT 裝置傳送 JSON 資料 （適用於最多 1 K 裝置）。 記錄大小、 視窗持續期間和事件總負載會透過 2 個輸入分割區而變動。

> [!Note]
> 更精確的預估值，自訂以符合您案例的範例。

### <a name="identifying-bottlenecks"></a>找出瓶頸
使用 Azure Stream Analytics 作業中的 [計量] 窗格中，找出您的管線中的瓶頸。 檢閱**輸入/輸出事件**輸送量並[浮水印延遲 」](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)或**待處理項目事件**若要查看工作是否跟輸入速率。 針對事件中樞計量，尋求**節流要求**並據以調整臨界值的單位。 針對 Cosmos DB 計量，請檢閱**針對每個資料分割索引鍵範圍取用 RU/秒上限**下輸送量，以確保您的資料分割索引鍵範圍一致的方式取用。 適用於 Azure SQL DB，監視**記錄 IO**並**CPU**。

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>在 Azure Stream Analytics 中使用機器學習服務異常偵測

下列影片示範如何使用 Azure Stream Analytics 中的 machine learning 函式的即時偵測異常狀況。 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

