---
title: 如何將實體用於對話學習模組應用程式 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何將實體用於對話學習模組應用程式。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 85df31c2e2ff3ca81698921a1f17f415daefb6c5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369590"
---
# <a name="introduction-to-entities"></a>實體簡介

本教學課程介紹實體，並說明如何在動作中使用 [不合格的實體] 和 [必要實體] 欄位。

## <a name="requirements"></a>需求

本教學課程需要執行一般教學課程 Bot

    npm run tutorial-general

## <a name="details"></a>詳細資料

本教學課程將說明實體的兩個常見用法。  首先，實體可以從使用者訊息中擷取子字串，例如在「西雅圖的天氣如何？」中識別出城市。  其次，實體可以限制動作可供使用的時機。  具體來說，動作可以將實體列示為「必要」或「不合格」的實體：
- 動作的必要實體必須存在於 Bot 的記憶體中，動作才可供使用
- 不合格的實體必須*不*存在於 Bot 的記憶體中，動作才可供使用

另有其他教學課程會說明實體的其他層面，例如預先建置的實體、多重值和 Negatable 實體、程式設計實體，以及在程式碼中操作實體。

## <a name="steps"></a>步驟

### <a name="create-the-application"></a>建立應用程式

1. 在 Web UI 中，按一下 [新增應用程式]
2. 在 [名稱] 中，輸入 IntroToEntities。 接著，按一下 [建立]。

### <a name="create-entity"></a>建立實體

1. 按一下 [實體]，然後按一下 [新增實體]。
2. 在 [實體名稱] 中，輸入城市。
3. Click Create

請注意，實體類型是「自訂」，這表示實體是可訓練的。  另外也有預先建置的實體，這表示無法調整其行為，另一個教學課程將說明這些實體。

### <a name="create-two-actions"></a>建立兩個動作

1. 按一下 [動作]，然後按一下 [新增動作]
2. 在 [回應] 中，輸入「我不知道您要的城市」。
3. 在 [不合格的實體] 中，輸入 $city。 按一下 [儲存]。
    - 這表示，如果此實體定義於 Bot 的記憶體中，這個動作即*無法*使用。
2. 按一下 [動作]，然後按一下 [新增動作] 以建立第二個動作。
3. 在 [回應] 中，輸入「'$city 的天氣可能是晴天」。
4. 在 [必要實體] 中，留意到城市實體已自動新增，因為它受到參照。
5. 按一下 [Save] \(儲存)。

現在您有兩個動作。

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>訓練 Bot

1. 按一下 [訓練對話]，然後按一下 [新增訓練對話]。
2. 輸入「您好」。
3. 按一下 [評分動作]，並選取 [我不知道您要選擇的城市?]
    - 請注意，您無法選取需要城市實體的回應，因為城市實體未定義於 Bot 的記憶體中。
2. 選取 [我不知道您要的城市]。
4. 輸入「西雅圖」。 將西雅圖醒目提示，然後按一下城市。
5. 按一下 [評分動作]
    - 請注意，城市值現已在 Bot 的記憶體中。
    - 「'$city 的天氣可能是晴天」現在已是可用的回應。 
6. 選取 ['$city 的天氣可能是晴天]。

我們假設使用者輸入「加以重複」。 
1. 輸入這些文字。 留意到城市實體及其值已在記憶體中，並可供使用。
2. 選取 ['$city 的天氣可能是晴天]。

![](../media/tutorial3_entities.PNG)

您現在已建立實體，並在使用者訊息中標示其執行個體。  您也已使用實體在 Bot 記憶體中的存在性/不存在性，透過動作的不合格和必要實體欄位來控制動作可供使用的時機。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [預期的實體](./4-expected-entity.md)
