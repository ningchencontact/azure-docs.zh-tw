---
title: 使用異常偵測器 API 的最佳做法
description: 偵測異常狀況與異常偵測器 API 時，請了解最佳做法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 467ac4e475a1c23e25b62c76cfbc959e7ed49465
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484027"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>使用異常偵測器 API 的最佳做法

異常偵測器 API 是無狀態的異常偵測服務。 精確度和效能，其結果可能會影響：

* 如何準備您的時間序列資料。
* 異常偵測器 API 參數使用。
* 在您的 API 要求中的資料點數目。 

您可以使用本文來了解使用 API 為您的資料得到最佳效果的最佳做法。 

## <a name="data-preparation"></a>資料準備

異常偵測器 API 可接受的時間序列資料格式化為 JSON 的要求物件。 時間序列可以是任何一段時間循序記錄的數值資料。 您可以傳送的時間序列資料的 windows，異常偵測器 API 端點來提升 API 效能。 您可以傳送的資料點的數目下限為 12，和最大值是 8640 點。 

傳送到的異常偵測器 API 的資料點必須要有有效的 Coordinated Universal Time (UTC) 時間戳記和數值。 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

### <a name="missing-data-points"></a>遺漏的資料點

遺漏的資料點都有平均分佈的時間序列資料集，特別是與微細資料粒度 （小型的取樣間隔。 例如，資料取樣每隔幾分鐘)。 遺漏預期的資料中的點數目小於 10%，不應在偵測結果有負面影響。 請考慮填滿您根據它的特性，例如取代從先前的期間、 線性插補或將移動平均的資料點的資料中的間距。

### <a name="aggregate-distributed-data"></a>彙總的分散式的資料

異常偵測器 API 最適合的平均分佈的時間序列。 如果您的資料會隨機分散，您應該彙總的時間，例如每分鐘，每小時或每日的單位，例如。

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>對具有季節性模式的資料的異常偵測

如果您知道您的時間序列資料有季節性模式 （其中定期發生），您可以改善的精確度和 API 回應時間。 

指定`period`JSON 要求在建構時可以減少異常偵測延遲最多 50%。 `period`是整數，指定時間序列的大約多少資料點會以重複模式。 比方說，會有一個資料點，每日使用時間序列`period`作為`7`，而且必須與每小時 （使用相同的每週模式） 的一個點的時間序列`period`的`7*24`。 如果您不確定的您的資料模式，您不需要指定此參數。

為了獲得最佳結果，提供 4`period`的資料點，再加上，另一個的價值。 比方說，每小時的資料，每週的模式與上面所述應提供 673 要求主體中的資料點 (`7 * 24 * 4 + 1`)。

### <a name="sampling-data-for-real-time-monitoring"></a>即時監視的取樣資料

如果您的串流資料進行取樣在短時間內 （例如秒或分鐘），傳送資料點的建議的數目可能會超出異常偵測器 API 的最大允許 （8640 資料點數目）。 如果您的資料顯示穩定的季節性模式，請考慮在較大的時間間隔，例如小時中傳送的時間序列資料的範例。 取樣資料以這種方式可以也明顯改善 API 回應時間。 

## <a name="next-steps"></a>後續步驟

* [什麼是異常偵測器 API？](../overview.md)
* [快速入門：偵測異常的時間序列資料使用異常偵測器 REST API](../quickstarts/detect-data-anomalies-csharp.md)
