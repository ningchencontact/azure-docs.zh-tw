---
title: Azure 串流分析中的異常偵測 (預覽)
description: 本文說明如何一起使用 Azure 串流分析和 Azure Machine Learning 來偵測異常行為。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.custom: seodec18
ms.openlocfilehash: 6f1b40e6224b9ed872700d526b954c605159d6bf
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789616"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure 串流分析中的異常偵測

Azure 串流分析 (在雲端和 Azure IoT Edge 中都可取得) 提供內建的機器學習型異常偵測功能，可用來監視兩個最常出現的異常狀況：暫存和持續性。 利用 **AnomalyDetection_SpikeAndDip** 和 **AnomalyDetection_ChangePoint** 函式，您可以直接在串流分析作業中執行異常偵測。

機器學習模型假設有一個均勻取樣的時間序列。 如果此時間序列不均勻，您可以在呼叫異常偵測之前，透過輪轉視窗插入彙總步驟。

機器學習作業不支援季節性趨勢或多變量的相互關聯。

## <a name="model-accuracy-and-performance"></a>模型精確度和效能

一般而言，在滑動視窗中使用更多資料可改善模型的精確度。 所指定滑動視窗中的資料會被視為屬於該時間範圍的正常值範圍。 此模型只會考慮整個滑動視窗的事件歷程記錄，以檢查目前的事件是否異常。 隨著滑動視窗移動，系統會從模型的訓練中收回舊值。

函式的運作方式為根據其目前為止所看到的內容建立某種標準。 與所建立的標準進行比較 (在信賴等級內)，藉此識別極端值。 視窗大小應該以針對正常行為定型模型所需的事件數目下限為基礎，如此在發生異常狀況時，才能夠加以辨識。

請記住，模型的回應時間會隨著歷程記錄大小而增加，因為它需要與較高的過去事件數目進行比較。 建議只包含所需的事件數目，以獲得較佳效能。

時間序列中的間距可能是未能在特定時間點及時收到事件的模型結果。 串流分析會使用插補來處理這種情況。 相同滑動視窗的歷程記錄大小，以及持續時間用來計算事件預計抵達的的平均速率。

## <a name="spike-and-dip"></a>峰值和谷值

時間序列事件資料流中的暫存異常狀況也稱為峰值和谷值。 您可以使用 Machine Learning 型運算子[AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
) 來監視峰值和谷值。

![峰值和谷值異常的範例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

在相同的滑動視窗中，如果第二個峰值小於第一個峰值，則較小峰值的計算分數可能不足以與指定的信賴等級內第一個峰值的分數比較。 您可以嘗試降低模型的信賴等級設定，以攔截這類異常狀況。 不過，如果您開始取得太多警示，則可使用較高的信賴區間。

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

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>在 Azure Stream Analytics 中使用機器學習服務異常偵測

下列影片示範如何使用 Azure Stream Analytics 中的 machine learning 函式的即時偵測異常狀況。 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

