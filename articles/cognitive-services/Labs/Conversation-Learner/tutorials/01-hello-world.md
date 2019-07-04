---
title: 如何建立 "Hello World" 對話學習模組模型 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何建立 "Hello World" 對話學習模組模型。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: fe5d21fadef8f4452ba36259dbf89cefc78230de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388050"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>如何使用對話學習模組建立 "Hello World" 模型

本教學課程說明如何開始使用對話學習模組，包括建立動作、以互動方式教導聊天機器人，以及修正終端使用者已記錄的對話。

## <a name="video"></a>影片

[![Hello World 教學課程預覽](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>需求
如果尚未準備好，請先確定所有安裝步驟均已完成，包括使用 LUIS 撰寫金鑰建立 `.env` 檔案。  如需詳細資訊，請參閱[快速入門](../quickstart.md)。

本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="steps"></a>步驟

在 Web UI 中的首頁開始。

### <a name="create-the-model"></a>建立模型
1. 按一下 [新增模型] 按鈕。
2. 在 [名稱] 欄位中，輸入 "Hello World"。
3. 按一下 [建立] 按鈕。

此時，您所建立的模型應該會顯示其檢視。

### <a name="create-an-action"></a>建立動作
1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
    - 動作可以是對話學習模組傳回給使用者、API 呼叫或卡片的文字訊息。
2. 在 [聊天機器人的回應...] 欄位中，輸入 "Hello" (您好)。
    - 這是聊天機器人將傳回的回應。
3. 按一下 [建立] 按鈕。

您已建立聊天機器人可執行的第一個動作，也就是傳回文字回應。

### <a name="train-dialogs"></a>訓練對話
您可以在此處訓練模型如何回應使用者語句。

#### <a name="first-training-dialog"></a>第一個訓練對話

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 輸入 "Hi" (您好)，然後按 Enter 鍵。
    - 作為使用者在對話開始時可能會說的語句範例。
3. 按一下 [評分動作] 按鈕。
4. 選取 [您好]。
    - 您剛剛已完成此範例對話中的一個完整回合。 
5. 輸入使用者回覆 "Goodbye" (再見)。
6. 按一下 [評分動作] 按鈕。
7. 按一下 [+ 動作] 按鈕。
8. 在 [聊天機器人的回應...] 欄位中輸入 "Goodbye!" (再見！)， 然後按一下 [建立] 按鈕。
    - 請注意，聊天機器人會以您剛剛建立的動作回應。
9. 按一下 [儲存] 按鈕。 
    - 這會結束並儲存此訓練對話。

現在，您的模型中已有一個訓練對話，以及單一實體和兩個動作。

#### <a name="second-training-dialog"></a>第二個訓練對話
我們將再進行一次訓練，看看聊天機器人會如何回應。

1. 按一下 [新增訓練對話] 按鈕。
2. 輸入 "Hi" (您好)
    - 這和第一個對話很類似，我們預期聊天機器人會給出不錯的分數。
3. 按一下 [評分動作] 按鈕。
    - 位置和分數可能還不夠精確，而需要更多訓練。
4. 選取 [您好]。
5. 輸入使用者回覆 "bye" (再見)。
6. 按一下 [評分動作] 按鈕。
7. 選取 [再見！]
8. 按一下 [儲存] 按鈕。

### <a name="log-dialogs"></a>記錄對話
您可以在此處測試、檢視及更正您或實際使用者與聊天機器人之間的對話。

#### <a name="test-the-model-as-an-end-user"></a>以終端使用者身分測試模型
1. 在左面板中按一下 [記錄對話]，然後按 [新增記錄對話] 按鈕。
2. 輸入 "hello there" (哈囉，你好)。
3. 稍待片刻，聊天機器人應該就會自動回應 "Hello" (哈囉)
4. 輸入 'byebye' (再見)
5. 稍待片刻，聊天機器人應該同樣會自動回應 "Hello" (哈囉)。
6. 按一下 [完成測試] 按鈕。

#### <a name="view-and-correct-a-user-conversation"></a>檢視和更正使用者對話
使用記錄對話，您可以檢視使用者與聊天機器人留下的對話清單。 您也可以編輯這些對話以更正機器人的回應，並將互動儲存為訓練對話。 若要這樣做：
1. 在方格中，按一下對話的記錄。
2. 按一下聊天機器人的最後一個動作，例如"Hello" (哈囉)。
3. 選取 [再見！] 以更正聊天機器人。
4. 按一下 [另存為訓練對話] 按鈕。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [訓練簡介](./02-intro-to-training.md)
