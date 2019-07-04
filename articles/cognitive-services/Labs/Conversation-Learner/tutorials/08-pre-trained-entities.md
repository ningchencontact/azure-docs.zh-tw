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
ms.openlocfilehash: fb70983c2f9fd20368bb8c6803c9568b27141af7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389263"
---
# <a name="how-to-add-pre-trained-entities"></a>如何新增預先定型的實體
本教學課程說明如何將預先定型的實體新增至對話學習模組模型。

## <a name="video"></a>影片

[![預先定型的實體教學課程預覽](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料

預先定型的實體可辨識常見的實體類型，例如數字、日期、貨幣金額，以及其他項目。  與自訂實體不同，它們是「現成可用」的，不需進行任何訓練，且其行為無法變更。  根據預設，預先定型的實體具有多重值，會累積每個已識別的實體執行個體。

## <a name="steps"></a>步驟

在 Web UI 中的首頁開始。

### <a name="create-the-model"></a>建立模型

1. 選取 **新的模型**。
2. 請輸入**PretrainedEntities** for**名稱**。
3. 選取 [建立]  。

### <a name="entity-creation"></a>建立實體

1. 選取 **實體**在左窗格中，然後**新實體**。
2. 選取 **前 Trained/datetimeV2** for**實體類型**。
3. 請檢查**多重值**啟用實體累積一或多個值。 請注意，無法 negatable Pre-Trained 實體。
4. 選取 [建立]  。

![](../media/T08_entity_create.png)

1. 選取 **動作**在左窗格中，然後**新動作**。
2. 請輸入**的日期是 $builtin-datetimev2**如**機器人的回應...** .
3. 選取 [建立]  。

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>建立第二個動作

1. 選取 **動作**在左窗格中，然後**新動作**。
2. 請輸入**什麼是日期？** 如**機器人的回應...** .預先定型的實體不能**所需的實體**因為預設會針對所有的發音都辨識。
3. 請輸入**builtin datetimev2** for **Disqualifying 實體**。
4. 選取 [建立]  。

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>訓練模型

1. 選取 **定型對話方塊**在左窗格中，然後**新的訓練對話方塊**。
2. 請輸入**hello**針對使用者的 utterance 左側的對談 面板中。
3. 選取 **評分動作**。
4. 選取 **什麼是日期？** 從動作清單
5. 請輸入**今天**針對使用者的 utterance 左側的對談 面板中。
    - **今天**LUIS 上預先定型的模型會自動辨識 [utterance]。
    - 將滑鼠停留預先定型的實體值上方，會顯示 LUIS 所提供的其他資料。

![](../media/T08_training.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [實體解析程式](./09-entity-resolvers.md)
