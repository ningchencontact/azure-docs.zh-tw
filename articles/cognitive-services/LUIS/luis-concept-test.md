---
title: 測試您的 LUIS 應用程式 - Azure | Microsoft Docs
description: 使用 Language Understanding (LUIS) 來持續調整您的應用程式，以改善應用程式及提升其語言理解能力。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: d231eaf98358e3f8237a820e59433558d293872f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224342"
---
# <a name="testing-in-luis"></a>LUIS 中的測試

測試是一個程序，其中您會將範例語句提供給 LUIS，然後獲得 LUIS 所辨識意圖和實體的回應。 

您可以一次使用一個語句，或是提供[一批](luis-concept-batch-test.md)語句，來反覆[測試](luis-interactive-test.md) LUIS。 藉由測試，您便可以將目前[作用中](luis-concept-version.md#active-version)模型與已發佈的模型做比較。 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>
## <a name="what-is-a-score-in-testing"></a>什麼是測試分數？
請參閱[預測分數](luis-concept-prediction-score.md)概念，以深入了解預測分數。

## <a name="interactive-testing"></a>互動式測試
執行互動式測試時，會從網站的 [Test] \(測試\) 面板執行。 您可以輸入語句來查看意圖和實體的識別與評分情況。 如果 LUIS 並未如您所預期在測試窗格中針對語句預測意圖和實體，請將其複製到 [Intent] \(意圖\) 頁面中作為新語句。 然後標記該語句的組件，並進行 LUIS 定型。 

## <a name="batch-testing"></a>批次測試
如果您要一次測試多個語句，請參閱[批次測試](luis-concept-batch-test.md)。

## <a name="endpoint-testing"></a>端點測試
您可以使用[端點](luis-glossary.md#endpoint)搭配最多兩個應用程式版本來進行測試。 在將您應用程式的主要或即時版本設定為**生產**端點的情況下，將第二個版本新增至**預備**端點。 此方法會提供您三個語句版本：在 [LUIS](luis-reference-regions.md) 網站 [測試] 窗格中的目前模型，以及在兩個不同端點的兩個版本。 

所有端點測試都會計入您的使用量配額中。 

## <a name="do-not-log-tests"></a>不記錄測試
如果您對端點進行測試，而不想要記錄語句，請記得使用 `logging=false` 查詢字串設定。

## <a name="where-to-find-utterances"></a>語句尋找位置
LUIS 會將所有已記錄的語句儲存在查詢記錄中，從 [LUIS](luis-reference-regions.md) 網站的 [應用程式] 清單頁面以及 LUIS [撰寫 API](https://aka.ms/luis-authoring-apis) 都可下載此記錄。 

任何 LUIS 不確定的語句都會列在 [LUIS](luis-reference-regions.md) 網站的[檢閱端點語句](luis-how-to-review-endoint-utt.md) 頁面中。 

![檢閱端點語句](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>請務必定型
在您對模型進行變更之後，請務必進行 LUIS [定型](luis-how-to-train.md)。 對 LUIS 應用程式所做的變更必須等到應用程式進行定型之後，才會顯示。 

## <a name="best-practices"></a>最佳作法
了解[最佳做法](luis-concept-best-practices.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解如何[測試](luis-interactive-test.md)您的語句。