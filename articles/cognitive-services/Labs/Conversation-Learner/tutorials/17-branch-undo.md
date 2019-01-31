---
title: 如何搭配對話學習模組模型使用分支與復原作業 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組模型使用分支與復原作業。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cca7b42c83be3ca428509ea48c387a1c35ddb35a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206725"
---
# <a name="how-to-use-branching-and-undo-operations"></a>如何使用分支與復原作業
在本教學課程中，我們將介紹復原與分支作業。


## <a name="details"></a>詳細資料
### <a name="undo"></a>復原
可讓開發人員「復原」上一個使用者輸入或動作選擇。 在背景中，「復原」實際上會建立新的對話，並重新執行到上一個步驟。  這表示將再次呼叫對話中的實體偵測回呼和 API 呼叫。

### <a name="branch"></a>分支
建立新的訓練對話，此對話的開頭會與現有的訓練對話相同，可省下手動重新輸入對話回合的時間。 在背景中，「分支」會建立新的對話，並重新執行現有訓練對話到選取的步驟。  這表示將再次呼叫對話中的實體偵測回呼和 API 呼叫。


## <a name="requirements"></a>需求
本教學課程需要執行接受披薩訂單的聊天機器人：

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>開啟或匯入示範

如果您已完成比薩訂購教學課程，則只需從 Web UI 中的清單開啟該模型即可。 否則，您必須按一下 [匯入教學課程]，並選取名為 "Demo-PizzaOrder" 的模型。

## <a name="undo"></a>復原

下列範例說明如何檢視執行中的 `Undo` 功能：

### <a name="training-dialogs"></a>訓練對話
1. 在左面板上按一下 [訓練對話]，然後按一下 `New Train Dialog` 按鈕。
2. 輸入 "Order a pizza" (訂購比薩)。
3. 按一下 `Score Actions` 按鈕。
4. 點選 [您的披薩要哪些配料？]
5. 輸入 "anything" (都可以)。
6. 按一下 `Undo` 按鈕。
    - 最後一個項目移除時，會留下聊天機器人的最後一個回應「您的披薩要哪些配料？」

## <a name="branch"></a>分支

在此示範中，我們將開啟現有的訓練對話，並藉由分支從中建立新的訓練對話。

1. 在左面板上，按一下 [訓練對話]。
2. 請留意此方格，您應該只會看到一個開頭為「新訂單」的訓練。
3. 在此方格中按一下 [新訂單]，以開啟現有的訓練對話。
4. 按一下對話中的最後一個「否」。
5. 按一下 [分支] 圖示，即下圖中以紅色圈起的圖示：
    - ![](../media/tutorial15_branch.PNG)
    - 「否」之前的所有訓練對話會整個複製到新的訓練對話中。
    - 如此，您就不需要重新輸入先前的對話回合，即可從這個時間點探索新的對話「分支」。
6. 輸入 "yes" (是)，然後按 Enter 鍵。
7. 按一下 `Score Actions` 按鈕。
    - 此時聊天機器人會自動挑選回應，但我們不喜歡這個回應，因此要加以變更。
8. 按一下聊天機器人的最後一個回應。
    - 如此我們即可選取不同的回應。
9. 選取 "UseLastToppings"。
10. 按一下 `Score Actions` 按鈕。
    - 同樣地，聊天機器人會自動挑選回應。 它應該會說，「您的比薩會加上臘腸、起司和蘑菇」。 
    - 我們喜歡這次的回應，因此將予以保留。
11. 按一下 `Score Actions` 按鈕。
    - 聊天機器人會再次自動挑選回應，它應該會說，「您還要加點什麼嗎？」
12. 輸入 "no" (否)。
13. 按一下 `Save Branch` 按鈕。
14. 請注意，方格中此時會有兩個以「新訂單」開頭的訓練。
    - 其中一個是您用來建立分支的訓練。
    - 另一個則是您剛儲存的分支版本。
    - 分別按一下這兩個訓練以驗證前述期望。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [版本控制和標記](./18-version-tag.md)
