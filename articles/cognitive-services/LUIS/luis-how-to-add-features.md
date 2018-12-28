---
title: 片語清單
titleSuffix: Language Understanding - Azure Cognitive Services
description: 使用 Language Understanding (LUIS) 來新增應用程式功能，可改善對類別和模式進行意圖和實體的偵測或預測
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 39a6ead041b1783a3effcb9659c59ea788cd2bf6
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139498"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>使用片語清單來提升字組清單訊號

您可以將功能新增到 LUIS 應用程式來改善它的準確性。 這些功能協助 LUIS 的方式是提供某些字組和片語屬於某個應用程式網域詞彙的提示。 

[片語清單](luis-concept-feature.md)包含一組屬於相同類別的值 (單字或片語)，而且必須以類似的方式處理 (例如城市或產品的名稱)。 LUIS 對於其中一個值的認識也會自動套用到其他值。 這個清單不是相符文字的封閉清單實體 (全文相符項目)。

片語清單會新增至應用程式網域的詞彙，作為向 LUIS 指示這些字的第二個信號。

## <a name="add-phrase-list"></a>新增片語清單

1. 在 [我的應用程式] 頁面上按一下應用程式的名稱，接著按一下 [建置]，然後在應用程式的左窗格中按一下 [片語清單]，即可開啟您的應用程式。 

2. 在 [片語清單] 頁面上，按一下 [建立新的片語清單]。 
 
3. 在 [新增片語清單] 對話方塊中，輸入 "Cities" (城市) 作為片語清單的名稱。 在 [值] 方塊中，輸入片語清單的值。 您可以一次輸入一個值，或輸入一組以逗號分隔的值，然後按 **Enter**。

    ![新增片語清單 Cities](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS 可以提議要新增到片語清單的相關值。 按一下 [建議]，以取得一組語意上與已新增值相關的提議值。 您可以按一下任何提議的值，或按一下 [全部新增] 以將它們全部新增。

    ![片語清單提議的值 - 全部加入](./media/luis-add-features/related-values.png)

5. 如果已新增的片語清單值是可交換使用的替代項，請按一下 [這些值是可交換的]。

    ![片語清單建議值 - 選取 [可交換的] 方塊 ](./media/luis-add-features/interchangeable.png)

6. 按一下 [檔案] 。 "Cities" 片語清單會新增到 [片語清單] 頁面。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 您可以從 [片語清單] 頁面上的關聯式工具列刪除或停用片語清單。

## <a name="next-steps"></a>後續步驟

新增、編輯、刪除或停用片語清單之後，請再次[將應用程式定型和測試](luis-interactive-test.md)，以查看效能是否改善。
