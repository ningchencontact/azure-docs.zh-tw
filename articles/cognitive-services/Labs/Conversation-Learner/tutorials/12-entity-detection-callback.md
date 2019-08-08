---
title: 如何搭配對話學習模組模型使用實體偵測回呼 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組模型使用實體偵測回呼。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3f6094da7b103f1c99e9bb3fa9313354fcb2a8bc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703994"
---
# <a name="how-to-use-entity-detection-callback"></a>如何使用實體偵測回呼

本教學課程會顯示實體偵測回呼，並呈現解析實體的常見模式。

## <a name="video"></a>視訊

[![實體偵測回呼教學課程預覽](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>需求
本教學課程要求 `tutorialEntityDetectionCallback` 聊天機器人必須為執行中狀態。

    npm run tutorial-entity-detection

## <a name="details"></a>詳細資料
實體偵測回呼可讓您透過程式碼修改實體辨識行為和實體操作。 我們將逐步解說常見的實體偵測回呼設計模式，以示範這項功能。 例如，將 "the big apple" (大蘋果) 解析為 "new york" (紐約)。

## <a name="steps"></a>步驟

### <a name="create-the-model"></a>建立模型

1. 在 Web UI 中，按一下 [新增模型]。
2. 在 [名稱] 欄位中輸入 "EntityDetectionCallback"，並按 Enter 鍵。
3. 按一下 [建立] 按鈕。

此範例需要三個實體，因此我們要先加以建立。

### <a name="create-the-custom-trained-entity"></a>建立自訂定型實體

1. 在左面板中按一下 [實體]，然後按 [新增實體] 按鈕。
2. 選取 [自訂定型] 作為 [實體類型]。
3. 輸入 "City" (城市) 作為 [實體名稱]。
4. 按一下 [建立] 按鈕。

### <a name="create-the-first-programmatic-entity"></a>建立第一個程式設計實體

1. 按一下 [新增實體] 按鈕。
2. 選取 [程式設計] 作為 [實體類型]。
3. 輸入 "CityUnknown" 作為 [實體名稱]。
4. 按一下 [建立] 按鈕。

### <a name="create-the-first-programmatic-entity"></a>建立第一個程式設計實體

1. 按一下 [新增實體] 按鈕。
2. 選取 [程式設計] 作為 [實體類型]。
3. 輸入 "CityResolved" 作為 [實體名稱]。
4. 按一下 [建立] 按鈕。

現在，建立三個動作。

### <a name="action-creation"></a>建立動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應] 欄位中，輸入 "Which city do you want?" (您想要哪座城市？)
3. 在 [使用者回覆中的預期實體] 欄位中，輸入 "City" (城市)。
4. 在 [不合格的實體] 欄位中，輸入 "City" (城市)。
5. 按一下 [建立] 按鈕。
6. 按一下 [新增動作] 按鈕。
7. 在 [聊天機器人的回應...] 欄位中，輸入 "Which city do you want?" (您想要哪座城市？)
8. 在 [使用者回覆中的預期實體...] 欄位中，輸入 "I don't know this city" (我不知道這座城市)。
9. 按一下 [建立] 按鈕。
10. 按一下 [新增動作] 按鈕。
11. 在 [聊天機器人的回應...] 欄位中，輸入 "$CityResolved is very nice" ($CityResolved 是很棒的城市)。
12. 按一下 [建立] 按鈕。

回呼程式碼如下：

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>訓練模型

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "hi" (您好)
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「您想要哪座城市？」
5. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "big apple" (大蘋果)
6. 按一下 [評分動作] 按鈕。
    - 按一下此按鈕會觸發實體偵測回呼
    - 回呼程式碼將 CityResolved 實體值正確設為 "new york" (紐約)
7. 選取 [new york is very nice] \(紐約是很棒的城市\) 回應。

許多聊天機器人案例都會用到此模式。 使用者語句和擷取的實體可提供給您的商務邏輯，而該邏輯會將語句轉換成標準格式，然後儲存到程式設計實體中，供後續的對話回合使用。

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [工作階段回呼](./13-session-callbacks.md)
