---
title: 如何將預先定型的實體新增至對話學習模組模型 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何將預先定型的實體新增至對話學習模組模型。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: fceca459b80725e1a9c8b7dbc6a4387ea98dbb7b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704126"
---
# <a name="how-to-add-pre-trained-entities"></a>如何新增預先定型的實體
本教學課程說明如何將預先定型的實體新增至對話學習模組模型。

## <a name="video"></a>視訊

[![預先定型的實體教學課程預覽](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料

預先定型的實體可辨識常見的實體類型，例如數字、日期、貨幣金額，以及其他項目。  與自訂實體不同，它們是「現成可用」的，不需進行任何訓練，且其行為無法變更。  根據預設，預先定型的實體具有多重值，會累積每個已識別的實體執行個體。

## <a name="steps"></a>步驟

在 Web UI 中的首頁開始。

### <a name="create-the-model"></a>建立模型

1. 選取 [**新增模型**]。
2. 輸入**PretrainedEntities**作為 [**名稱**]。
3. 選取 [建立]。

### <a name="entity-creation"></a>建立實體

1. 選取左面板中的 [**實體**], 然後選取 [**新增實體**]。
2. 針對 [**實體類型**] 選取 [**預先定型/datetimeV2** ]。
3. 勾選 [**多重值**] 可讓實體累積一或多個值。 請注意, 預先定型的實體無法 negatable。
4. 選取 [建立]。

![](../media/T08_entity_create.png)

1. 在左面板中選取 [**動作**], 然後選取 [**新增動作**]。
2. 輸入**Bot 回應** **$builtin datetimev2 的日期**。
3. 選取 [建立]。

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>建立第二個動作

1. 在左面板中選取 [**動作**], 然後選取 [**新增動作**]。
2. 輸入**Bot 回應**的**日期為何？** 預先定型的實體不可以是**必要的實體**, 因為所有語句的預設值都會予以辨識。
3. 輸入**不合格實體**的內**建-datetimev2** 。
4. 選取 [建立]。

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>訓練模型

1. 在左面板中選取 [**訓練對話**], 然後選取 [**新增訓練對話]** 。
2. 在左側聊天面板中輸入**hello**作為使用者的語句。
3. 選取 [**評分動作**]。
4. 從動作清單中選取 **[日期？** ]
5. 請**立即**在左側聊天面板中輸入使用者的語句。
    - LUIS 中預先定型的模型會自動辨識**今日**語句。
    - 將滑鼠停留預先定型的實體值上方，會顯示 LUIS 所提供的其他資料。

![](../media/T08_training.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [實體解析程式](./09-entity-resolvers.md)
