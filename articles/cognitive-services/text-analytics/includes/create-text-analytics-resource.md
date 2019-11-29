---
title: 建立認知服務文字分析資源
titleSuffix: Azure Cognitive Services
description: 了解如何建立認知服務文字分析資源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383484"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>建立認知服務文字分析資源

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [**建立資源**]，然後移至 [ **AI + Machine Learning** > **文字分析**]。
   或者，移至 [[建立文字分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)]。
1. 輸入所有必要的設定：

    |設定|Value|
    |--|--|
    |Name|輸入名稱（2-64 個字元）。|
    |Subscription|選取適當的訂用帳戶。|
    |Location|選取附近的位置。|
    |價格層| 輸入 **，** 標準定價層。|
    |Resource group|選取可用的資源群組。|

1. 選取 [**建立**]，並等候資源建立。 您的瀏覽器會自動重新導向至新建立的資源網頁。
1. 收集已設定的 `endpoint` 和 API 金鑰：

    |入口網站的 [資源] 索引標籤|設定|Value|
    |--|--|--|
    |**Overview**|端點|複製端點。 它看起來類似 `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`。|
    |**金鑰**|API 金鑰|複製這兩個金鑰的其中一個。 它是32字元的英數位元字串，不含空格或破折號： <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>。|
