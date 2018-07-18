---
title: 如何將預先建立的實體新增至對話學習模組應用程式 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何將預先建立的實體新增至對話學習模組應用程式。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f014464419bfac39a9e57e679fcd28a737e9ebdb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370314"
---
# <a name="how-to-add-pre-built-entities"></a>如何新增預先建立的實體
本教學課程示範如何將「預先建立」的實體新增至對話學習模組應用程式。

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程 Bot

    npm run tutorial-general

## <a name="details"></a>詳細資料

預先建立的實體可辨識常見的實體類型，例如數字、日期、貨幣金額，以及其他項目。  與自訂實體不同，它們是「現成」可用，並不需要進行任何定型作業。  與自訂實體不同，您無法變更其行為。  根據預設，預先建立的實體是多重值；亦即，Bot 記憶體將會累積實體的每個已識別執行個體。

## <a name="steps"></a>步驟

### <a name="create-the-application"></a>建立應用程式

1. 在 Web UI 中，按一下 [新增應用程式]
2. 在 [名稱] 中，輸入 BuiltInEntities。 然後按一下 [建立]。

### <a name="create-an-entity"></a>建立實體

1. 按一下 [實體]，然後按一下 [新增實體]。
2. 按一下 EntityType 下拉式清單，並選取 datetimev2。
    - 將會停用 [可程式化] 和 [不可取消] 選項，因為它們不會套用到預先建置的實體。
3. 按一下 [建立]。

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>建立兩個動作

1. 按一下 [動作]，然後按一下 [新增動作]
2. 在 [回應] 中，鍵入「日期是 $luis-datetimev2」。
3. 按一下 [建立]。

![](../media/tutorial7_actions.PNG)

然後建立第二個動作：

1. 按一下 [動作]，然後按一下 [新增動作] 建立第二個動作。
3. 在 [回應] 中，鍵入「日期為何？」。
4. 在 [不合格的實體] 中，輸入 'luis-datetimev2'。
4. Click Create

![](../media/tutorial7_actions2.PNG)

現在您有兩個動作。

### <a name="train-the-bot"></a>將 Bot 定型

1. 按一下 [定型] 對話方塊，然後按一下 [新增定型] 對話方塊。
2. 鍵入「您好」。
3. 按一下 [分數動作]，並選取 [日期為何？]
2. 輸入「今天」。 
    - 請注意，今天已加上標籤，並顯示在第二行，因為它是預先建立的實體，而且不可編輯。
5. 按一下 [分數動作]
    - 請注意，日期現在會出現在 [實體記憶體] 區段中。 
    - 如果您將滑鼠移到日期上，則會看到 LUIS 所提供的其他資料，這些資料可供使用，而且可以進一步在程式碼中操作。 
6. 選取「日期是 $luis-datetimev2」。
7. 按一下 [完成教學]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [替代輸入](./8-alternative-inputs.md)
