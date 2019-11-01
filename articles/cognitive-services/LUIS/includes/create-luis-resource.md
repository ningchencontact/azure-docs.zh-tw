---
title: 容器支援
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: ecb172aec37fdab21bed8373ac7484dc52a54829
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821959"
---
## <a name="create-a-luis-resource"></a>建立 LUIS 資源

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 按一下 [[建立] **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. 輸入所有必要的設定：

    |設定|Value|
    |--|--|
    |Name|想要的名稱（2-64 個字元）|
    |Subscription|選取適當的訂用帳戶|
    |Location|選取任何附近和可用的位置|
    |價格層次|`F0`-最低定價層|
    |資源群組|選取可用的資源群組|

1. 按一下 [**建立**] 並等候資源建立。 建立之後，流覽至 [資源] 頁面
1. 收集已設定的 `endpoint` 和 API 金鑰：

    |入口網站的 [資源] 索引標籤|設定|Value|
    |--|--|--|
    |**Overview**|端點|複製端點。 看起來類似 `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**金鑰**|API 金鑰|複製這兩個金鑰的1。 它是32英數位元字串，不含空格或連字號，`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|
