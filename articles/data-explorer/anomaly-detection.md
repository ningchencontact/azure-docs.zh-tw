---
title: 時間序列異常偵測和 Azure 在資料總管中預測
description: 了解如何分析進行異常偵測和預測使用 Azure 資料的時間序列資料。
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: f40350129a12c7865051bcae80b74b6f9c069179
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233535"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>異常偵測和 Azure 在資料總管中預測

Azure 的資料總管，執行持續收集遙測資料從雲端服務或 IoT 裝置。 這項資料是針對各種不同的深入解析，例如，監視服務健康狀態、 實體的實際執行程序、 使用趨勢，以及負載預測分析。 選取的度量，以找出差模式相對於其一般正常基準模式的度量的時間序列進行分析。 Azure 的資料總管包含建立、 操作和多個時間序列分析的原生支援。 它可以建立和分析數千個時間序列，以秒為單位，啟用近乎即時監視解決方案和工作流程。

本文詳細說明 Azure 資料總管時間序列異常偵測和趨勢預測功能。 適用於時間序列函式以模型為基礎強固的已知分解，其中每個原始的時間序列分解成多季節性、 趨勢，以及剩餘的元件。 預測是藉由推斷季節性時所剩餘的元件，在極端值偵測到異常，和趨勢元件。 Azure 資料總管實作大幅增強了基本的分解模型自動季節性偵測、 穩固的極端值分析及向量化的實作來處理數以千計的時間序列以秒為單位。

## <a name="prerequisites"></a>必要條件

讀取[時間序列分析，Azure 在資料總管中的](/azure/data-explorer/time-series-analysis)有關時間序列功能的概觀。

## <a name="time-series-decomposition-model"></a>時間序列分解模型

Azure 的資料總管原生實作時間序列預測和異常偵測會使用已知的分解模型。 此模型會套用至時間序列的預期定期和趨勢的行為，例如服務流量、 元件的活動訊號和 IoT 預測未來的計量值，並偵測異常的週期性的度量資訊清單的計量。 此迴歸的程序假設是以外先前已知季節性和趨勢的行為，會隨機分散數列的時間。 您可以預測未來的計量值，從季節性和趨勢元件，以下合稱名為基準，並忽略的剩餘部分。 您也可以偵測到使用剩餘的部分的極端值分析為基礎的異常值。
若要建立分解的模型，請使用 函式[ `series_decompose()` ](/azure/kusto/query/series-decomposefunction)。 `series_decompose()`函式會採用一組時間序列，並自動分解每個要其季節性時間序列、 趨勢、 剩餘和基礎元件。 

例如，您可以將內部的 web 服務的流量，藉由使用下列查詢：

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![時間序列分解](media/anomaly-detection/series-decompose-timechart.png)

* 標示為原始的時間序列**num** （以紅色標示）。 
* 使用函式在處理序啟動的自動偵測季節性[ `series_periods_detect()` ](/azure/kusto/query/series-periods-detectfunction) ，並擷取**季節性**模式 （以紫色顯示）。
* 季節性模式會減去原始時間序列，並使用函式執行線性迴歸[ `series_fit_line()` ](/azure/kusto/query/series-fit-linefunction)來尋找**趨勢**元件 （以淺藍色）。
* 函式會減去趨勢和餘數**殘差**元件 （以綠色顯示）。
* 最後，將季節性函式，並趨勢元件，以產生**基準**（以藍色顯示）。

## <a name="time-series-anomaly-detection"></a>時間序列異常偵測

此函式[ `series_decompose_anomalies()` ](/azure/kusto/query/series-decompose-anomaliesfunction)時間序列的一組上發現異常的點。 此函式會呼叫`series_decompose()`來建置此分解模型，然後執行[ `series_outliers()` ](/azure/kusto/query/series-outliersfunction)上剩餘的元件。 `series_outliers()` 計算異常分數的每個剩餘的元件使用 Tukey 圍欄測試點。 1.5 高於或低於-1.5 的異常分數指出輕度異常升高，或分別拒絕。 3.0 上方或下方為-3.0 的異常分數表示強式的異常。 

下列查詢可讓您偵測異常的內部網頁服務流量：

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![時間序列異常偵測](media/anomaly-detection/series-anomaly-detection.png)

* 原始的時間序列 （以紅色標示）。 
* 基準 (季節性趨勢 +) 元件 （以藍色顯示）。
* （以紫色顯示） 上的原始時間序列異常點。 異常的點大幅偏離預期的基準值。

## <a name="time-series-forecasting"></a>時間序列預測

此函式[ `series_decompose_forecast()` ](/azure/kusto/query/series-decompose-forecastfunction)預測未來的一組的時間序列的值。 此函式會呼叫`series_decompose()`來建置模型，然後針對每個時間序列分解外推的基準元件到未來。

下列查詢可讓您預測下一週的 web 服務的流量：

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decmposition')
```

![時間序列預測](media/anomaly-detection/series-forecasting.png)

* 原有的度量 （以紅色標示）。 未來值遺漏，並設為 0，預設值。
* 進行推斷基準中的元件 （藍色），以預測下一週的值。

## <a name="scalability"></a>延展性

Azure 的資料總管查詢語言語法可讓處理多個時間序列的單一呼叫。 其唯一的最佳化的實作可讓快速的效能，也就是有效的異常偵測和預測時監視近乎即時的情境中計數器的數千個重要的。

下列查詢會同時顯示三個時間序列的處理：

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![時間序列延展性](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>總結

本文件詳述用於時間序列異常偵測和預測的原生 Azure 資料總管函式。 每個原始的時間序列分解成季節性、 趨勢和剩餘的元件偵測異常狀況及/或預測。 這些功能可以用於接近即時的監視案例，例如錯誤偵測、 預測性維護，以及需求和負載預測。

## <a name="next-steps"></a>後續步驟

深入了解[機器學習服務功能](/azure/data-explorer/machine-learning-clustering)Azure 在資料總管中。
