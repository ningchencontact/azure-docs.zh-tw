---
title: 描述成人和猥褻內容 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 偵測影像中成人和猥褻內容的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a4c136e9c210dd40e004b8d5e1c1a2a8fceaa7
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312519"
---
# <a name="detect-adult-and-racy-content"></a>偵測成人和猥褻內容

電腦視覺可以偵測影像中的成人內容，使開發人員可以在其軟體中限制此類影像的顯示。 內容旗標會搭配介於零與一的分數套用，使開發人員可以根據自己的喜好對結果進行解譯。 

> [!NOTE]
> 此功能同時也由 [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) 服務提供。 此替代方案為適用於更嚴格內容仲裁案例 (例如文字仲裁和人為檢閱工作流程) 的解決方案。

## <a name="content-flag-definitions"></a>內容旗標定義

**成人**影像的定義是本質上為色情，而且通常會描繪裸露和性行為的影像。 

**猥褻**影像的定義是本質上具有性暗示，而且相較於標記為**成人**的影像內容，通常包含內容較不露骨的影像。 

## <a name="identify-adult-and-racy-content"></a>識別成人和猥褻內容

[分析](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) \(英文\) API。

分析影像方法會在該方法的 JSON 回應中傳回 `isAdultContent` 和 `isRacyContent` 兩個布林值屬性，分別代表成人和猥褻內容。 此方法也會傳回 `adultScore` 和 `racyScore` 兩個屬性，分別代表識別成人和猥褻內容的信賴分數。

## <a name="next-steps"></a>後續步驟

了解有關[偵測特定網域內容](concept-detecting-domain-content.md) (英文) 和[偵測臉部](concept-detecting-faces.md) (英文) 的相關概念。
