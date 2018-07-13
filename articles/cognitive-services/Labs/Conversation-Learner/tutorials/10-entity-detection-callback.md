---
title: 如何搭配對話學習模組應用程式使用實體偵測回呼 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 學習如何搭配對話學習模組應用程式使用實體偵測回呼。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e41ea5930ff0c8395d0c93aa42e224ebfc894ba8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369602"
---
# <a name="how-to-use-entity-detection-callback"></a>如何使用實體偵測回呼

本教學課程會顯示實體偵測回呼，並呈現解析實體的常見模式。

## <a name="requirements"></a>需求
本教學課程需要「tutorialEntityDetectionCallback」聊天機器人正在執行。

    npm run tutorial-entity-detection

## <a name="details"></a>詳細資料
實體偵測回呼允許使用自訂程式碼處理與實體有關的商務規則。 在此示範中，我們將使用回呼和程式設計實體解析使用者輸入的名稱成為正式名稱，例如，將「大蘋果」解析為「紐約」。

### <a name="open-the-demo"></a>開啟示範

在應用程式清單中，按一下 [Tutorial-10-EntityDetenctionCallback]。 

### <a name="entities"></a>實體

我們已在應用程式中定義 3 個實體。

![](../media/tutorial10_entities.PNG)

1. 城市是使用者將提供文字輸入的自訂實體。
2. CityUnknown 是程式設計實體。 這會由系統填入。 如果系統不知道是哪一個城市，系統會複製使用者輸入的文字。
3. CityResolved 是系統知道的城市。 這將是城市的正式名稱，例如，「大蘋果」將解析為「紐約」。

### <a name="actions"></a>動作

我們已經建立 3 個動作。 

![](../media/tutorial10_actions.PNG)

1. 第一個動作是「您想要哪座城市？」
2. 第二個是「我不知道 $CityUknown 這座城市。 您想要哪座城市？」
3. 第三個是「您說 $City，而我解析成 $CityResolved。」

### <a name="callback-code"></a>回呼代碼

讓我們看看代碼。 您可以在 C:\<installedpath>\src\demos\tutorialSessionCallbacks.ts 檔案中找到 EntityDetectionCallback 方法。

![](../media/tutorial10_callbackcode.PNG)

實體解析發生後，會呼叫此函式。
 
- 首先進行的動作是清除 $CityUknown。 $CityUknown 在一開始就會被清除，只會維持單一回合。
- 然後，我們會得到列有已辨識城市的清單。 選擇並嘗試解析第一個城市。
- 解析它的函式 (resolveCity) 已在上述程式碼中進一步定義。 該函示有標準城市名稱的清單。 它會在清單中找到城市名稱並傳回。 若找不到，則會查看「cityMap」並傳回對應的名稱。 如果仍然找不到城市，會傳回 null。
- 最後，如果城市已解析為名稱，我們會將該名稱儲存於 $CityKnown 實體。 若未解析，則將清除使用者說的話並填入 $CityUknown 實體。

### <a name="train-dialogs"></a>訓練對話

1. 按一下 [Train Dialogs]\(訓練對話\)，然後按一下 [New Train Dialog]\(新增訓練對話\)。
2. 鍵入「hello」(您好)。
3. 按一下 [Score Actions]\(評分動作\)，並選取 [Which city do you want?]\(您想要哪座城市？\)。
2. 鍵入「new york」(紐約)。
    - 請注意，這個輸入內容已被辨識為城市實體。
5. 按一下 [Score Actions]\(評分動作\)
    - 請注意，已填入 City 和 CityResolved。
6. 選取 [You said $City, and I resolved that to $CityResolved]\(您說 $City，而我解析成 $CityResolved\)。
7. 按一下 [Done Teaching]\(完成教學\)。

加入另一個範例對話：

1. 按一下 [New Train Dialog]\(新增訓練對話\)。
2. 鍵入「hello」(您好)。
3. 按一下 [Score Actions]\(評分動作\)，並選取 [Which city do you want?]\(您想要哪座城市？\)。
2. 輸入「big apple」(大蘋果)。
    - 請注意，這個輸入內容已被辨識為城市實體。
5. 按一下 [Score Actions]\(評分動作\)
    - 請注意，CityResolved 顯示程式碼執行的效果。
6. 選取 [You said $City, and I resolved that to $CityResolved]\(您說 $City，而我解析成 $CityResolved\)。
7. 按一下 [Done Teaching]\(完成教學\)。

加入另一個範例對話：

1. 按一下 [New Train Dialog]\(新增訓練對話\)。
2. 鍵入「hello」(您好)。
3. 按一下 [Score Actions]\(評分動作\)，並選取 [Which city do you want?]\(您想要哪座城市？\)。
2. 輸入「foo」。
    - 這是系統不知道的城市的範例。 
5. 按一下 [Score Actions]\(評分動作\)
6. 選取 I don't know this city, $CityUknown. Which city do you want?\(我不知道 $CityUknown 這座城市。您想要哪座城市？\)。
7. 鍵入「new york」(紐約)。
8. 按一下 [Score Actions]\(評分動作\)。
    - 注意 CityUknown 已清除，回答填入 CityResolved。
6. 選取 [You said $City, and I resolved that to $CityResolved]\(您說 $City，而我解析成 $CityResolved\)。
7. 按一下 [Done Teaching]\(完成教學\)。

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [工作階段回呼](./11-session-callbacks.md)
