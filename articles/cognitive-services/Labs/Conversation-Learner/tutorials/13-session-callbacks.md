---
title: 如何搭配對話學習模組模型使用工作階段回呼 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組模型使用工作階段回呼。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: eeed0718a1feb170dbbaa783ec0a840c7829c02e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390006"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>如何搭配對話學習模組模型使用工作階段回呼

本教學課程介紹工作階段、如何處理工作階段，以及對話學習模組的 onSessionStart 和 onSessionEnd 回呼。

## <a name="video"></a>影片

[![工作階段回呼教學課程預覽](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>需求
本教學課程需要執行 "tutorialSessionCallbacks" 聊天機器人。

    npm run tutorial-session-callbacks

## <a name="details"></a>詳細資料
本教學課程涵蓋工作階段的概念、如何預設處理工作階段，以及如何覆寫該行為。

在對話學習模組中，一個工作階段代表一個與聊天機器人之間不中斷的互動式交換。 工作階段可以有多個回合，但如果超過 30 分鐘無活動，則會以程式設計方式終止。  請參閱「限制」的相關說明頁，以取得關於變更此預設工作階段逾時持續期間的資訊。

若無活動的期間達到此長度，將導致聊天機器人建立新的工作階段，並將遞迴式類神經網路重設回其初始狀態。 依預設會清除所有實體值。 此一清除實體值的預設行為，可依據下列方式進行變更。

### <a name="load-the-demo-model"></a>載入示範模型

在 Web UI 中按一下 [匯入教學課程]，並選取名為 "Tutorial-13-SessionCallbacks" 的模型。

### <a name="code-for-the-callbacks"></a>回呼的程式碼

對於此模型的兩個回呼，您可以在下列位置找到其程式碼範例：`c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`。

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback：這個方法會設定 BotName 實體。
- OnSessionEndCallback：您可以指定要保留的實體。 這將清除使用者名稱和使用者電話以外的所有實體。

每個回呼都是選擇性的。

### <a name="actions"></a>動作

模型中定義了四個動作。 現有的動作會顯示在 [動作] 的方格檢視中

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>建立結束工作階段動作 (適用於回呼引動過程)

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 選取 [ENDSESSION] 作為 [實體類型]。
3. 在 [資料...] 欄位中，輸入 "Done" (完成)
4. 按一下 [建立] 按鈕。

### <a name="edit-an-existing-action"></a>編輯現有的動作

1. 從方格檢視中選取「$UserName，您目前在 $UserLocation」動作。
2. 取消勾選 [等候回應] 核取方塊。
3. 按一下 [儲存] 按鈕。

### <a name="chaining-actions"></a>鏈結動作

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "hi" (您好)。
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「您好，我是 Botty。 您叫什麼名字？」
5. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "Lars"
6. 選取回應：「您好，我是 Lars。 您的電話號碼幾號？」
7. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "555-555-5555"
8. 按一下 [評分動作] 按鈕。
9. 選取回應：「Lars，您能否告訴 Botty 您的位置？」
10. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "Seattle" (西雅圖)
11. 按一下 [評分動作] 按鈕。
12. 選取回應：「Lars，您在西雅圖嗎」
13. 選取回應：「完成」
14. 按一下 [儲存] 按鈕。

### <a name="testing-the-model"></a>測試模型

1. 在左面板中按一下 [記錄對話]，然後按 [新增記錄對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "hi" (您好)
3. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "Lars"
4. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "555-555-5555"
5. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "Seattle" (西雅圖)
    - 此時，位置以外的所有實體值都應已保留。
6. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "hello" (您好)
7. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "Detroit" (底特律)
8. 按一下 [工作階段逾時] 按鈕。
    - 按一下此按鈕會執行聊天機器人對長時間無活動的回應
9. 按一下 [確定] 按鈕。
10. 按一下 [完成測試] 按鈕。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [API 呼叫](./14-api-calls.md)
