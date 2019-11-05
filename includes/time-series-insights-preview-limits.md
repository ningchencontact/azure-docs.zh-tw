---
title: 包含檔案
description: 包含檔案
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/16/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: dfe16b4e965670d115cfa92f1cb3ca812d6375ad
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990833"
---
### <a name="general-availability-and-preview-comparison"></a>一般可用性和預覽比較

下表摘要說明 Azure 時間序列深入解析公開上市（GA）和預覽實例之間的幾個主要差異。

| | 正式上市 | 預覽 |
| --- | --- | ---|
| 第一級公民 | 以事件為中心 | 以時間為中心的 |
| 語義推理 | 低層級（參考資料） | 高階（模型） |
| 資料 coNtextualization | 非裝置層級 | 裝置和非裝置層級 |
| 計算邏輯儲存體 | 否 | 儲存在模型的型別變數部分 |
| 儲存體和存取控制 | 否 | 已透過模型啟用 |
| 匯總/取樣 | 否 | 事件加權和時間加權 |
| 信號重建 | 否 | 幀 |
| 衍生時間序列的生產環境 | 否 | 是、合併和聯結 |
| 語言彈性 | 不可組合 | 可組合 |
| 運算式語言 | 述詞字串 | 時間序列運算式（述詞字串、值、運算式和函數） |

### <a name="property-limits"></a>屬性限制

時間序列深入解析的屬性限制已從 GA 中的最大上限800增加至1000。 提供的事件屬性具有對應的 JSON、CSV 和圖表欄，您可以在[時間序列深入解析 Preview explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)中查看這些資料行。

| SKU | 最大屬性 |
| --- | --- |
| 預覽 PAYG | 1000屬性（資料行） |
| GA S1 | 600屬性（資料行） |
| GA S2 | 800屬性（資料行） |

### <a name="event-sources"></a>事件來源

每個實例最多可支援兩個事件來源。 

* 瞭解如何[新增事件中樞來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。
* 設定[IoT 中樞來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)。

### <a name="api-limits"></a>API 限制

時間序列深入解析 Preview 的 REST API 限制是在[REST API 參考檔](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)中指定。