---
title: 如何搭配對話學習模組使用替代輸入 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組使用替代輸入。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7416939db8d6552f79fba700b8432de2ad228846
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704205"
---
# <a name="how-to-use-alternative-inputs"></a>如何使用替代輸入

本教學課程說明如何在教學介面中使用使用者語句的 [替代輸入] 欄位。

## <a name="video"></a>視訊

[![替代輸入教學課程預覽](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料
替代輸入是使用者可能在訓練對話中的特定時間點所說的替代使用者語句 (具有相同語意)。 這些替代輸入可讓您更簡潔地指定語句的變化，而不需要在個別的訓練對話中表述各種變化。

## <a name="steps"></a>步驟

### <a name="create-the-model"></a>建立模型

1. 在 Web UI 中，按一下 [新增模型]。
2. 在 [名稱] 欄位中輸入 "AlternativeInputs"，並按 Enter 鍵。
3. 按一下 [建立] 按鈕。

### <a name="entity-creation"></a>建立實體

1. 在左面板中按一下 [實體]，然後按 [新增實體] 按鈕。
2. 選取 [自訂定型] 作為 [實體類型]。
3. 輸入 "city" (城市) 作為 [實體名稱]。
4. 按一下 [建立] 按鈕。

![](../media/T10_actions.png)

現在，我們將建立三個動作。

### <a name="create-the-first-action"></a>建立第一個動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入 "Which city?" (哪座城市？)
3. 在 [使用者回覆中的預期實體] 欄位中，輸入 "city" (城市)。
4. 在 [不合格的實體] 欄位中，輸入 "city" (城市)。
5. 按一下 [建立] 按鈕。

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>建立第二個動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入 "The weather in $city is probably sunny" ($city 可能是晴天)。
3. 按一下 [建立] 按鈕。

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>建立第三個動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入 "Try asking for the weather" (嘗試詢問天氣)。
3. 在 [不合格的實體] 欄位中，輸入 "city" (城市)。
4. 按一下 [建立] 按鈕。

![](../media/T10_action_create_3.png)

您現在有三個動作。

![](../media/T10_actions.png)

### <a name="train-the-model"></a>訓練模型

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "What's the weather?" (天氣如何？)
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「哪座城市？」
5. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "Denver" (丹佛)
6. 按一下 [評分動作] 按鈕。
7. 選取回應：「丹佛可能是晴天」。
8. 按一下 [提交變更] 按鈕。

![](../media/T10_training_1.png)

我們將建立另一個訓練對話以進一步訓練模型。

### <a name="second-model-train-dialog"></a>第二個模型訓練對話

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "What can you do?" (您該怎麼辦？)
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「嘗試詢問天氣」。
5. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "What's the weather in Seattle?" (西雅圖的天氣如何？)
6. 按一下 [西雅圖]，然後在實體清單中按一下 [城市]。
7. 按一下 [評分動作] 按鈕。
8. 選取回應：「西雅圖可能是晴天」。
9. 按一下 [提交變更] 按鈕。

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>使用替代輸入的第三個模型訓練對話

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "help" (協助)
3. 按一下 [評分動作] 按鈕。
    - 模型並不確定最佳選項，因此將依預設選擇最高的百分位數。
4. 按一下 [放棄教學] 按鈕，然後按 [確認] 按鈕。

![](../media/T10_training_3.png)

我們將使用替代輸入進一步調整系統。 您可以在教學期間或是稍後新增替代輸入。

1. 在左面板上按一下 [訓練對話]，然後在訓練對話的清單中 選取 [您該怎麼辦？]。
1. 在聊天面板中按一下 [您該怎麼辦？] 語句。
1. 在 [新增替代輸入...] 欄位中輸入"help" (協助)，並按 Enter 鍵。
1. 按一下 [儲存變更] 按鈕。

![](../media/T10_training_4.png)

我們將新增另一個替代輸入來處理休士頓。

1. 在聊天面板中按一下 [西雅圖的天氣如何？] 語句。
1. 在 [新增替代輸入...] 欄位中輸入"forecast for Houston" (休士頓預報)，並按 Enter 鍵。
   - 錯誤訊息會反白指出替代輸入必須在語意上相同，並且包含與原始語句相同的實體，而不只是相同的實體值。 必須要有相同的實體存在。
1. 按一下 [休士頓]，並從實體清單中選取 [城市]。
1. 在 [新增替代輸入...] 欄位中輸入"forecast for Seattle" (西雅圖預報)，並按 Enter 鍵。
1. 按一下 [西雅圖]，並從實體清單中選取 [城市]。
1. 按一下 [儲存變更] 按鈕。
1. 按一下 [儲存編輯] 按鈕。

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>測試模型

1. 在左面板中按一下 [記錄對話]，然後按 [新增記錄對話]。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "help me" (協助我)
3. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "forecast for Denver" (丹佛預報)

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [記錄對話](./11-log-dialogs.md)
