---
title: 反復應用程式設計-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 在模型變更、語句範例、發佈及從端點查詢收集資料的反覆循環中學習時，成效最佳。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 2a540606a6f9cfa790a2244628e7f0b7bef35986
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639254"
---
# <a name="authoring-cycle-for-your-luis-app"></a>撰寫 LUIS 應用程式的循環
LUIS 在模型變更、語句範例、發佈及從端點查詢收集資料的反覆循環中學習時，成效最佳。 

![撰寫循環](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>建置 LUIS 模型
模型的目的是要查明使用者想要什麼 (意圖)，以及問題的哪些部分提供有助於判斷解答的詳細資料 (實體)。 

模型必須是應用程式定義域特定的，才能判斷相關的單字和語句，以及一般的單字排序。 

模型需要意圖, 而且_應該有_實體。 

## <a name="add-training-examples"></a>新增定型範例
LUIS 需要意圖中有範例語句。 這些範例需要有足夠的單字選擇和單字順序變化，才能判斷語句的意圖為何。 每個範例語句都必須將任何必要的資料標記為實體。 

您需指示 LUIS 忽略與應用程式定義域無關的語句，方法是將該語句指派給 **None** 意圖。 無須標記任何您不需要從語句中提取的單字或片語。 無須標記要忽略的單字或片語。 

## <a name="train-and-publish-the-app"></a>將應用程式定型並發佈
一旦每個意圖中有15到30個不同的語句, 並將所需的實體標示為, 您就必須[訓練](luis-how-to-train.md)再[發佈](luis-how-to-publish-app.md)。 使用發佈成功通知中的連結來取得您的端點。 請確定您已建立併發布您的應用程式, 以便在您需要的[端點區域](luis-reference-regions.md)中使用。 

## <a name="https-endpoint-testing"></a>HTTPS 端點測試
您可以從 HTTPS 端點測試您的 LUIS 應用程式。 從端點進行測試可讓 LUIS 選擇任何語句, 並以低信賴度進行[審查](luis-how-to-review-endpoint-utterances.md)。  

## <a name="recycle"></a>回收

當您完成撰寫循環時，可以重新開始。 開始閱讀以低信賴度標記的[端點語句](luis-how-to-review-endpoint-utterances.md)LUIS。 檢查這些語句是否有意圖和實體。 檢閱語句之後，檢閱清單應該會空白。  

請考慮將目前版本[複製](luis-concept-version.md#clone-a-version)到新版本, 然後開始撰寫新版本中的變更。 

## <a name="batch-testing"></a>批次測試

[批次測試](luis-concept-batch-test.md)是一種方式, 可查看 LUIS 評分的範例語句數。 範例應該對 LUIS 來說是新的範例，且應該已正確標記您想要讓 LUIS 尋找的意圖和實體。 測試結果會指出 LUIS 對該組語句將有的執行效果。 

## <a name="next-steps"></a>後續步驟

了解[共同作業](luis-concept-collaborator.md)的相關概念。
