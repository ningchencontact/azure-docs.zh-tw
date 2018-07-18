---
title: 如何將多重值實體用於對話學習模組應用程式 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何將多重值實體用於對話學習模組應用程式。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 865b50747b2c9574b5f88d4902bea9e4c8e0e032
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369594"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-application"></a>如何將多重值實體用於對話學習模組應用程式
本教學課程說明實體的「多重值」屬性。

##<a name="requirements"></a>需求
本教學課程需要執行一般教學課程 Bot

    npm run tutorial-general

## <a name="details"></a>詳細資料
「多重值」的實體會在清單中累積其值，而不是儲存單一值。  這適用於可讓使用者指定多個值的實體，例如披薩的配料。

具體而言，如果實體已標示為「多重值」，則該實體每個已辨識的執行個體都將附加至 Bot 的記憶體清單中 (而非覆寫單一實體值)。

## <a name="steps"></a>步驟

### <a name="create-the-application"></a>建立應用程式

1. 在 Web UI 中，按一下 [新增應用程式]
2. 在 [名稱] 中輸入 MultiValueEntities。 接著，按一下 [建立]。

### <a name="create-an-entity"></a>建立實體

1. 按一下 [實體]，然後按一下 [新增實體]。
2. 在 [實體名稱] 中，輸入「配料」。
3. 勾選 [多重值]。
    - 多重值實體會在實體中累積一或多個值。
2. 勾選 [Negatable]。  
    - 這可讓使用者從他們累積的披薩配料清單中移除配料。
3. 按一下 [建立]。

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>建立兩個動作

1. 按一下 [動作]，然後按一下 [新增動作]
2. 在 [回應] 中，輸入「您想要哪些配料？」。
3. 在 [不合格的實體] 中，輸入「配料」。
3. Click Create

然後建立第二個動作。

1. 按一下 [動作]，然後按一下 [新增動作] 以建立第二個動作。
3. 在 [回應] 中，輸入「以下是您的配料：$Toppings」。
4. Click Create

現在您有兩個動作。

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>訓練 Bot

1. 按一下 [訓練對話]，然後按一下 [新增訓練對話]。
2. 輸入「您好」。
3. 按一下 [評分動作]，然後選取 [您想要哪些配料?]
2. 輸入「蘑菇和起士」。 
    - 您可以標示零個、一個或多個實體。
3. 按一下 [蘑菇]，然後選取 [配料]。
4. 按一下 [起司]，然後選取 [配料]。
5. 按一下 [評分動作]
    - 留意到「配料」實體中現在會有兩個值。 
6. 選取 [以下是您的配料：$Toppings]。

我們可以新增更多項目：

7. 輸入「新增辣椒」。
    - 在 [實體偵測] 下按一下 [辣椒]，並選取 [配料]。
3. 按一下 [評分動作]。
    - 留意到辣椒現在會顯示為 [配料] 中新加入的值。
6. 選取 [以下是您的配料：$Toppings]。

接下來我們要移除一項配料，並新增一項配料：

2. 輸入「移除辣椒並新增臘腸」。
1. 按一下 [辣椒]，然後按一下紅色的 x 加以移除。
2. 按一下 [辣椒]，然後選取 [-配料]。
3. 按一下 [評分動作]。
    - 留意到此時已刪除「辣椒」，並新增「臘腸」。
6. 選取 [以下是您的配料：$Toppings]。

現在我們要試著移除所有項目：

6. 輸入「移除蘑菇、移除起司和移除臘腸」。
7. 分別按一下以上三項，然後選取 [-配料]。
7. 按一下 [評分動作]。
    - 留意到所有配料皆已清除。
2. 選取 [您想要哪些配料？]
3. 按一下 [完成教導]

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [內建實體](./7-built-in-entities.md)
