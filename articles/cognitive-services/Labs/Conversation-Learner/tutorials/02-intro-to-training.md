---
title: 訓練對話學習模組模型的簡介 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何訓練模型，包括透過對話學習模組對先前的訓練建立分支和進行編輯。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c657025ce588363cf76ce10868d809a9aff69222
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705619"
---
# <a name="introduction-to-training"></a>訓練簡介

本教學課程將說明訓練模型、根據先前的訓練建立新的分支訓練，以及編輯聊天機器人回應而加以變更的基本概念。

## <a name="video"></a>視訊

[![訓練簡介教學課程預覽](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料

- 動作:聊天機器人對使用者輸入的回應。
- 訓練：我們教導聊天機器人回應使用者輸入的方式。
- 建立分支：修改已儲存訓練對話內的使用者輸入，用以建立一開始與原先相同、但後續將對話導引至不同方向的新訓練對話。

## <a name="steps"></a>步驟

### <a name="create-a-new-model"></a>建立新模型

1. 在 Web UI 中，按一下 [新增模型]
2. 針對 [名稱]，輸入 "Inspire Bot"。 然後按一下 [建立]。

### <a name="create-an-action"></a>建立動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應] 欄位中，輸入 "Hi! Would you like to be inspired today?" (您好！今天需要新的靈感嗎？)。
    - 將所有其他欄位和核取方塊保留為預設設定。
3. 按一下 [建立]。

### <a name="first-training-and-creating-another-action-while-training"></a>第一次訓練和在訓練時建立另一個動作

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "hello" (您好)。 
    - 這會模擬對話的使用者端。
3. 按一下 [評分動作]。
4. 選取回應：「您好！ Would you like to be inspired today?" (您好！今天需要新的靈感嗎？)。
5. 以使用者的身分回應 "yes" (是)。
6. 按一下 [評分動作]。
7. 按一下 [+ 動作] 按鈕。 
    - 這會帶您前往熟悉的 [建立動作] 對話方塊。
8. 在聊天機器人的回應中輸入 "You're awesome!" (您太厲害了！)
9. 按一下 [建立]。
10. 請注意，聊天機器人會立即回應。
11. 按一下 [儲存] 按鈕。

### <a name="branch-a-second-training-off-of-the-first-training"></a>從第一個訓練建立另一個分支訓練
1. 按一下摘要列出第一個訓練的方格資料列。 
    - 這可讓您檢視和編輯現有的訓練。
2. 按一下使用者回應 [是]。 
    - 這將會顯示編輯控制項。
3. 按一下分支圖示。 
    - 這會顯示一個提示，詢問是否為新的對話提供不同的使用者輸入。
4. 輸入 "no" (否) 並按 Enter 鍵，或按一下 [建立] 按鈕。 
    - 此時您將會有新的訓練對話執行個體，而原有的會保持原狀。
5. 按一下 [評分動作]。
6. 按一下聊天機器人剛剛顯示的錯誤回應。
7. 按一下 [+ 動作] 按鈕 
    - 如此，我們即可為聊天機器人的回應建立新動作。
8. 在聊天機器人的回應中輸入 "No problem! Have a great day!" (好的，祝您有美好的一天！)
9. 按一下 [建立專案]。
10. 請注意，聊天機器人會立即回應。
11. 按一下 [儲存] 按鈕。

### <a name="test-the-trainings"></a>測試訓練
1. 在左面板中按一下 [記錄對話]，然後按 [新增記錄對話]。
2. 在訊息中輸入 "hi" (您好)。 
3. 請注意，聊天機器人會依據我們訓練的方式自動回應。
4. 輸入使用者回覆 "yes" (是)。
5. 請留意聊天機器人的回應，它會指出第一個訓練可有效運作。
6. 按一下 [工作階段逾時] 按鈕。 這會向對話學習模組指出我們想要重新開始，而忽略剛才執行的對話回合。
7. 在訊息中輸入 "hi" (您好)。 
8. 請注意，聊天機器人會依據我們訓練的方式自動回應。
9. 輸入使用者回覆 "no" (否)。
10. 請留意聊天機器人的回應，它會指出第二個訓練可有效運作。
11. 按一下 [完成測試] 按鈕。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [等候與非等候動作](./03-wait-vs-nonwait-actions.md)
