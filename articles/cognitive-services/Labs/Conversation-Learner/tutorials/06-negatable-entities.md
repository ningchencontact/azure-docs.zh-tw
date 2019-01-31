---
title: 如何搭配對話學習模組模型使用 Negatable 實體 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組模型使用 Negatable 實體。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: fea950e2c13d9b5ce0c3619990961e611edd6626
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207372"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>如何搭配對話學習模組模型使用 Negatable 實體

本教學課程示範實體的 "Negatable" 屬性。

## <a name="video"></a>影片

[![Negatable 實體教學課程預覽](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料
實體的 "Negatable" 屬性可讓您標記實體的一般 (正面) 和負面執行個體、正面和負面模型進行教導，以及清除現有實體的值。 設定了 "Negatable" 屬性的實體，對話學習模組中稱為 Negatable 實體。

## <a name="steps"></a>步驟

### <a name="create-the-model"></a>建立模型

1. 在 Web UI 中，按一下 [新增模型]。
2. 在 [名稱] 欄位中輸入 "NegatableEntity"，並按 Enter 鍵。
3. 按一下 [建立] 按鈕。

### <a name="entity-creation"></a>建立實體

1. 在左面板中按一下 [實體]，然後按 [新增實體] 按鈕。
2. 選取 [自訂] 作為 [實體類型]。
3. 輸入 "name" (名稱) 作為 [實體名稱]。
4. 勾選 [Negatable] 核取方塊。
    - 勾選此屬性可讓使用者提供實體值，或指出某個項目*不是*實體值。 在後者的情況下，結果將是刪除相符的實體值。
5. 按一下 [建立] 按鈕。

![](../media/tutorial5_entities.PNG)

### <a name="create-the-first-action"></a>建立第一個動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入 "I don't know your name" (我不知道您的名字)。
3. 在 [不合格的實體] 欄位中，輸入 "name" (名稱)。
4. 按一下 [建立] 按鈕。

### <a name="create-the-second-action"></a>建立第二個動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入 "I know your name. It is $name" (我知道您的名字。您叫做 $name)。
3. 按一下 [建立] 按鈕。

> [!NOTE]
> 回應中的參考已將「名稱」實體自動新增為「必要實體」。

現在您有兩個動作。

![](../media/tutorial5_actions.PNG)

### <a name="train-the-model"></a>訓練模型

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "hello" (您好)。
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「我不知道您的名字」。
    - 百分位數為 100%，因為唯一的有效動作以條件約束為基礎。
5. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "My name is Frank" (我的名字是 Frank)
6. 選取 [Frank]，並選擇標籤 [+名字]
    - 「名稱」實體有兩個執行個體：「+名字」和「-名字」。  (+) 加號會新增或覆寫值。 (-) 減號會移除值。
7. 按一下 [評分動作] 按鈕。
    - 「名稱」實體此時會在模型的記憶體中定義為 "Frank"，因此「我知道您的名字。 您叫做 $name」動作是可用的動作。
8. 選取回應：「我知道您的名字。 您叫做 $name」。
9. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "My name is not Frank" (我的名字不是 Frank)。
10. 選取 [Frank]，並選擇標籤 [-名字]
    - 我們將選取 [-名字] 以清除實體目前的值。
11. 按一下 [評分動作] 按鈕。
12. 選取回應：「我不知道您的名字」。
13. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "My name is Susan" (我的名字是 Susan)。
14. 選取 [Susan]，並選擇標籤 [+名字]

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [多重值實體](./07-multi-value-entities.md)
