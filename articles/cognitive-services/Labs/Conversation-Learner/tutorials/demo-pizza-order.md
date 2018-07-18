---
title: 對話學習模組應用程式範例：披薩訂單 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 學習如何建立示範對話學習模組應用程式。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99cd89c4f4430f2d65ed0963e3092d51a83842d7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369670"
---
# <a name="demo-pizza-order"></a>示範：披薩訂單
這段示範說明披薩訂購聊天機器人。 這支援藉由下列功能訂購一份披薩：

- 辨識使用者語句中的披薩配料
- 檢查披薩配料是否有庫存或無庫存，並進行適當的回應
- 記住前一份訂單的披薩配料，並使用相同的配料開始新的訂單

## <a name="requirements"></a>需求
本教學課程需要執行披薩訂單聊天機器人

    npm run demo-pizza

### <a name="open-the-demo"></a>開啟示範

在 Web UI 的應用程式清單中，按一下 [TutorialDemo Pizza Order]。 

## <a name="entities"></a>實體

我們已經建立 3 個實體。

- Toppings：將累積使用者要求的配料。 其中包含有庫存的有效配料。 這會檢查配料是否有庫存或無庫存。
- OutofStock：這會用來回覆使用者選取的配料沒有庫存。
- LastToppings：下達訂單之後，此實體會用來在訂單上向使用者提供配料清單。

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>動作

我們已經建立一組動作，其中包括詢問使用者想要披薩加什麼配料、告訴使用者目前已加入的配料等等。

另外還有兩個 API 呼叫：

- FinalizeOrder： 下達披薩訂單
- UseLastToppings：從前一份訂單移轉配料 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>訓練對話
我們已經定義許多訓練對話。 

![](../media/tutorial_pizza_dialogs.PNG)

讓我們試試以教學工作階段為例。

1. 按一下 [Train Dialogs]\(訓練對話\)，然後按一下 [New Train Dialog]\(新增訓練對話\)。
1. 輸入「order a pizza」(訂購披薩)。
2. 按一下 [Score Actions]\(評分動作\)。
3. 按一下以選取 [what would you like on your pizza?]\(您要在披薩上加什麼？\)
4. 輸入「mushrooms and cheese」(蘑菇和起士)。
    - 請注意 LUIS 已將兩者標示為配料。 如果不正確，您可以按一下以反白顯示，然後進行修正。
    - 實體旁的「+」號表示已加入到配料集合。
5. 按一下 [Score Actions]\(評分動作\)。
    - 請注意蘑菇和起士不在配料的記憶中。
3. 按一下以選取 [you have $Toppings on your pizza]\(您的披薩上有 '$Toppings\)
    - 請注意這是非等候動作，因此聊天機器人將要求下一個動作。
6. 選取 [Would you like anything else?]\(您還需要其他任何服務嗎？\)
7. 輸入「remove mushrooms and add peppers」(不要蘑菇而且要加黑胡椒)。
    - 請注意**蘑菇**旁邊有「-」號，因為已經移除。 而且黑胡椒有「+」，可以加入到配料。
2. 按一下 [Score Actions]\(評分動作\)。
    - 請注意**黑胡椒**現在是粗體，因為這是新配料。 而且已將**蘑菇**去除。
8. 按一下以選取 [you have $Toppings on your pizza]\(您的披薩上有 '$Toppings\)
6. 選取 [Would you like anything else?]\(您還需要其他任何服務嗎？\)
7. 輸入「加入豌豆」。
    - 豌豆是沒有庫存的配料範例。 請注意，這仍然標示為配料。
2. 按一下 [Score Actions]\(評分動作\)。
    - 豌豆顯示為 OutOfStock。
    - 若要查看這是如何發生，請開啟位於 C:\<\installedpath>\src\demos\demoPizzaOrder.ts 的程式碼。 並注意 EntityDetectionCallback 方法。 對於每個配料都會呼叫這個方法來查看否有庫存。 如果沒有，則會從配料集合中清除，並新增至 OutOfStock 實體。 inStock 變數是在該方法之上定義，其中有一份庫存配料清單。
6. 選取 [We don't have $OutOfStock]\(我們沒有 $OutOfStock\)。
7. 選取 [Would you like anything else?]\(您還需要其他任何服務嗎？\)
8. 輸入「no」(否)。
9. 按一下 [Score Actions]\(評分動作\)。
10. 選取 [FinalizeOrder] API 呼叫。 
    - 這會呼叫程式碼中定義的「FinalizeOrder」函式。 這會清除配料，並且回覆「your order is on its way」(您的訂單正在處理中)。 
2. 輸入「order another」(訂購另一份)。 我們會開始新的訂單。
9. 按一下 [Score Actions]\(評分動作\)。
    - 請注意起士和黑胡椒在記憶體中，因為這些是前一份訂單的配料。
1. 選取 [Would you like $LastToppings]\(您是否要 $LastToppings\)。
2. 輸入「yes」(是)。
3. 按一下 [Score Actions]\(評分動作\)。
    - 聊天機器人想要進行 UseLastToppings 動作。 這是兩個回呼方法的第二個。 這會將最後一份訂單的配料複製到配料，並清除最後的配料。 這會記住最後一份訂單，而且，如果使用者說還要另一份披薩，會提供這些配料做為選項。
2. 按一下以選取 [you have $Toppings on your pizza]\(您的披薩上有 '$Toppings\)。
3. 選取 [Would you like anything else?]\(您還需要其他任何服務嗎？\)
8. 輸入「no」(否)。
4. 按一下 [Done Teaching]\(完成教學\)。

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [示範 - VR 應用程式啟動器](./demo-vr-app-launcher.md)
