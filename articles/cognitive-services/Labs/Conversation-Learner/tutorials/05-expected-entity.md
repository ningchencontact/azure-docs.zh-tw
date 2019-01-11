---
title: 如何使用對話學習模組動作的「預期實體」屬性 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何使用對話學習模組模型的「預期實體」屬性。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c6696d0f22026ac333c526b505732d15a4b01be7
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796987"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>如何使用動作的「預期實體」屬性

本教學課程示範動作的「預期實體」屬性。

## <a name="video"></a>影片

[![預期實體教學課程預覽](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料
使用動作的「預期實體」屬性，可將使用者的回應儲存到實體到這個動作中。

將實體新增至動作的「預期實體」屬性時，系統將會：

1. 先嘗試使用機器學習架構的實體擷取模型比對實體
2. 如果未找到實體，則會根據啟發學習法將整個使用者語句指派給 $entity
3. 呼叫 `EntityDetectionCallback`，並繼續進行動作選取項目。

## <a name="steps"></a>步驟

### <a name="create-the-model"></a>建立模型

1. 在 Web UI 中，按一下 [新增模型]。
2. 在 [名稱] 欄位中輸入 "ExpectedEntities"，並按 Enter 鍵。
3. 按一下 [建立] 按鈕。

### <a name="entity-creation"></a>建立實體

1. 在左面板中按一下 [實體]，然後按 [新增實體] 按鈕。
2. 選取 [自訂定型] 作為 [實體類型]。
3. 輸入 "name" (名稱) 作為 [實體名稱]。
4. 按一下 [建立] 按鈕。

> [!NOTE]
> 若實體類型為「自訂定型」，表示此實體與其他類型的實體不同，可以進行訓練。

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>建立第一個動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入 "What's your name" (您叫什麼名字？)
3. 在 [預期實體] 欄位中，輸入 "name" (名稱)。
4. 按一下 [建立] 按鈕。

> [!NOTE]
> 在使用者的回應中偵測到並擷取的實體，會在選擇此動作時儲存至「名稱」實體。 若未偵測到任何實體，則會將整個回應儲存至此實體。

### <a name="create-the-second-action"></a>建立第二個動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入 "Hi $name!" ($name，您好！)
3. 按一下 [建立] 按鈕。

> [!NOTE]
> 回應中的參考已將「名稱」實體自動新增為「必要實體」。

現在您有兩個動作。

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>訓練模型

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "hi" (您好)。
    - 這會模擬對話的使用者端。
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「您叫什麼名字？」
    - 「$name，您好！」 回應無法選取，因為此回應此時需要將「名稱」實體定義於模型的記憶體中。
5. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "Frank"。
    - "Frank" 會根據我們先前設定用以將回應儲存為實體的啟發學習法，反白顯示為實體。
6. 按一下 [評分動作] 按鈕。
    - 「名稱」實體此時會在模型的記憶體中定義為 "Frank"，因此「哈囉 $name」動作是可供選取的動作。
7. 選取回應：「$name，您好！」
8. 按一下 [儲存] 按鈕。

新增替代輸入可進一步訓練模型。

1. 在 [新增替代輸入...] 欄位中，輸入"I'm Jose" (我是 Jose)。
    - 模型無法將此名稱辨識為實體，因此會選取整個文字區塊作為實體的值
2. 按一下 "I'm Jose" (我是 Jose) 片語，然後按一下垃圾桶圖示。
3. 按一下 [Jose]，然後在實體清單中按一下 [名稱]。
4. 按一下 [Score Actions]\(評分動作\)。
5. 選取回應：「Frank，您好！」
6. 按一下 [儲存] 按鈕。

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Negatable 實體](./06-negatable-entities.md)
