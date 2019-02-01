---
title: Conversation Learner 模型中的實體解析程式 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何使用 Conversation Learner 中的實體解析程式。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e6a671470a87f4509e466bcdfe7845b7bf7ec8dc
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209141"
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

### <a name="create-a-new-model"></a>建立新模型

1. 在 Web UI 中，按一下 [新增模型] 按鈕。
2. 在 [名稱] 欄位中輸入 [實體解析程式]，然後按下 Enter，或按一下 [建立] 按鈕。

### <a name="create-a-pair-of-entities"></a>建立一組實體

1. 在左側面板中，按一下 [實體]，然後按一下 [新增實體] 按鈕。
2. 在 [實體名稱] 欄位中，輸入「出發」。
3. 在 [解析程式類型] 下拉式清單中，選取 [datetimeV2]。
4. 按一下 [建立] 按鈕。
5. 在閱讀資訊快顯視窗之後，按一下 [確定] 按鈕。
6. 依照相同的步驟，建立另一個名為「回程」的實體，並且也使用 [datetimeV2] 解析程式類型。

### <a name="create-a-pair-of-actions"></a>建立一組動作

1. 在左側面板中，按一下 [動作]，然後按一下 [新增動作] 按鈕。
2. 在 [Bot 回應] 欄位中，輸入「您預計的出發日期為 $departure 並在 $return 返回」。
    - 重要 - 輸入 "$[entityName]" 時，您必須按下 Enter 或按一下下拉清單中的實體，否則 Conversation Learner 會認為這是文字，而不是實體。
    - 請注意，「所需實體」欄位也會取得這些實體，而且不能移除。 這可防止在兩個必要實體都存在之前執行此動作。
3. 按一下 [建立] 按鈕。
4. 再次按一下 [新增動作] 按鈕，以建立第二個動作。
5. 在 [Bot 回應] 欄位中，輸入「您預計何時出遊？」。
6. 在 [不合格實體] 欄位中，輸入「出發」，也輸入「返回」。
    - 這些內容會告訴 Bot，當這些實體中任何一個包含值的時候，「不要」採取此動作。
7. 按一下 [建立] 按鈕。


### <a name="training"></a>訓練

1. 觀看頁面左上角的「訓練：[狀態]」，並等候該作業「完成」。
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
    - 暫留在每一個項目上，並觀察實體為何是清楚擷取實際日曆日期的日期物件，而不是「週日」或「明天」。
10. 選取 [您預計的出發日期為...]Bot 回應。
11. 按一下 [儲存] 按鈕。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [替代輸入](./10-alternative-inputs.md)
