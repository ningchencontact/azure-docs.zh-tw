---
title: 如何搭配對話學習模組模型使用多重值實體 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組模型使用多重值實體。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2363dde7dc2462adde730fa9a4883ffb6c558f3f
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796967"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>如何搭配對話學習模組模型使用多重值實體
本教學課程說明實體的「多重值」屬性。

## <a name="video"></a>影片

[![多值實體教學課程預覽](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料
多重值實體會在清單中累積其值，而不是儲存單一值。  這些實體有助於使用者指定多個值。 例如比薩的配料。

已標示為「多重值」的實體，其每個已辨識的實體執行個體都將附加至聊天機器人的記憶體清單中。 後續的辨識會附加至實體的值，而非覆寫。

## <a name="steps"></a>步驟

### <a name="create-the-model"></a>建立模型

1. 在 Web UI 中，按一下 [新增模型]。
2. 在 [名稱] 欄位中輸入 "MultiValueEntities"，並按 Enter 鍵。
3. 按一下 [建立] 按鈕。

### <a name="entity-creation"></a>建立實體

1. 在左面板中按一下 [實體]，然後按 [新增實體] 按鈕。
2. 選取 [自訂定型] 作為 [實體類型]。
3. 輸入 "toppings" (配料) 作為 [實體名稱]。
4. 勾選 [多重值] 核取方塊。
    - 多重值實體會在實體中累積一或多個值。
5. 勾選 [Negatable] 核取方塊。
    - "Negatable" 屬性已於另一個教學課程中說明。
6. 按一下 [建立] 按鈕。

![](../media/tutorial6_entities.PNG)

### <a name="create-the-first-action"></a>建立第一個動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入「以下是您的配料：$toppings」
    - 前置的貨幣符號表示實體參考
3. 按一下 [建立] 按鈕。

### <a name="create-the-second-action"></a>建立第二個動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入「您想要哪些配料？」
3. 在 [不合格的實體] 欄位中，輸入 "toppings" (配料)。
4. 按一下 [建立] 按鈕。

現在您有兩個動作。

![](../media/tutorial6_actions.PNG)

### <a name="train-the-model"></a>訓練模型

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "hi" (您好)。
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「您想要哪些配料？」
    - 百分位數為 100%，因為唯一的有效動作以條件約束為基礎。
5. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "cheese and mushrooms" (起司和蘑菇)
6. 按一下 [起司]，並選擇標籤 [+配料]
7. 按一下 [蘑菇]，並選擇標籤 [+配料]
8. 按一下 [評分動作] 按鈕。
9. 選取回應：「以下是您的配料：$toppings」
10. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "add pepper" (加辣椒)
11. 按一下 [辣椒]，並選擇標籤 [+配料]
12. 按一下 [評分動作] 按鈕。
13. 選取回應：「以下是您的配料：$toppings」
14. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "remove cheese" (移除起司)
15. 按一下 [起司]，並選擇標籤 [-配料]
16. 按一下 [評分動作] 按鈕。
17. 選取回應：「以下是您的配料：$toppings」

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [預先定型的實體](./08-pre-trained-entities.md)
