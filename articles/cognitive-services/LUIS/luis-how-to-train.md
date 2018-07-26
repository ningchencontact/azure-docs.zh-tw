---
title: 訓練 LUIS 應用程式 - Azure | Microsoft Docs
description: 使用 Language Understanding (LUIS) 訓練您的模型。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: e947df20141b0b9870f318f410488aea23bafcf5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223179"
---
# <a name="train-your-luis-app"></a>訓練 LUIS 應用程式

訓練程序會教導您的 Language Understanding (LUIS) 應用程式，以改善其自然語言理解。 在模型的更新之後訓練 LUIS 應用程式，例如新增、編輯、加上標籤或刪除實體、意圖或語調。 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

訓練和[測試](luis-concept-test.md)應用程式是一種反覆程序。 在您訓練 LUIS 應用程式之後，請使用範例語調來測試它，查看是否正確地辨識意圖和實體。 若為否，請更新 LUIS 應用程式、訓練，並重新測試。 

## <a name="train-your-app"></a>訓練應用程式
若要開始反覆程序，您至少需要先訓練 LUIS 應用程式一次。 請在訓練之前，確定每個意圖都至少有一個語調。

1. 在 [我的應用程式] 頁面上選取應用程式名稱，來存取應用程式。 

2. 在應用程式中，選取上方面板中的 [訓練]。 

    ![訓練按鈕](./media/luis-how-to-train/train-button.png)

3. 訓練完成時，綠色通知列會顯示在瀏覽器頂端。

    ![訓練和測試應用程式頁面](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>若應用程式中有一或多個未包含範例語調的意圖，則無法訓練應用程式。 新增所有意圖的語調。 如需詳細資訊，請參閱[新增範例語調](luis-how-to-add-example-utterances.md)。

## <a name="next-steps"></a>後續步驟

* [使用 LUIS 標示建議的語調](luis-how-to-review-endoint-utt.md) 
* [使用功能來改善 LUIS 應用程式效能](luis-how-to-add-features.md) 