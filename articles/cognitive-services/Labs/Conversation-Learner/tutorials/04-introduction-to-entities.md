---
title: 如何搭配對話學習模組模型使用實體 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組模型使用實體。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 4239cbfe369060516653145b04a2bcfa0882f20d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796996"
---
# <a name="introduction-to-entities"></a>實體簡介

本教學課程介紹實體、不合格的實體和必要實體及其在對話學習模組中的使用方式。

## <a name="video"></a>影片

[![實體簡介教學課程預覽](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>需求

本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料

實體會擷取聊天機器人執行其工作所需的某些資訊，而這些資訊可從使用者語句中擷取，或是由自訂程式碼指派。 實體本身也可以藉由明確分類為「必要」或「不合格」來限制動作的可用性

- 「必要實體」必須存在於模型的記憶體中，動作才可供使用
- 「不合格的實體」必須*不*存在於模型的記憶體中，動作才可供使用

本教學課程著重於「自訂實體」。 預先定型、多重值、Negatable 實體和程式設計實體會分述於其他教學課程中。

## <a name="steps"></a>步驟

### <a name="create-the-model"></a>建立模型

1. 在 Web UI 中，按一下 [新增模型]。
2. 在 [名稱] 欄位中輸入 "IntroToEntities"，並按 Enter 鍵。
3. 按一下 [建立] 按鈕。

### <a name="entity-creation"></a>建立實體

1. 在左面板中按一下 [實體]，然後按 [新增實體] 按鈕。
2. 選取 [自訂定型] 作為 [實體類型]。
3. 輸入 "city" (城市) 作為 [實體名稱]。
4. 按一下 [建立] 按鈕。

> [!NOTE]
> 若實體類型為「自訂定型」，表示此實體與其他類型的實體不同，可以進行訓練。

### <a name="create-the-actions"></a>建立動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入 "I don't know what city you want" (我不知道您要的城市)。
3. 在 [不合格的實體] 欄位中，輸入 "city" (城市)。
4. 按一下 [建立] 按鈕。

> [!NOTE]
> 在「城市」實體定義於聊天機器人的記憶體中的情況下，若將「城市」實體新增至「不合格的實體」，聊天機器人即不會再考量此動作。

現在，建立第二個動作。

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入 "The weather in the $city is probably sunny" ($city 可能是晴天)。
3. 按一下 [建立] 按鈕。

> [!NOTE]
> 回應中的參考已將「城市」實體自動新增至「必要實體」清單中。

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>訓練模型

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "hello" (您好)。
    - 這會模擬對話的使用者端。
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「我不知道您要的城市」。
5. 以使用者的身分回應 "Seattle" (西雅圖)。
6. 按一下 [評分動作] 按鈕。
7. 選取回應：「$city 可能是晴天」。
8. 按一下 [儲存] 按鈕。

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [預期的實體](./05-expected-entity.md)
