---
title: 新增對應方式
titleSuffix: Language Understanding - Azure Cognitive Services
description: 將意圖新增至 LUIS 應用程式，以識別具有相同用意的問題或命令群組。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: e6ab51d568c950ca7fd503150557df288d84174f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867727"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>新增意圖以判斷語句的使用者用意

將[意圖](luis-concept-intent.md)新增至 LUIS 應用程式，以識別具有相同用意的問題或命令群組。 

從上方導覽列的 [建置] 區段，然後從左側面板的 [意圖] 管理意圖。 

## <a name="add-intent"></a>新增意圖

1. 在 [意圖] 頁面上，選取 [建立新意圖]。

1. 在 [建立新的意圖] 對話方塊中，輸入意圖名稱 `GetEmployeeInformation`，然後按一下 [完成]。

    ![新增意圖](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>新增範例語句

範例語句是使用者問題或命令的文字範例。 若要教導 Language Understanding (LUIS)，您需要將範例語句新增至意圖。

1. 在 **GetEmployeeInformation** 意圖詳細資料頁面上，於意圖名稱下面的文字方塊中輸入您預期來自使用者的相關語句，例如 `Does John Smith work in Seattle?`，然後按 Enter 鍵。
 
    ![意圖詳細資料頁面的螢幕擷取畫面 (已醒目提示語句)](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS 會將所有意圖轉換成小寫，並且在語彙基元 (例如連字號) 周圍新增空格。

## <a name="intent-prediction-discrepancy-errors"></a>意圖預測差異錯誤 

意圖中的語句在所選意圖與預測分數之間，可能會有意圖預測差異。 LUIS 會在範例語句的資料列上以紅色方塊圍住 [加上標籤的意圖] 來指出此差異。 

![意圖詳細資料頁面的螢幕擷取畫面，包含語句預測差異錯誤](./media/luis-how-to-add-intents/prediction-discrepancy-intent.png) 

在頂端導覽列中，選取 [定型]。 預測差異現在已消失。

## <a name="add-a-custom-entity"></a>新增自訂實體

語句新增至意圖後，您可以從語句中選取文字來建立自訂實體。 自訂實體是標記文字以供擷取及正確意圖的方法。 

1. 在語句中選取字組 `Seattle`。 文字周圍會加上方括號，且隨即出現下拉式功能表。 

    ![意圖詳細資料頁面 (建立自訂實體) 的螢幕擷取畫面](./media/luis-how-to-add-intents/create-custom-entity.png) 

    此範例會選取單一字組來標示為實體。 您可以將單一字組和片語標示為實體。

1. 在功能表的頂端文字方塊中，輸入 `Location`，然後選取 [建立新的實體]。 

    ![意圖詳細資料頁面 (建立自訂實體名稱) 的螢幕擷取畫面](./media/luis-how-to-add-intents/create-custom-entity-name.png) 

1. 在實體建立的 [您要建立哪種類型的實體?] 快顯視窗中，驗證**實體名稱**為 [位置]，而**實體類型**為 [簡單]。 選取 [完成] 。

## <a name="entity-prediction-discrepancy-errors"></a>意圖預測差異錯誤 

實體會加上紅色底線來表示[實體預測差異](luis-how-to-add-example-utterances.md#entity-status-predictions)。 因為這是實體第一次出現，所以 LUIS 沒有足夠的範例可高度信任此文字會標記為正確的實體。 當應用程式定型後，則會移除此差異。 

![意圖詳細資料頁面 (以藍色醒目提示自訂實體名稱) 的螢幕擷取畫面](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

文字會以藍色醒目提示，表示實體。  

## <a name="add-a-prebuilt-entity"></a>新增預先建置的實體

如需詳細資訊，請參閱[預先建置的實體](luis-how-to-add-entities.md#add-prebuilt-entity)。

## <a name="using-the-contextual-toolbar"></a>使用內容相關的工具列

若已選取清單中的一或多個範例語句，核取語句左邊的方塊，語句清單上方的工具列即可讓您執行下列動作：

* 重新指派意圖：將語句移至不同的意圖
* 刪除語句
* 實體篩選條件：只顯示包含篩選後實體的語句
* 全部顯示 / 僅限錯誤：顯示具有預測錯誤的語句或顯示所有語句
* 實體/權杖檢視：顯示具有實體名稱的實體檢視，或顯示語句未經處理的文字
* 放大鏡：搜尋含有特定文字的語句

## <a name="working-with-an-individual-utterance"></a>使用個別語句

從語句右邊的省略符號功能表，可以在個別語句上執行下列動作：

* 編輯：變更語句的文字
* 刪除：從意圖中移除語句。 如果您仍想要語句，最好的方法是將其移至 **None** 意圖。 
* 加入模式：模式可讓您採用一般語句，並標示可取代的文字和可忽略的文字，藉此減少意圖中更多語句的需求。 

[加上標籤的意圖] 資料行可讓您變更語句的意圖。

## <a name="train-your-app-after-changing-model-with-intents"></a>在利用意圖變更模型後訓練您的應用程式

在您新增、編輯或移除意圖之後，[訓練](luis-how-to-train.md)並[發佈](luis-how-to-publish-app.md)您的應用程式，變更才會套用到端點查詢。 

## <a name="next-steps"></a>後續步驟

深入了解如何新增具有實體的[範例語句](luis-how-to-add-example-utterances.md)。 
