---
title: 如何搭配對話學習模組模型使用 Negatable 實體 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組模型使用 Negatable 實體。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 2c758d1dd5d4d1e7ab25faccd5280963211181d1
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388797"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>如何搭配對話學習模組模型使用 Negatable 實體

本教學課程示範實體的 "Negatable" 屬性。

## <a name="video"></a>影片

[![Negatable 實體教學課程預覽](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料
實體的 「 Negatable"屬性可讓您標記這兩個標準模式 （正） 和負之實體執行個體，請教導正數和負數的模型為基礎並清除現有的實體的值。 設定了 "Negatable" 屬性的實體，對話學習模組中稱為 Negatable 實體。

## <a name="steps"></a>步驟

在 Web UI 中的首頁開始。

### <a name="create-the-model"></a>建立模型

1. 選取 **新的模型**。
2. 請輸入**NegatableEntity** for**名稱**。
3. 選取 [建立]  。

### <a name="entity-creation"></a>建立實體

1. 選取 **實體**在左窗格中，然後**新實體**。
2. 選取 **自訂訓練**for**實體類型**。
3. 請輸入**名稱**for**實體名稱**。
4. 請檢查**Negatable**若要讓使用者提供實體的值，或者，假設有*不*實體值以便刪除相符的實體值。
5. 選取 [建立]  。

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>建立第一個動作

1. 選取 **動作**在左窗格中，然後**新動作**。
2. 輸入**我不知道您的名稱。** 針對**機器人的回應...** .
3. 請輸入**名稱**for **Disqualifying 享有**。
4. 選取 [建立]  。

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>建立第二個動作

1. 選取 **動作**在左窗格中，然後**新動作**。
2. 輸入**我知道您的名稱。它是 $name。** 針對**機器人的回應...** .
3. 選取 [建立]  。

> [!NOTE]
> **名稱**實體已自動加入做**所需的實體**回應 [utterance] 中的參考。

現在您有兩個動作。

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>訓練模型

1. 選取 **定型對話方塊**在左窗格中，然後**新的訓練對話方塊**。
2. 請輸入**hello**針對使用者的 utterance 左側的對談 面板中。
3. 選取 **評分動作**。
4. 選取**我不知道您的名稱。** 從 [動作] 清單中。 百分位數做為依據的條件約束的唯一有效動作為 100%。
5. 請輸入**我的名字是 Frank**針對使用者的 utterance 左側的對談 面板中。
6. 反白顯示**Frank**然後選取 **+ 名稱**。 Negatable 實體具有兩個執行個體: （+） 再加上新增或覆寫值;（-） 負移除的值。
7. 選取 **評分動作**。 **名稱**實體現在指**Frank**模型在記憶體中，因此**我知道您的名稱。它是 $name**就可以使用動作。
8. 選取**我知道您的名稱。它是 $name。** 從 [動作] 清單中。
9. 輸入**我的名字不是 Frank。** 針對使用者的 utterance 左側的對談 面板中。
10. 反白顯示**Frank**然後選取 **-名稱**清除從該值**名稱**實體。
11. 選取 **評分動作**。
12. 選取**我不知道您的名稱。** 從 [動作] 清單中。
13. 輸入**我的名字是 Susan。** 針對使用者的第三個 utterance 左側的對談 面板中。
14. 反白顯示**Susan**然後 **+ 名稱** 

![](../media/T06_training.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [多重值實體](./07-multi-value-entities.md)
