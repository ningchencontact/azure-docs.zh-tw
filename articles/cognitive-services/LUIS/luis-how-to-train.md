---
title: 訓練應用程式-LUIS
titleSuffix: Azure Cognitive Services
description: 訓練程序會教導您的 Language Understanding (LUIS) 應用程式版本，以改善其自然語言理解。 在模型的更新之後訓練 LUIS 應用程式，例如新增、編輯、加上標籤或刪除實體、意圖或語調。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b3841c9d60cf275e423024fc66c15582f95c0a10
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932763"
---
# <a name="train-your-active-version-of-the-luis-app"></a>訓練您 LUIS 應用程式的作用中版本 

訓練程序會教導您的 Language Understanding (LUIS) 應用程式，以改善其自然語言理解。 在模型的更新之後訓練 LUIS 應用程式，例如新增、編輯、加上標籤或刪除實體、意圖或語調。 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

訓練和[測試](luis-concept-test.md)應用程式是一種反覆程序。 在您訓練 LUIS 應用程式之後，請使用範例語調來測試它，查看是否正確地辨識意圖和實體。 若為否，請更新 LUIS 應用程式、訓練，並重新測試。 

定型會套用至 LUIS 入口網站中的作用中版本。 

## <a name="how-to-train-interactively"></a>若要以互動方式定型

若要在 [LUIS 入口網站](https://www.luis.ai)中開始反覆程序，您必須至少先將 LUIS 應用程式定型一次。 請在訓練之前，確定每個意圖都至少有一個語調。

1. 在 [我的應用程式] 頁面上選取應用程式名稱，來存取應用程式。 

2. 在應用程式中，選取上方面板中的 [訓練]。 

3. 訓練完成時，綠色通知列會顯示在瀏覽器頂端。

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>若應用程式中有一或多個未包含範例語調的意圖，則無法訓練應用程式。 新增所有意圖的語調。 如需詳細資訊，請參閱[新增範例語調](luis-how-to-add-example-utterances.md)。

## <a name="training-date-and-time"></a>訓練日期和時間

訓練日期和時間為 GMT + 2。 

## <a name="train-with-all-data"></a>以所有資料進行訓練

訓練會使用一小部分的負面取樣。 如果您想要使用所有資料，而不是使用小部分的負面取樣，請使用[版本設定 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)，並將 `UseAllTrainingData` 設定為 true，來關閉這項功能。 

## <a name="unnecessary-training"></a>不必要的定型

您不需要在每一次變更之後都進行定型。 應該在將一組變更套用至模型之後，且您想要進行的下一個步驟是測試或發佈時，才進行定型。 如果您不需要測試或發佈，則無須進行定型。 

## <a name="training-with-the-rest-apis"></a>使用 REST API 進行定型

在 LUIS 入口網站中，定型是只要按 [Train] \(定型\) 按鈕的單一步驟。 使用 REST API 進行定型是一個兩步驟程序。 首先是使用 HTTP POST 來[要求定型](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)。 接著是使用 HTTP Get 來要求[定型狀態](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46)。 

若要知道定型何時完成，您必須輪詢該狀態，直到所有模型都定型成功為止。 

## <a name="next-steps"></a>後續步驟

* [使用 LUIS 標示建議的語調](luis-how-to-review-endpoint-utterances.md) 
* [使用功能來改善 LUIS 應用程式效能](luis-how-to-add-features.md) 
