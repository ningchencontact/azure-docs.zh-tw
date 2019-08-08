---
title: 如何搭配對話學習模組模型使用 API 呼叫 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組模型使用 API 呼叫。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 261536932cc82a28ad4ee3ffc3575ea41fe9ec5b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703917"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>如何將 API 呼叫新增到對話學習模組模型

本教學課程示範如何將 API 呼叫新增到您的模型。 API 呼叫是您在聊天機器人中定義並寫入的函式，可供對話學習模組呼叫。

## <a name="video"></a>視訊

[![API 呼叫教學課程預覽](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>需求
本教學課程需要「tutorialAPICalls.ts」聊天機器人正在執行。

    npm run tutorial-api-calls

## <a name="details"></a>詳細資料

- API 呼叫可以讀取和操作實體。
- API 呼叫可以存取記憶體管理員物件。
- API 呼叫可以接受引數。

### <a name="open-the-demo"></a>開啟示範

在 Web UI 中按一下 [匯入教學課程]，並選取名為 "Tutorial-14-APICalls" 的模型。

### <a name="entities"></a>實體

我們已在名為 `number` 的模型中定義一個實體。

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API 呼叫
API 呼叫的程式碼會定義於此檔案中：`C:\<installedpath>\src\demos\tutorialAPICalls.ts`。

![](../media/tutorial12_apicalls.PNG)

- `RandomGreeting` 回呼會傳回在 `greeting` 陣列中定義的隨機問候語。
- `Multiply` 回呼會將呼叫它的動作所傳入的兩個數字相乘，並傳回可呈現在 UI 中的結果。
    - 請注意，記憶體管理員是第一個引數。 
    - 此外應注意，API 回呼可接受多個輸入，在此案例中為 `num1string` 和 `num2string`。
- `ClearEntities` 回呼會清除數字實體，讓使用者能夠輸入另一個數字。 
    - 說明 API 呼叫可如何操作實體。

### <a name="actions"></a>個動作
我們建立了四個動作。 其中三個是「非等候動作」，第四個則是「文字」動作，會詢問使用者我們在其他教學課程中看過的類似問題。 若要了解每個動作的建立方式，執行下列作業：
1. 在左面板中按一下 [動作]，然後按一下方格中列出的四個動作之一。
2. 請留意快顯的表單上各個欄位的值。
3. 此外應留意 API 欄位旁的 `Refresh` 按鈕。
    - 如果我們要在 UI 頁面開啟時停止聊天機器人，並且對 API 進行變更，您可以按一下 `Refresh` 按鈕以挑選最新的變更。

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities、Multiply 和 RandomGreeting
這三個動作全都是 API 類型。 它們分別依賴 API 回呼函式執行某項工作，並可能傳回值對使用者顯示。

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>「您想要哪個數字乘以 12」
這是「文字」動作，唯一的作用是向使用者提問。 雖然此動作實際上並不會與其中一個 API 回呼互動，但它會提示使用者以數字回應，而此數字會存入實體的記憶體中，且隨後可供真正會使用 API 回呼之一的 "Multiply" 動作使用。


### <a name="train-dialog"></a>訓練對話

我們將逐步執行「訓練對話」。

1. 在左面板上按一下 `Train Dialogs`，然後按一下 `New Train Dialog` 按鈕。
2. 輸入 "hello"。
3. 按一下 `Score Actions` 按鈕。
4. 選取 `RandomGreeting`。 
    - 這將執行隨機問候語 API 呼叫。
    - 此動作不會等待使用者回應。
5. 選取 `What number to do you want to multiply by 12?`
6. 輸入任意單一數字。
    - 請注意，您的數字已自動標示為 `number` 實體。
7. 按一下 `Score Actions` 按鈕。
8. 選取 `Multiply` 動作。
    - 請留意乘以 12 的結果。
    - 請注意，記憶體仍包含您為 `number` 輸入的值
9. 選取 `ClearEntities` 動作。
    - 請注意，`number` 的實體值已從記憶體中清除。
10. 按一下 `Save` 按鈕。

您現在已了解如何註冊 API 回呼、其常見模式，以及如何定義引數，並且使值與其中的實體產生關聯。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [卡片第 1 部分](./15-cards.md)
