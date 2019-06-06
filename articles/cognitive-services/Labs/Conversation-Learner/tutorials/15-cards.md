---
title: 如何搭配對話學習模組模型使用卡片，第 1 部分 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組模型使用卡片。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: d0b87597c506aca5c3b4f6f3815f58656203ac3c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389511"
---
# <a name="how-to-use-cards-part-1-of-2"></a>如何使用卡 (第 1 部份，共 2 個部份)

本教學課程顯示如何在聊天機器人中加入並使用簡單的卡。

> [!NOTE]
> 對話學習模組目前預期您的卡片定義檔案位於名為 "cards" 的目錄中，也就是啟動聊天機器人的目錄。

## <a name="video"></a>影片

[![卡片教學課程預覽](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料

卡是讓使用者可在對話中選取選項的 UI 項目。 

### <a name="open-the-demo"></a>開啟示範

在 Web UI 中按一下 [匯入教學課程]，並選取名為 "Tutorial-15-Cards" 的模型。

### <a name="the-card"></a>卡

卡片定義位於下列位置：C:\<installedpath\>\src\cards\prompt.json。

系統預期會在這個 "cards" 目錄中找出卡片定義。

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> 請注意文字欄位中的內文類型 "TextBlock" 和 "{{question}}" 預留位置。
> 有兩個提交按鈕，以及對於每個按鈕所提交的文字。

### <a name="actions"></a>動作

我們建立了三個動作。 如下所示，第一個動作是卡片。

![](../media/tutorial13_actions.PNG)

我們將查看卡片動作類型的建立方式：

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> 卡片包含三個不同的參數 - 問題輸入、按鈕 1 和按鈕 2。 這些元素是卡片中的範本參考，您會在其中輸入問題和各自的答案。 您也可以參考並使用實體，或參考並使用文字和實體的混合。

眼睛圖示可顯示卡呈現的樣貌。

### <a name="practicing-creating-card-actions"></a>練習建立卡片動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 選取 [CARD] 作為 [動作類型]。
3. 從 [範本] 清單中選擇 [提示]。
4. 在 [問題] 欄位中，輸入 "Go left or right" (向左或向右)
5. 在 [按鈕 1] 欄位中，輸入 "left" (向左)
6. 在 [按鈕 2] 欄位中，輸入 "right" (向右)
7. 按一下 [取消] 按鈕。

### <a name="train-dialog-using-an-adaptive-card"></a>使用調適型卡片的訓練對話

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "hi" (您好)
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「提示：問題：向左或向右？」
    - 眼睛圖示可用來預覽卡片
5. 在聊天面板中，在顯示的提示中按一下 [向左] 按鈕。
6. 按一下 [評分動作] 按鈕。
7. 選取回應：「向左」
8. 按一下 [儲存] 按鈕。
9. 選取回應：「提示：問題：向左或向右？」
10. 在聊天面板中，在顯示的提示中按一下 [向右] 按鈕。
11. 按一下 [評分動作] 按鈕。
12. 選取回應：「向右」

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [混合式聊天機器人](./16-hybrid-bots.md)
