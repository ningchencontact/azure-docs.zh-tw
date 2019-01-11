---
title: 如何搭配對話學習模組模型使用等候與非等候動作 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組模型使用等候與非等候動作。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 95cf223004c50583701e65d3adf02f57bd541fae
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796982"
---
# <a name="wait-and-non-wait-actions"></a>等候與非等候動作

本教學課程會顯示對話學習模組中的等候與非等候動作兩者的差異。

## <a name="video"></a>影片

[![等候與非等候教學課程預覽](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料

- 等候動作：系統採取「等候」動作之後，將停止採取動作，並等候使用者輸入。
- 非等候動作：系統採取「非等候」動作之後，將立即選擇其他動作 (不等候使用者輸入)。

## <a name="steps"></a>步驟

### <a name="create-a-new-model"></a>建立新模型

1. 在 Web UI 中，按一下 [新增模型]
2. 在 [名稱] 欄位中輸入 "Wait Non-Wait" 並按 Enter 鍵，或按一下 [建立] 按鈕。

### <a name="create-the-first-two-wait-actions"></a>建立前兩個等候動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入「您想要哪種比薩？」。
    - 這是等候動作，因此請保留已勾選的 [等候回應] 方塊。
3. 按一下 [建立] 按鈕。
4. 重複這些步驟，以 "Pizza on the way!" (外帶比薩！) 作為聊天機器人的回應，建立另一個動作。

### <a name="train-using-those-wait-actions"></a>使用這些等候動作進行訓練

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "Hi" (您好)。 
    - 這會模擬對話的使用者端。
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「您想要哪種比薩？」。
5. 以使用者的身分回應 "Margherita" (瑪格麗特)。
6. 按一下 [評分動作] 按鈕。
7. 選取回應：「外帶比薩！」。
8. 按一下 [儲存] 按鈕。

### <a name="create-a-non-wait-action-while-training"></a>在訓練時建立非等候動作
雖然您可以比照先前的方式來建立非等候動作，但您也可以從訓練工作階段內建立。
1. 按一下 [新增訓練對話] 按鈕。
2. 以使用者的身分輸入 "Hello" (您好)。
3. 按一下 [評分動作] 按鈕。
4. 按一下 [+ 動作] 按鈕。 
    - 這會帶您前往熟悉的 [建立動作] 對話方塊。
5. 在聊天機器人的回應中，輸入 "Welcome to Pizza Bot!" (歡迎使用比薩聊天機器人！)
6. 取消勾選 [等候回應] 核取方塊。
7. 按一下 [建立] 按鈕。
    - 請注意，聊天機器人會立即回應：「歡迎使用比薩聊天機器人！」 而系統會再次提示您提供另一個聊天機器人回應。 這是因為聊天機器人的回應是我們剛才建立的非等候動作。
9. 選取回應：「您想要哪種比薩？」。
10. 以使用者的身分回應 "Margherita" (瑪格麗特)。
11. 按一下 [評分動作] 按鈕。
12. 選取回應：「外帶比薩！」。
13. 按一下 [儲存] 按鈕。

> [!NOTE]
> 聊天機器人對於等候與非等候動作的回應順序。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [實體簡介](./04-introduction-to-entities.md)
