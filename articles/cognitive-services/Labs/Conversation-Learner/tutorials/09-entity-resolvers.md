---
title: 對話學習模組模型-Azure 認知服務中的實體解析程式 |Microsoft Docs
titleSuffix: Azure
description: 了解如何使用 Conversation Learner 中的實體解析程式。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 51f74f504f0ad70c8c7f73be8ee6a05add685824
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475748"
---
# <a name="entity-resolvers"></a>實體解析程式

本教學課程將說明如何使用 Conversation Learner 中的實體解析程式

## <a name="video"></a>影片

[![實體解析程式教學課程預覽](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料

- 解析程式類型是自訂實體的選用屬性。
- 實體解析程式會利用 LUIS 中預先訓練的實體辨識器功能，為您的自訂實體提供其他詳細資料和解析。

## <a name="steps"></a>步驟

在 Web UI 中的首頁開始。

### <a name="create-the-model"></a>建立模型

1. 選取 **新的模型**。
2. 請輸入**實體的解析程式**for**名稱**。
3. 選取 [建立]  。

### <a name="create-a-pair-of-entities"></a>建立一組實體

1. 選取 **實體**在左窗格中，然後**新實體**。
2. 請輸入**起飛**for**實體名稱**。
3. 選取  **datetimeV2** for**解析程式類型**。
4. 選取 [建立]  。 關閉選取的資訊快顯**確定**。
5. 重複步驟 1-4，以建立名為的第二個實體**會傳回**具有**datetimeV2**解析程式類型。

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>建立一組動作

1. 選取 **動作**在左窗格中，然後**新動作**。
2. 請輸入**您會在 $departure 留下，並傳回 $return**如**機器人的回應...** .
    - 重要事項-輸入您要叫用的 $[entityName] 中輸入或按一下下拉式清單則對話學習模組中的實體會認為這是文字，而不是實體時。
    - 請注意，**所需的實體**欄位也會取得這些實體，且無法移除它們。 這可防止在兩個必要實體都存在之前執行此動作。
3. 選取 [建立]  。
4. 選取 **新的動作**建立第二個動作。
5. 輸入**當您打算移動？** 如**機器人的回應...** .
6. 請輸入**起飛**並**傳回**如**Disqualifying 實體**。 這些內容會告訴 Bot，當這些實體中任何一個包含值的時候，「不要」採取此動作。
7. 選取 [建立]  。

![](../media/T09_actions.png)

### <a name="training"></a>訓練

1. 監看式**訓練: [狀態]** 中的左上角**已完成**。
    - 如果費時太長，您可以按一下 [重新整理] 連結。
    - 訓練狀態必須是「已完成」，我們的實體解析程式才能在訓練模型時運作。

2. 在左側面板中，按一下 [訓練對話方塊]，然後按一下 [新增訓練對話方塊] 按鈕。
3. 在第一個使用者語句中輸入「為我預定班機」。 
4. 按一下 [評分動作] 按鈕。
5. 選取回應：「您預計何時出遊？」。
6. 以使用者身分回應「明天離開並在下週日返回」。
    - 請注意 Conversation Learner 如何在該使用者對話中偵測到兩個「預先訓練的日期」實體。
7. 在 [實體偵測] 面板中，選取「明天」這兩個字，並將其標示為「出發」
8. 並也把「下週日」這幾個字標示為「返回」
9. 按一下 [評分動作] 按鈕。
    - 請注意「記憶」窗格會如何包含您的出發和返回日期。
    - 暫留在每一項，並觀察之實體的日期物件可清楚地擷取實際的行事曆日期，而不是 「 星期日 」 或 「 明天 」 的方式。
10. 選取 [您預計的出發日期為...]Bot 回應。
11. 按一下 [儲存] 按鈕。

![](../media/T09_training.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [列舉實體](./tutorial-enum-set-entity.md)
