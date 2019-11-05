---
title: 建立 LUIS 資源
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465914"
---
## <a name="create-a-luis-resource"></a>建立 LUIS 資源

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 按一下 [[建立] **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. 輸入所有必要的設定：

    |設定|值|
    |--|--|
    |名稱|想要的名稱（2-64 個字元）|
    |訂用帳戶|選取適當的訂用帳戶|
    |位置|選取任何附近和可用的位置|
    |定價層|`F0`-最低定價層|
    |資源群組|選取可用的資源群組|

1. 按一下 [**建立**] 並等候資源建立。 建立之後，流覽至 [資源] 頁面。
1. 收集已設定的 `endpoint` 和 API 金鑰，請參閱[收集必要的參數](#gathering-required-parameters)。

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
