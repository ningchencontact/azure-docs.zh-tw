---
title: 成人、猥褻、繁雜內容-電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 APi 偵測影像中成人內容的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718522"
---
# <a name="detect-adult-content"></a>偵測成人內容

電腦視覺可以偵測影像中的成人內容，讓開發人員可以限制在其軟體中顯示這些影像。 內容旗標會搭配介於零與一的分數套用，使開發人員可以根據自己的喜好對結果進行解譯。

> [!NOTE]
> 這項功能大部分是由[Azure 內容仲裁](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview)服務所提供。 此替代方案為適用於更嚴格內容仲裁案例 (例如文字仲裁和人為檢閱工作流程) 的解決方案。

## <a name="content-flag-definitions"></a>內容旗標定義

「成人」分類中有幾個不同的類別：

- **成人**影像會定義為本質上明確的性行為，而且通常會描繪裸體和性行為的行為。
- **猥褻**影像的定義是本質上具有性暗示，而且相較於標記為**成人**的影像內容，通常包含內容較不露骨的影像。
- **繁雜**影像會定義為描繪而且高爾的映射。

## <a name="use-the-api"></a>使用 API

您可以使用[分析影像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)API 來偵測成人內容。 當您將 `Adult` 的值新增至**visualFeatures**查詢參數時，API 會傳回三個布林屬性 @ no__t-2 @ no__t-3，`isRacyContent`，而 `isGoryContent` @ no__t-6IN 其 JSON 回應。 此方法也會傳回對應的屬性 @ no__t-0 @ no__t-1，`racyScore`，而 `goreScore` @ no__t-4which 代表每個個別類別的信賴分數，介於0到1之間。

- [快速入門：分析影像 (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [快速入門：分析影像 (REST API)](./quickstarts/csharp-analyze.md)
