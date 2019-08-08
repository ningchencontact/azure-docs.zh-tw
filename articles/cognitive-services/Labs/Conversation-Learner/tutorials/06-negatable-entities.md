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
ROBOTS: NOINDEX
ms.openlocfilehash: 4edbfc396d4ab32b991b084a7b738f04b5205418
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704113"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>如何搭配對話學習模組模型使用 Negatable 實體

本教學課程示範實體的 "Negatable" 屬性。

## <a name="video"></a>視訊

[![Negatable 實體教學課程預覽](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料
實體的 "Negatable" 屬性可讓您標記實體的一般 (正面) 和負實例, 並根據正面和負面模型進行教學, 並清除現有實體的值。 設定了 "Negatable" 屬性的實體，對話學習模組中稱為 Negatable 實體。

## <a name="steps"></a>步驟

在 Web UI 中的首頁開始。

### <a name="create-the-model"></a>建立模型

1. 選取 [**新增模型**]。
2. 輸入**NegatableEntity**作為 [**名稱**]。
3. 選取 [建立]。

### <a name="entity-creation"></a>建立實體

1. 選取左面板中的 [**實體**], 然後選取 [**新增實體**]。
2. 針對 [**實體類型**] 選取 [**自訂定型**]。
3. 輸入 [**機構名稱**] 的**名稱**。
4. 請檢查**Negatable** , 讓使用者提供實體值, 或說某個專案*不*是實體值, 因此會刪除相符的實體值。
5. 選取 [建立]。

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>建立第一個動作

1. 在左面板中選取 [**動作**], 然後選取 [**新增動作**]。
2. 輸入 [**我不知道您的名稱]。** 適用于**Bot 的回應**...。
3. 輸入**不合格**授與的**名稱**。
4. 選取 [建立]。

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>建立第二個動作

1. 在左面板中選取 [**動作**], 然後選取 [**新增動作**]。
2. 輸入**我知道您的姓名。$Name。** 適用于**Bot 的回應**...。
3. 選取 [建立]。

> [!NOTE]
> 在回應語句中,**名稱**實體會自動新增為**所需的實體**。

現在您有兩個動作。

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>訓練模型

1. 在左面板中選取 [**訓練對話**], 然後選取 [**新增訓練對話]** 。
2. 在左側聊天面板中輸入**hello**作為使用者的語句。
3. 選取 [**評分動作**]。
4. 選取 [**我不知道您的名稱]。** 從動作清單。 百分位數是 100%, 因為唯一的有效動作是以條件約束為基礎。
5. 在左側聊天面板中, 為使用者的語句輸入**我的姓名**。
6. 反白顯示**Frank** , 然後選取 [ **+ 名稱**]。 Negatable 實體有兩個實例: (+) 加上或覆寫值。(-) 減號會移除值。
7. 選取 [**評分動作**]。 **名稱**實體現在已在模型的記憶體中定義為**Frank** , 因此我知道**您的名稱。$Name**的動作可供使用。
8. 選取 **[我知道您的名稱]。$Name。** 從動作清單。
9. 輸入**我的姓名不是 Frank。** 針對使用者在左側聊天面板中的語句。
10. 反白顯示**Frank**然後選取 [ **-名稱**], 以清除**名稱**實體中的值。
11. 選取 [**評分動作**]。
12. 選取 [**我不知道您的名稱]。** 從動作清單。
13. 輸入**我的姓名是 Susan。** 針對使用者在左側聊天面板中的第三個語句。
14. 反白顯示**Susan** then **+ 姓名** 

![](../media/T06_training.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [多重值實體](./07-multi-value-entities.md)
