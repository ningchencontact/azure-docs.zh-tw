---
title: 如何將預先定型的實體新增至對話學習模組模型 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何將預先定型的實體新增至對話學習模組模型。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: afa927009e684fa7f8c6217c91dcb589b331b5f5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224168"
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

### <a name="create-the-model"></a>建立模型

1. 在 Web UI 中，按一下 [新增模型]。
2. 在 [名稱] 欄位中輸入 "PretrainedEntities"，並按 Enter 鍵。
3. 按一下 [建立] 按鈕。

### <a name="entity-creation"></a>建立實體

1. 在左面板中按一下 [實體]，然後按 [新增實體] 按鈕。
2. 選取 [預先定型/datetimeV2] 作為 [實體類型]。
3. 勾選 [多重值] 核取方塊。
    - 多重值實體會在實體中累積一或多個值。
    - 預先定型的實體會停用 Negatable 屬性。
4. 按一下 [建立] 按鈕。

![](../media/tutorial7_entities_a.PNG)

### <a name="create-the-first-action"></a>建立第一個動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入 "The date is $builtin-datetimev2" (日期是 $builtin-datetimev2)
3. 按一下 [建立] 按鈕。

![](../media/tutorial7_actions_a.PNG)

### <a name="create-the-second-action"></a>建立第二個動作

1. 在左面板中按一下 [動作]，然後按 [新增動作] 按鈕。
2. 在 [聊天機器人的回應...] 欄位中，輸入 "What's the date?" (日期為何？)
    - 預先定型的實體不可以是必要實體，因為依預設對所有使用者語句都會辨識這些實體。
3. 在 [不合格的實體] 欄位中，輸入 "builtin-datetimev2"。
4. 按一下 [建立] 按鈕。

![](../media/tutorial7_actions2_a.PNG)

### <a name="train-the-model"></a>訓練模型

1. 在左面板中按一下 [訓練對話]，然後按 [新增訓練對話] 按鈕。
2. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "hello" (您好)。
3. 按一下 [評分動作] 按鈕。
4. 選取回應：「日期為何？」
5. 在聊天面板中 (此處會顯示「輸入您的訊息...」)，輸入 "today" (今天)
    - LUIS 中預先定型的模型會自動辨識今天的語句。
    - 將滑鼠停留預先定型的實體值上方，會顯示 LUIS 所提供的其他資料。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [實體解析程式](./09-entity-resolvers.md)
