---
title: 反覆的應用程式設計
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 在模型變更、語句範例、發佈及從端點查詢收集資料的反覆循環中學習時，成效最佳。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 67bcb33727bc808f5e5bea701daffc77dde736ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813779"
---
# <a name="authoring-cycle-for-your-luis-app"></a>撰寫 LUIS 應用程式的循環
LUIS 在模型變更、語句範例、發佈及從端點查詢收集資料的反覆循環中學習時，成效最佳。 

![撰寫循環](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>建置 LUIS 模型
模型的目的是要查明使用者想要什麼 (意圖)，以及問題的哪些部分提供有助於判斷解答的詳細資料 (實體)。 

模型必須是應用程式定義域特定的，才能判斷相關的單字和語句，以及一般的單字排序。 

模型包含意圖、實體。 

## <a name="add-training-examples"></a>新增定型範例
LUIS 需要意圖中有範例語句。 這些範例需要有足夠的單字選擇和單字順序變化，才能判斷語句的意圖為何。 每個範例語句都必須將任何必要的資料標記為實體。 

您需指示 LUIS 忽略與應用程式定義域無關的語句，方法是將該語句指派給 **None** 意圖。 無須標記任何您不需要從語句中提取的單字或片語。 無須標記要忽略的單字或片語。 

## <a name="train-and-publish-the-app"></a>將應用程式定型並發佈
在每個意圖都有 10 到 15 個不同語句，並標記必要實體之後，進行訓練和發佈。 使用發佈成功通知中的連結來取得您的端點。 請務必建立您的應用程式並將其發佈，如此應用程式才能在您所需的[端點區域](luis-reference-regions.md)中可供使用。 

## <a name="https-endpoint-testing"></a>HTTPS 端點測試
您可以從 HTTPS 端點測試您的 LUIS 應用程式。 從端點測試可讓 LUIS 選擇任何低信賴度的語句來進行檢閱。  

## <a name="recycle"></a>再循環
當您完成撰寫循環時，可以重新開始。 請從檢閱 LUIS 標示為低信賴度的端點語句開始著手。 檢查這些語句是否有意圖和實體。 檢閱語句之後，檢閱清單應該會空白。  

## <a name="batch-testing"></a>批次測試
批次測試是一種了解 LUIS 會為多少範例語句評分的方式。 範例應該對 LUIS 來說是新的範例，且應該已正確標記您想要讓 LUIS 尋找的意圖和實體。 測試結果會指出 LUIS 對該組語句將有的執行效果。 

## <a name="next-steps"></a>後續步驟

了解[共同作業](luis-concept-collaborator.md)的相關概念。
