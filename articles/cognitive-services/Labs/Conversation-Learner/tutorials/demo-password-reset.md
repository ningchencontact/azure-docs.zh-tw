---
title: 示範對話學習模組模型：密碼重設 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何建立示範對話學習模組模型。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 409647da146a2844384204cb03de5028d45e5763
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792385"
---
# <a name="demo-password-reset"></a>示範：密碼重設
本教學課程示範簡單的技術支援聊天機器人如何協助您以對話學習模組進行密碼重設。 聊天機器人的模型可學習非一般對話流程和多回合序列，包括領域外類別。 工作可在不使用程式碼或實體的情況下完成。

## <a name="video"></a>影片

[![示範密碼預覽](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>需求
本教學課程需要執行密碼重設聊天機器人

    npm run demo-password

### <a name="open-the-demo"></a>開啟示範

在 Web UI 的模型清單中，按一下 [教學課程示範密碼重設]。 

### <a name="actions"></a>動作

模型中包含一組可協助使用者解決常見密碼問題的動作。

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>訓練對話

模型也包含數個訓練對話，其中包括會示範領域外類別訓練的一些對話。 例如，某些使用者可能會提出行車路線指示之類的要求。 範例聊天機器人已針對示範用途受過一些相關訓練，而會直接回答「無法提供協助」。 現有的訓練對話清單可在左面板中取得。

![](../media/tutorial_pw_reset_entities.PNG)

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "i lost my password" (我遺失了密碼)。
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「是本機帳戶還是 Microsoft 帳戶的密碼？」
5. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "local account please" (是本機帳戶)
6. 按一下 [評分動作] 按鈕。
7. 選取回應：「您的 Windows 是什麼版本？」
8. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "windows xp"
9. 按一下 [評分動作] 按鈕。
10. 按一下 [+動作] 按鈕。
11. 在 [聊天機器人的回應...] 欄位中，輸入 "SOLUTION:How to reset password on Windows XP" (解決方案：如何在 Windows XP 重設密碼)。
12. 按一下 [建立] 按鈕。

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>領域外案例的訓練對話

1. 在左面板中按一下 [訓練對話]，然後按現有的 "toy stores" (玩具店) 訓練對話。
2. 在聊天面板中，按一下 [玩具店] 語句。
3. 在 [新增替代輸入...] 欄位中輸入"web search" (Web 搜尋)，並按 Enter 鍵。
4. 在 [新增替代輸入...] 欄位中輸入"flight booking" (航班預訂)，並按 Enter 鍵。
5. 按一下 [儲存變更] 按鈕。
6. 按一下 [儲存編輯] 按鈕。
7. 在左面板中按一下 [記錄對話]，然後按 [新增記錄對話] 按鈕。
8. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "i can't find my password" (我找不到密碼)
9. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "Microsoft account" (Microsoft 帳戶)
10. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "thanks" (謝謝)
11. 按一下 [完成測試] 按鈕。
12. 在方格檢視中按一下 [我找不到密碼] 記錄對話。
13. 在聊天面板中，按一下錯誤顯示的 [解決方案：如何重設 Microsoft 帳戶密碼] 回應。
14. 按一下 [+動作] 按鈕。
15. 在 [聊天機器人的回應...] 欄位中，輸入 "You are welcome" (不客氣)
16. 按一下 [建立] 按鈕。
17. 按一下 [另存為訓練對話] 按鈕。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [示範 - 披薩訂單](./demo-pizza-order.md)
