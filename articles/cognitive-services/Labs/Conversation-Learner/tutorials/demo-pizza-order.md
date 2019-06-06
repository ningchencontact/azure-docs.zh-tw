---
title: 示範對話學習模組模型：披薩訂單 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何建立示範對話學習模組模型。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 010245480d8e1f59d5c1b92a9e717f73b5ba7f4c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389147"
---
# <a name="demo-pizza-order"></a>示範：披薩訂單
這段示範說明披薩訂購聊天機器人，可藉由下列動作來支援單一比薩訂購：

- 從使用者語句中辨識披薩配料
- 管理配料庫存，並適當回應
- 記住先前的訂單，以快速重新訂購相同的比薩

## <a name="video"></a>影片

[![示範披薩預覽](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>需求
本教學課程需要執行披薩訂單聊天機器人

    npm run demo-pizza

### <a name="open-the-demo"></a>開啟示範

在 Web UI 的模型清單中，按一下 [TutorialDemo Pizza Order]。 

## <a name="entities"></a>實體

模型包含三個實體：

- "Toppings" 會累加使用者指定的配料 (如果有的話)。
- "OutofStock" 會告知使用者其選取的配料存貨不足
- "LastToppings" 會包含其上一次訂購時所加的配料

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>動作

模型包含一組會詢問使用者要選取哪些配料的動作、累加的配料和其他項目。

此外也提供兩個 API 呼叫：

- "FinalizeOrder" 會處理訂單履行
- "UseLastToppings" 會處理過去的配料資訊

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>訓練對話

在模型中可找到數個訓練對話。

![](../media/tutorial_pizza_dialogs.PNG)

我們將建立另一個訓練對話以進一步訓練模型。

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "Order a pizza with cheese" (訂購加起司的比薩)
    - 實體擷取程式會擷取「起司」一詞。
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「您的比薩加了起司」。
5. 選取回應：「您還要加點什麼嗎？」
6. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "add mushrooms and peppers" (加蘑菇和辣椒)
7. 按一下 [評分動作] 按鈕。
8. 選取回應：「您的比薩加了起司、蘑菇和辣椒」。
9. 選取回應：「您還要加點什麼嗎？」
10. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "remove peppers and add sausage" (移除辣椒，加臘腸)
11. 按一下 [評分動作] 按鈕。
12. 選取回應：「您的比薩加了起司、蘑菇和臘腸」。
13. 選取回應：「您還要加點什麼嗎？」
14. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "add yam" (加甜薯)
15. 按一下 [評分動作] 按鈕。
    - 實體偵測回呼程式碼已將「甜薯」值新增至 "OutofStock"，因為該文字不符合任何支援的原料。
16. 選取回應："OutOfStock"
17. 選取回應：「您還要加點什麼嗎？」
18. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "no" (否)
    - 「否」不會標記為任何類型的意圖。 相對地，我們將根據目前的內容選取相關動作。
19. 按一下 [評分動作] 按鈕。
20. 選取回應："FinalizeOrder"
    - 選取此動作會使客戶目前的配料儲存至的 "LastToppings" 實體，並且使 FinalizeOrder 回呼程式碼刪除 "Toppings" 實體。
21. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "order another" (訂購另一份)
22. 按一下 [評分動作] 按鈕。
23. 選取回應：「您要起司、蘑菇和臘腸嗎？」
    - 由於設定了 "LastToppings" 實體，因此這現在已是可用的動作。
24. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "yes" (是)
25. 按一下 [評分動作] 按鈕。
26. 選取回應："UseLastToppings"
27. 選取回應：「您的比薩加了起司、蘑菇和臘腸」。
28. 選取回應：「您還要加點什麼嗎？」

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [部署對話學習模組聊天機器人](../deploy-to-bf.md) (英文)
