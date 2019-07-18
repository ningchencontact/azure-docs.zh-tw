---
title: 何時使用列舉實體, 並使用 Conversation Learner 模型來設定實體動作-Azure 認知服務 |Microsoft Docs
titleSuffix: Azure
description: 瞭解何時適合使用列舉實體, 以及如何使用 Conversation Learner 模型來設定實體動作。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nolachar
ms.openlocfilehash: ed18d30a0c3f5d51cb3a07b8948863cdda16c1ae
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2019
ms.locfileid: "67845955"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>使用列舉實體和設定實體動作的時機

本教學課程將說明何時應使用列舉 (列舉) 實體和 SET_ENTITY 動作。

## <a name="video"></a>視訊

[![設定實體教學課程預覽](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>涵蓋的項目

本教學課程將介紹兩個新功能。 稱為 ENUM 的新實體類型 (列舉的 short) 和新的動作類型 (稱為 SET_ENTITY), 它可以參考其中一個列舉值, 顧名思義, 會將實體設定為這個值。 如下所示, 這些新功能會一起使用, 我們將在下面說明其內容和使用方式。 在我們深入探討詳細資料之前, 請務必瞭解這些功能有助於解決的問題。

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>問題

在某些情況下, 文字的意義取決於內容。  一般來說, 已加上標籤的關鍵字會使用語言理解服務來學習和解壓縮, 但是在這些情況下, 這些系統可能無法使用加上標籤的範例來學習。

假設您在附近的人之間 overhear 對話的一部分, 而您只聽到「是」這個字。 您不知道「是」是同意或確認的內容, 因為您沒有聽到問題之前所詢問的問題。 前面詢問的問題是內容, 這會對答案提供意義。 同樣地, 因為「是」是對許多不同問題的常見回應, 所以無法藉由提供範例來瞭解如何使用[自訂的定型](04-introduction-to-entities.md)實體, 因此它會學習將每個「是」標記為該實體。

### <a name="example"></a>範例

讓我們進一步說明下列範例:

Bot您喜歡 Azure 認知服務嗎？
使用者：是 Bot:您喜歡霜淇淋嗎？
使用者：是

在先前的教學課程中, 我們探討了[自訂的定型](04-introduction-to-entities.md)實體, 而您的初步想法可能是建立名為 "likesCogServices" 的實體, 並將第一個「是」標記為此實體。  不過, 系統也會標示第二個「是」。 當我們嘗試將第二個 "Yes" 的標籤更正為 "likesIceCream" 時, 我們會建立兩個相同輸入「是」的衝突, 這表示有不同的東西, 而且會停滯。

在這些情況下, 您需要使用列舉實體和 SET_ENTITY 動作。

## <a name="when-to-use-enums-or-setentity-actions"></a>使用 Enum 或 SET_ENTITY 動作的時機

請使用下列規則來瞭解何時使用列舉實體和 SET_ENTITY 動作:

- 實體的偵測或設定與內容相關
- 可能值的數目是固定的 (Yes 和 No 是兩個值)

換句話說, 您可以使用這些資訊來進行任何關閉的提示, 例如一律會產生「是」或「否」的確認問題。

> [!NOTE]
> 我們目前限制每個列舉實體最多有5個值。 每個值都會使用目前64限制中的其中一個位置。 請參閱[cl-值和界限](../cl-values-and-boundaries.md)

範例：Bot您的訂單是否正確？
使用者：是

當實體的可能值為開放式且未修正時, 您必須使用如[預期實體](05-expected-entity.md)之類的替代功能。

範例：Bot你叫什麼名字？
使用者：Matt Bot:您最愛的色彩為何？
使用者：Silver

這些提示會視為開放式結束, 因為它們可以使用任意值來回答。

## <a name="what"></a>何事

### <a name="enum-entities"></a>列舉實體

建立列舉實體的方式就像其他實體一樣。 類似于「程式設計」實體, 您無法將單字標記為這些實體。 相反地, 它們必須透過程式碼或 SET_ENTITY 動作來設定。

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>設定實體動作

如先前所述, 「設定實體」動作只是將實體設定為已知的列舉值。 您可以藉由建立 API 回呼動作, 並使用記憶體管理員將實體設定為值, 來達到相同的結果。 例如 `memory.Set(entityName, entityValue)`. 必須撰寫此程式碼並建立這些動作, 會變得既繁瑣又難以管理, 因此 Conversation Learner 有特殊的動作可協助執行此工作, 並在使用時自動產生這些動作。 將這些視為獨立的動作可保留撰寫這些功能的能力, 而不需要與 bot 中的其他動作或程式碼結合。

- 只有在參考列舉實體的值時, 才能建立設定實體動作, 因此您必須先建立列舉實體。
- [設定實體] 動作也是「非 await」, 因為它們沒有可見的輸出, 而且必須跟隨使用者可以看到的「等候」動作。
- 設定實體動作是不可變的, 這表示您無法在建立之後編輯它們。

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>自動產生動作

如果模型中有列舉實體, Conversation Learner 將會為每個可能的值建立預留位置動作, 並讓它們可在定型期間選取。 選取時, 系統會自動為您建立動作。

例如, 如果我建立一個列舉實體, 其值為 "Yes" 和 "No":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

即使未針對這個新的列舉明確建立動作, 您仍會在定型期間看到兩個可用的新動作:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>使用這些新功能建立 Bot

### <a name="requirements"></a>需求

本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

我們將建立 bot 來模擬快速食物的順序。 對於飲料和薯條 (小型/中型/大型) 大小的離散值, 以及具有「是/否」答案的確認問題。 這兩個實體都符合與內容相關的答案和固定值的兩個規則。

### <a name="create-the-model"></a>建立模型

1. 在 web UI 中, 按一下 [匯入]
2. 選取名為「教學課程-列舉-集合-實體」的教學課程

這會流覽至 [模型管理] 頁面。
請注意, 模型已包含幾個列舉實體和設定實體動作。

### <a name="create-the-first-dialog"></a>建立第一個對話方塊

1. 在左側導覽窗格中, 按一下 [訓練對話], 然後按一下 [新增訓練對話] 按鈕。
2. 當使用者輸入時, 「嗨, 我想要訂購 coke 和薯條」。
3. 按一下 [評分動作] 按鈕

   > 使用者未指定飲料或薯條的大小, 因此我們需要要求他們。

4. 選取具有回應的動作:「您喜歡什麼大小飲料？」
5. 當使用者輸入時, 「大型」
6. 按一下 [評分動作] 按鈕
7. 選取 [動作] SET_ENTITY-"drinkSize:大型
8. 選取具有回應的動作:「您喜歡什麼大小的薯條？」
9. 做為中的「使用類型」, 請將其設為媒體。
10. 按一下 [評分動作] 按鈕
11. 選取 [動作] SET_ENTITY-"friesSize:中場
12. 選取具有回應的動作:「您喜歡任何 condiments 嗎？」
13. 作為中的使用類型, 「是」
14. 按一下 [評分動作] 按鈕
15. 選取 [動作] SET_ENTITY-"condimentsConfirmation:肯定
16. 選取具有回應的動作:「好的, 我有一種大飲料和中等薯條的訂單。 這是正確的嗎？」
17. 作為中的使用類型, 「是」
18. 按一下 [評分動作] 按鈕
19. 選取 [動作] SET_ENTITY-"orderConfirmation:肯定
20. 選取具有回應的動作:「好的, 您的訂單號碼是58。 請稍候。」
21. 按一下 [儲存] 以關閉對話方塊

您剛使用列舉實體和 SET_ENTITY 動作建立了第一個對話方塊。 您可以為使用者提供更多的組合來指定部分資訊, 或試驗其他類型的封閉式問題。

> [!NOTE]
> 在定型期間, 您會看到 SET_ENTITY 動作的預留位置, 例如
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> 但在建立記錄對話或使用已部署的 bot 時, 使用者將不會看到這些。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [替代輸入](./10-alternative-inputs.md)
