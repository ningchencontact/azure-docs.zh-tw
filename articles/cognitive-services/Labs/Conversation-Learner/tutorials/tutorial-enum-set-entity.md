---
title: 何時使用列舉實體和設定實體動作與對話學習模組模型-Azure 認知服務 |Microsoft Docs
titleSuffix: Azure
description: 了解適合搭配對話學習模組模型使用列舉實體和設定實體動作的時機。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1393d0bd1c31a2c9c24652e260ef7f3182d91367
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480803"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>何時使用列舉實體和設定實體動作

本教學課程將說明何時應該使用列舉 (enumeration) 的實體和 SET_ENTITY 動作。

## <a name="video"></a>影片

[![設定實體的教學課程預覽](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>涵蓋的項目

本教學課程將介紹兩項新功能。 新的實體類型稱為列舉 （簡稱為列舉型別） 和新類型的動作稱為 SET_ENTITY，它可以參考其中一個列舉值，並如同名稱所暗示，請為此值設定的實體。 當您將了解以下，一起使用這些新功能，我們將說明它們是什麼，以及如何使用下列。 我們會詳細說明之前，請務必了解這些功能有助於解決哪些問題。

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>問題

交談的單字的意義取決於內容中，有一些情況。  加上標籤的關鍵字通常被學習，而且擷取使用 language understanding 服務，但在這些情況下這些系統可能無法以了解使用加上標籤的範例。

假設 overhear 近端的人員之間的交談的一部分，而您只會聽到字"Yes"。 您就無法得知已同意的 []，或確認您沒聽到這個問題，因為要求之前。 之前的疑問是內容中，會提供答案的意義。 同樣地會因為 [是] 是許多不同的問題不一樣，提供範例了解這類的常見回應[自訂定型](04-introduction-to-entities.md)實體因為然後它會了解來標示每個 「 是 」 作為該實體。

### <a name="example"></a>範例

讓我們進一步釐清與下列範例：

Bot:您喜歡 Azure 認知服務嗎？
使用者：是 Bot:您喜歡冰淇淋嗎？
使用者：是

在上一個教學課程中，我們探討[自訂定型](04-introduction-to-entities.md)實體和您的初始考量可能會建立名為"likesCogServices 」 實體，並加上標籤第一個"Yes"時為此實體。  不過，系統會也加上標籤，第二個"Yes"。 當我們嘗試更正"Yes"到"likesIceCream 」 的第二個標籤時，我們會再造成衝突的兩個相同的輸入 「 是 」 意義不同的項目，並會停滯。

它是在這些情況下，您必須使用列舉實體和 SET_ENTITY 動作。

## <a name="when-to-use-enums-or-setentity-actions"></a>何時使用列舉或 SET_ENTITY 動作

若要了解何時使用列舉實體和 SET_ENTITY 動作中使用下列這些規則：

- 偵測或實體的設定與內容相關
- 在固定數目的可能值 （是，並不會是兩個值）

換句話說，使用這些來進行任何關閉結束的提示，例如確認問題，一定會產生 Yes 或 no。

> [!NOTE]
> 我們目前有每個列舉實體最多 5 個值的限制。 每個值會使用其中一個目前 64 限制中的位置。 請參閱[cl-值-和-界限](../cl-values-and-boundaries.md)

範例：Bot:您的訂單是正確的？
使用者：是

當實體的可能值會是開放式且不固定時，您必須使用替代功能，例如[預期實體](05-expected-entity.md)。

範例：Bot:您的姓名為何？
使用者：Matt Bot:什麼是您最愛的色彩？
使用者：Silver

這些提示會被視為開放式，因為它們無法回答任意值。

## <a name="what"></a>何事

### <a name="enum-entities"></a>列舉實體

列舉的實體會建立如同其他任何實體。 類似於 「 程式設計 」 實體，您無法為這些實體標籤文字。 相反地，它們必須透過程式碼或 SET_ENTITY 動作設定。

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>設定實體動作。

如先前所述，「 設定實體 」 動作只是將實體設定為已知的列舉值。 您可以藉由建立 API 回呼的動作，並使用記憶體管理員設為值的實體來達到相同的結果。 例如 `memory.Set(entityName, entityValue)` 。 不必撰寫此程式碼，並建立這些動作會變得既繁瑣又難管理-因此對話學習模組必須採取特殊動作來協助這項工作，以及使用時，自動產生這些動作。 擁有這些做為獨立的動作會保留這些不在結合其他動作或在您的機器人的程式碼撰寫的能力。

- 設定參考實體的一個列舉值，因此您必須先建立列舉實體時，可以只建立動作的實體。
- 設定實體動作是也非-await > 因為它們有沒有可見的輸出，而且需要持續追蹤使用者會看到 「 等候 」 動作。
- 設定實體動作是固定不變，這表示您無法在建立後加以編輯的。

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>自動動作產生

如果列舉實體存在於您的模型，對話學習模組建立預留位置每個可能值的動作，並將其提供給在訓練期間選取。 在 選取項目，動作會自動會為您建立。

例如，如果我建立列舉實體的值"Yes"和"No":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

即使沒有明確地建立這個新的列舉的動作中，您會看到兩個新的動作可在定型期間：

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>建立使用這些新功能的機器人

### <a name="requirements"></a>需求

本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

我們將建立模擬的速食排序 bot。 它會有離散值的大小以及 drinks 薯條 (SMALL/MEDIUM/LARGE)，並確認問題使用是 / 沒有解答。 這兩個這些實體符合上述兩個規則的內容相關的答案和固定的值。

### <a name="create-the-model"></a>建立模型

1. 在 web UI 中，按一下 「 匯入 」
2. 選取名為 「 教學課程-列舉-集-實體 」 教學課程

這會將您巡覽至 [模型] 管理頁面。
請注意，模型已經包含一些列舉項目，並設定實體的動作。

### <a name="create-the-first-dialog"></a>建立第一個對話方塊

1. 左方瀏覽窗格中，按一下 「 訓練對話方塊，然後按一下 「 新訓練對話方塊"按鈕。
2. 作為中的使用者類型，「 大家好，我想要請訂購 coke 薯條"。
3. 按一下 [分數動作] 按鈕

   > 使用者尚未指定飲料的大小或薯條，因此我們必須要求他們。

4. 選取回應動作：「 何種大小喝？ 」
5. 做為使用者類型中，「 大型 」
6. 按一下 [分數動作] 按鈕
7. 選取動作 SET_ENTITY-"drinkSize:大型 」
8. 選取回應動作：「 何種大小薯條嗎？ 」
9. 作為使用中的類型，「 嗯，讓那些在媒體。
10. 按一下 [分數動作] 按鈕
11. 選取動作 SET_ENTITY-"friesSize:中 」
12. 選取回應動作：「 您要讓任何 「 調味品 」 嗎？ 」
13. 作為使用中的類型，[是]
14. 按一下 [分數動作] 按鈕
15. 選取動作 SET_ENTITY-"condimentsConfirmation:[是]
16. 選取回應動作：「 好，我有大型的飲料和中型薯條順序。 是這樣的想法正確嗎？ 」
17. 作為使用中的類型，[是]
18. 按一下 [分數動作] 按鈕
19. 選取動作 SET_ENTITY-"orderConfirmation:[是]
20. 選取回應動作：「 好，您的訂單編號是 58。 請稍候那邊。 」
21. 按一下 [儲存] 以關閉對話方塊

您剛才建立您第一次使用列舉實體和 SET_ENTITY 動作的對話方塊。 您可以讓使用者指定部分的資訊或試驗其他類型的 [關閉] 結束問題的更多的組合。

> [!NOTE]
> 在定型期間您會看到預留位置 SET_ENTITY 動作類似
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> 但是，當建立對話方塊或使用部署記錄 bot 使用者將不會看到這些。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [替代輸入](./10-alternative-inputs.md)
