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
ROBOTS: NOINDEX
ms.openlocfilehash: 7911dd561da0f1e7b62b1b457ae5b059d5d54767
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704095"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>如何搭配對話學習模組模型使用多重值實體
本教學課程說明實體的「多重值」屬性。

## <a name="video"></a>視訊

[![多值實體教學課程預覽](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料
多重值實體會在清單中累積其值，而不是儲存單一值。  這些實體有助於使用者指定多個值。 例如比薩的配料。

已標示為「多重值」的實體，其每個已辨識的實體執行個體都將附加至聊天機器人的記憶體清單中。 後續的辨識會附加至實體的值，而非覆寫。

## <a name="steps"></a>步驟

在 Web UI 中的首頁開始。

### <a name="create-the-model"></a>建立模型

1. 選取 [**新增模型**]。
2. 輸入**MultiValueEntities**作為 [**名稱**]。
3. 選取 [建立]。

### <a name="entity-creation"></a>建立實體

1. 選取左面板中的 [**實體**], 然後選取 [**新增實體**]。
2. 針對 [**實體類型**] 選取 [**自訂定型**]。
3. 輸入**配料**作為 [**機構名稱**]。
4. 勾選 [**多重值**] 可讓實體累積一或多個值。
5. 檢查**Negatable**。
6. 選取 [建立]。

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>建立第一個動作

1. 在左面板中選取 [**動作**], 然後選取 [**新增動作**]。
2. 在**這裡輸入您的配料:** **Bot 的回應**$toppings ...。前置的貨幣正負號表示實體參考。
3. 選取 [建立]。

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>建立第二個動作

1. 在左面板中選取 [**動作**], 然後選取 [**新增動作**]。
2. 輸入您想要的**配料是什麼？** 適用于**Bot 的回應**...。
3. 輸入**配料**以取得**不合格**。
4. 選取 [建立]。

現在您有兩個動作。

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>訓練模型

1. 在左面板中選取 [**訓練對話**], 然後選取 [**新增訓練對話]** 。
2. 在左側聊天面板中, 為使用者的語句輸入 [ **hi** ]。
3. 選取 [**評分動作**]。
4. 從 [動作] 清單中選取您想要的**配料**。 百分位數是 100%, 因為唯一的有效動作是以條件約束為基礎。
5. 在左側聊天面板中, 為使用者的語句輸入「**乳酪」和「蘑菇**」。
6. 反白顯示 [**乳酪**], 然後選取 [ **+ 配料**]。
7. 反白顯示**蘑菇**, 然後選取 [ **+ 配料**]。
8. 選取 [**評分動作**]。
9. 從 [動作] 清單中選取 [**以下是您的配料: $toppings** ]。
10. 在左側聊天面板中輸入使用者下一個語句的 [**新增辣椒**]。
11. 反白顯示**辣椒**, 然後選取 [ **+ 配料**]。
12. 選取 [**評分動作**]。
13. 從 [動作] 清單中選取 [**以下是您的配料: $toppings** ]。
14. 在左側聊天面板中輸入 [移除使用者的第三個語句的**乳酪**]。
15. 反白顯示 [**乳酪**], 然後選取 [ **-配料**]。
16. 選取 [**評分動作**]。
17. 從 [動作] 清單中選取 [**以下是您的配料: $toppings** ]。

![](../media/T07_training.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [預先定型的實體](./08-pre-trained-entities.md)
