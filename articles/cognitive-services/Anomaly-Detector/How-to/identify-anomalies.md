---
title: 如何使用您的時間序列資料的異常偵測器 API
description: 了解如何在您的資料中偵測異常行為，以批次，或對串流資料。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 63ede8fe90d5c19c2473ffb315bf6096599ffb9c
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473449"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>作法：使用您的時間序列資料的異常偵測器 API  

[異常偵測器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)提供兩種方法的異常偵測。 您可以偵測異常狀況視為一個批次在您的時間序列，或藉由偵測異常的狀態最新的資料點產生您的資料。 偵測模型會傳回異常結果，以及每個資料點的預期值和上限和下限的異常偵測界限。 您可以使用這些值，以視覺化方式檢視的範圍，一般的值，以及在資料中的異常狀況。

## <a name="anomaly-detection-modes"></a>異常偵測模式 

異常偵測器 API 提供偵測模式： 批次和串流處理。

> [!NOTE]
> Url 必須與您訂用帳戶的適當端點結合下列的要求。 例如：`https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>批次偵測

若要偵測異常狀況，整個批次的資料點在給定的時間範圍內，使用時間序列資料中的下列要求 URI: 

`/timeseries/entire/detect` 。 

藉由同時傳送時間序列資料，API 會使用整個系列中，產生模型，並分析與其每個資料點。  

### <a name="streaming-detection"></a>資料流的偵測

若要持續偵測異常狀況對串流資料，使用最新的資料點中的下列要求 URI: 

`/timeseries/last/detect'` 。 

藉由在產生它們，請傳送新的資料點，您可以監視您即時的資料。 將與資料點在傳送時，產生的模型和 API 會判斷為時間序列中最新的點是否異常狀況。

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>調整的下限和上限的異常偵測界限

根據預設，上限和下限之間，以便進行異常偵測使用計算`expectedValue`， `upperMargin`，和`lowerMargin`。 如果您需要不同的界限時，我們建議您套用`marginScale`要`upperMargin`或`lowerMargin`。 界限會計算方式如下：

|界限  |計算  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

下列範例會示範了異常偵測器 API 的結果，在不同的特性。

### <a name="example-with-sensitivity-at-99"></a>在 99 的區分大小寫的範例

![預設敏感度](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>在 95 的區分大小寫的範例

![99 的敏感度](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>在 85 的區分大小寫的範例

![85 的敏感度](../media/sensitivity_85.png)

## <a name="next-steps"></a>後續步驟

* [什麼是異常偵測器 API？](../overview.md)
* [快速入門：偵測異常的時間序列資料使用異常偵測器 REST API](../quickstarts/detect-data-anomalies-csharp.md)