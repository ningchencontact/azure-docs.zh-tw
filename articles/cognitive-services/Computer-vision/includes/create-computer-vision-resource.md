---
title: 容器支援
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499081"
---
## <a name="create-an-computer-vision-resource"></a>建立電腦視覺資源

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 按一下 [[建立**電腦視覺**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)資源]。
1. 輸入所有必要的設定：

    |設定|值|
    |--|--|
    |名稱|想要的名稱（2-64 個字元）|
    |訂用帳戶|選取適當的訂用帳戶|
    |位置|選取任何附近和可用的位置|
    |定價層|`F0`-最低定價層|
    |資源群組|選取可用的資源群組|

1. 按一下 [**建立**] 並等候資源建立。 建立之後，流覽至 [資源] 頁面。
1. 收集已設定的 `{ENDPOINT_URI}` 和 `{API_KEY}`，請參閱[收集必要參數](../computer-vision-how-to-install-containers.md#gathering-required-parameters)以取得詳細資料。
