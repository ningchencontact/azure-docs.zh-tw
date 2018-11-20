---
title: 描述成人和猥褻內容：電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 偵測影像中成人和猥褻內容的相關概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 71866149e3d2dca4b39585ce8da73aae658a4d59
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344909"
---
# <a name="detecting-adult-and-racy-content"></a>偵測成人和猥褻內容

各種不同的視覺類別中包含成人和猥褻群組，可用來偵測成人內容及限制包含色情內容的影像顯示。 成人和猥褻內容偵測的篩選條件可用滑動標尺來設定，以配合使用者的喜好設定。

## <a name="defining-adult-and-racy-content"></a>定義成人和猥褻內容

在[分析影像方法](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) (英文) 涵蓋的多種視覺功能中，成人視覺功能可偵測成人和猥褻影像。 「成人」影像的定義是本質上為色情，而且通常會描繪裸露和性行為的影像。 「猥褻」影像的定義是本質上具有性暗示，而且相較於標記為「成人」的影像內容，通常包含內容較不露骨的影像。 成人視覺功能類型通常會用來限制顯示具有性暗示和露骨內容的影像。

## <a name="identifying-adult-and-racy-content"></a>識別成人和猥褻內容

分析影像方法會在該方法的 JSON 回應中傳回 `isAdultContent` 和 `isRacyContent` 兩個屬性，藉此分別表示成人和猥褻內容。 這兩個屬性會傳回 True 或 False 的布林值。 此方法也會傳回 `adultScore` 和 `racyScore` 兩個屬性，分別代表識別成人和猥褻內容的信賴分數。 成人和猥褻內容偵測的信賴篩選條件可根據特定案例使用滑動標尺來設定，以配合您的喜好設定。

## <a name="next-steps"></a>後續步驟

了解有關[偵測特定網域內容](concept-detecting-domain-content.md) (英文) 和[偵測臉部](concept-detecting-faces.md) (英文) 的相關概念。