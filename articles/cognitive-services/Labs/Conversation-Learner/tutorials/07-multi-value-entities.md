---
title: 如何搭配對話學習模組模型使用多重值實體 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組模型使用多重值實體。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 1f62def5e498f3f744beaed0cda207e1a75bfdf2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387952"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>如何搭配對話學習模組模型使用多重值實體
本教學課程說明實體的「多重值」屬性。

## <a name="video"></a>影片

[![多重值實體教學課程的預覽](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料
多重值實體會在清單中累積其值，而不是儲存單一值。  這些實體有助於使用者指定多個值。 例如比薩的配料。

已標示為「多重值」的實體，其每個已辨識的實體執行個體都將附加至聊天機器人的記憶體清單中。 後續的辨識會附加至實體的值，而非覆寫。

## <a name="steps"></a>步驟

在 Web UI 中的首頁開始。

### <a name="create-the-model"></a>建立模型

1. 選取 **新的模型**。
2. 請輸入**MultiValueEntities** for**名稱**。
3. 選取 [建立]  。

### <a name="entity-creation"></a>建立實體

1. 選取 **實體**在左窗格中，然後**新實體**。
2. 選取 **自訂訓練**for**實體類型**。
3. 請輸入**配料**for**實體名稱**。
4. 請檢查**多重值**啟用實體累積一或多個值。
5. 請檢查**Negatable**。
6. 選取 [建立]  。

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>建立第一個動作

1. 選取 **動作**在左窗格中，然後**新動作**。
2. 請輸入**以下是您的配料： $toppings**如**機器人的回應...** .前置的錢幣符號表示實體參考。
3. 選取 [建立]  。

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>建立第二個動作

1. 選取 **動作**在左窗格中，然後**新動作**。
2. 輸入**哪些配料？** 如**機器人的回應...** .
3. 請輸入**配料**for **Disqualifying 享有**。
4. 選取 [建立]  。

現在您有兩個動作。

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>訓練模型

1. 選取 **定型對話方塊**在左窗格中，然後**新的訓練對話方塊**。
2. 請輸入**大家好**針對使用者的 utterance 左側的對談 面板中。
3. 選取 **評分動作**。
4. 選取 **哪些配料？** 從動作清單。 百分位數做為依據的條件約束的唯一有效動作為 100%。
5. 請輸入**起司和蘑菇**針對使用者的 utterance 左側的對談 面板中。
6. 反白顯示**起司**然後選取 **+ 配料**。
7. 反白顯示**蘑菇**然後選取 **+ 配料**。
8. 選取 **評分動作**。
9. 選取 **以下是您的配料： $toppings**從動作清單。
10. 請輸入**新增披**的使用者在左邊的對談 面板的 下一步 utterance。
11. 反白顯示**披**然後選取 **+ 配料**。
12. 選取 **評分動作**。
13. 選取 **以下是您的配料： $toppings**從動作清單。
14. 請輸入**移除起司**的使用者在左邊的對談 面板的第三個 utterance。
15. 反白顯示**起司**然後選取 **-配料**。
16. 選取 **評分動作**。
17. 選取 **以下是您的配料： $toppings**從動作清單。

![](../media/T07_training.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [預先定型的實體](./08-pre-trained-entities.md)
