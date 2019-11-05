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
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522462"
---
## <a name="create-an-anomaly-detector-resource"></a>建立 Anomaly Detector 資源

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 按一下 [[建立**異常**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)偵測器資源]
1. 輸入所有必要的設定：

    |設定|值|
    |--|--|
    |名稱|想要的名稱（2-64 個字元）|
    |訂用帳戶|選取適當的訂用帳戶|
    |位置|選取任何附近和可用的位置|
    |定價層|`F0`-最低定價層|
    |資源群組|選取可用的資源群組|
    |預覽確認核取方塊（必要）|您是否已閱讀**預覽**通知|

1. 按一下 [**建立**] 並等候資源建立。 建立之後，流覽至 [資源] 頁面
1. 收集已設定的 `endpoint` 和 API 金鑰：

    |入口網站的 [資源] 索引標籤|設定|值|
    |--|--|--|
    |**概觀**|端點|複製端點。 看起來類似 `https://westus2.api.cognitive.microsoft.com/`|
    |**金鑰**|API 金鑰|複製這兩個金鑰的1。 它是32英數位元字串，不含空格或連字號，`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|



