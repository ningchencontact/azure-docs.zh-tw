---
title: 容器支援
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b40f1833f08074cb0a8d45fe3afc6bac7cbac7f0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717050"
---
## <a name="create-an-anomaly-detector-resource"></a>建立異常偵測器資源

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 按一下  [Create**異常偵測器**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)資源
1. 輸入所有必要的設定：

    |設定|值|
    |--|--|
    |名稱|所需的名稱 （2 到 64 個字元）|
    |訂用帳戶|選取適當的訂用帳戶|
    |Location|選取任何鄰近及可用的位置|
    |定價層|`F0` -最小的定價層|
    |資源群組|選取可用的資源群組|
    |預覽確認核取方塊 （必要）|您已經讀過，zda bude**預覽**注意到|

1. 按一下 **建立**並等候要建立的資源。 在建立之後，瀏覽至 [資源] 頁面
1. 收集設定`endpoint`和 API 金鑰：

    |在入口網站中的資源 索引標籤|設定|值|
    |--|--|--|
    |**概觀**|端點|複製的端點。 它看起來類似於 `https://westus2.api.cognitive.microsoft.com/`|
    |**金鑰**|API 金鑰|將複製兩個索引鍵的 1。 它是 32 的英數字元字串，不含空格或連字號`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|



