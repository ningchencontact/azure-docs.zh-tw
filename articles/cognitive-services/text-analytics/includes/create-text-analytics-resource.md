---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 了解如何建立認知服務文字分析資源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 022ffcd806d4d4f89f8a8cf256a541518ea12602
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455128"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>建立認知服務文字分析資源

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 選取  **+ 建立資源**，瀏覽至**AI + 機器學習服務 > 文字分析**，或按一下 [建立**文字分析**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. 輸入所有必要的設定：

    |設定|值|
    |--|--|
    |名稱|所需的名稱 （2 到 64 個字元）|
    |訂用帳戶|選取適當的訂用帳戶|
    |Location|選取的鄰近位置|
    |定價層|`S` -標準定價層|
    |資源群組|選取可用的資源群組|

1. 按一下 **建立**並等候要建立的資源。 在建立之後，您的瀏覽器將自動重新導向至新建立的資源頁面
1. 收集設定`endpoint`和 API 金鑰：

    |在入口網站中的資源 索引標籤|設定|值|
    |--|--|--|
    |**概觀**|端點|複製的端點。 它看起來類似於 `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**金鑰**|API 金鑰|將複製兩個索引鍵的 1。 它是 32 的英數字元字串，不含空格或連字號`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|