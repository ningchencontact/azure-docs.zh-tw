---
title: 如何在對話學習模組模型中記錄對話 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何在對話學習模組模型中記錄對話。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: f1e161fa05a77682d0b5eb1c6c21975ac87028a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387672"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>如何在對話學習模組模型中記錄對話

本教學課程示範如何從已記錄的實際使用者互動使用記錄對話，進一步訓練對話學習模組模型。

## <a name="video"></a>影片

[![記錄對話教學課程預覽](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

和在先前的教學課程中建立的天氣模型。

## <a name="details"></a>詳細資料
記錄對話是您的聊天機器人與使用者的互動所留下的記錄。 藉由管理這些記錄對話，您將可修正實體標籤與動作選取項目，以改善模型效能和整體系統效能。

## <a name="steps"></a>步驟

在 Web UI 中按一下 [匯入教學課程]，並選取名為 "Tutorial-11-LogDialogs" 的模型。

此模型包含一個名為「城市」的實體，和依設計用來回應該城市相關氣象查詢的動作。 先前使用兩個訓練對話來訓練模型，因此效能期望會比較低。 經由額外的訓練以及對實際使用者互動的公開，模型將可改善效能。

### <a name="create-a-new-conversation"></a>建立新的對話

1. 在左面板中按一下 [記錄對話]，然後按 [新增記錄對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "Austin weather forecast" (奧斯丁天氣預報)
3. 按一下 [完成測試] 按鈕。
4. 在清單中按一下「奧斯丁天氣預報」記錄對話。
5. 在聊天面板中按一下「奧斯丁天氣預報」語句。
6. 按一下 [奧斯丁]，然後在實體清單中按一下 [城市]。
7. 按一下 [提交變更] 按鈕。
    - 實體值中的這項變更會導致對話產生下游變更，因為我們在記憶體中有新的實體值。 後續動作有可能已失效，尤其是涉及「城市」實體的動作。
8. 在聊天面板中按一下「哪些城市？」 語句。
9. 選取回應：「奧斯丁可能是晴天」。
10. 按一下 [另存為訓練對話] 按鈕。
    - 訓練會立即展開

![](../media/T11_logdialog.png)

最後要請您注意， 您可以根據商務需求，在 [設定] 中取消勾選 [記錄對話]，以關閉對話記錄功能。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [實體偵測回呼](./12-entity-detection-callback.md)
