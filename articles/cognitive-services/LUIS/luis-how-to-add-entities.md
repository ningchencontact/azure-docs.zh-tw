---
title: 新增實體-LUIS
titleSuffix: Azure Cognitive Services
description: 建立實體以從 Language Understanding (LUIS) 應用程式中的使用者語句, 將重要資料解壓縮。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 80e1052cb7acbdcec2dcb94f1667cae3c554d18e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932930"
---
# <a name="create-entities-without-utterances"></a>建立不含語句的實體

實體代表語句中您想要擷取的單字或片語。 實體代表一個類別, 包括類似物件的集合 (位置、事物、人員、事件或概念)。 實體描述與意圖有關的資訊，有時是您應用程式執行其工作的必備要素。 您可以在將語句新增至意圖時, 或在將語句新增至意圖時, 建立實體。

您可以在 LUIS 應用程式中，透過 [實體] 頁面上的 [實體清單] 來新增、編輯或刪除實體。 LUIS 提供兩種主要類型的實體：[預先建置的實體](luis-reference-prebuilt-entities.md)，以及您自己的[自訂實體](luis-concept-entity-types.md#types-of-entities)。

建立機器學習的實體之後, 您必須將該實體標示為其所在所有意圖的所有範例語句。

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>將預先建立的實體新增至您的應用程式

新增到應用程式的常見預先建置實體為 *number* 和 *datetimeV2*。 

1. 在應用程式的 [建置] 區段中，選取左面板中的 [實體]。
 
1. 在 [實體] 頁面上，選取 [新增預先建置的實體]。

1. 在 [新增預先建置的實體] 對話方塊中，選取 [number] 和 [datetimeV2] 預先建置的實體。 然後選取 [完成]。

    ![[新增預先建置的實體] 對話方塊的螢幕擷取畫面](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>新增單一概念的簡單實體

簡單實體能描述單一概念。 使用下列程序來建立實體，以擷取公司部門名稱，例如「人力資源」或「營運」。   

1. 在應用程式中，選取 [建置] 區段，選取左面板中的 [實體]，然後選取 [建立新實體]。

1. 在快顯對話方塊中，於 [實體名稱] 方塊中輸入 `Location`、從 [實體類型] 清單中選取 [簡單]，然後選取 [完成]。

    建立此實體後，請移至具有範例語句 (含有此實體) 的所有意圖。 選取範例語句中的文字並將此文字標示為實體。 

    [片語清單](luis-concept-feature.md)常被用來提升簡單實體的訊號。

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>新增適用于高度結構化概念的正則運算式實體

規則運算式實體可根據您提供的規則運算式，用來從語句中提取出資料。 

1. 在應用程式中，從左側瀏覽列選取 [實體]，然後選取 [建立新實體]。

1. 在快顯對話方塊中，於 [實體名稱] 方塊中輸入 `Human resources form name`、從 [實體類型] 清單中選取 [規則運算式]、輸入規則運算式 `hrf-[0-9]{6}`，然後選取 [完成]。 

    這個正則運算式會比`hrf-`對常值字元, 然後是6位數, 代表人力資源表單的表單編號。

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>將複合實體新增至父子式關聯性中的群組

您可藉由建立複合實體，以定義不同實體類型之間的關聯性。 在下列範例中，實體包含規則運算式，以及預先建置的實體名稱。  

在語句 `Send hrf-123456 to John Smith` 中，文字 `hrf-123456` 會與人力資源 [規則運算式](#add-regular-expression-entities)配對，而且會使用預先建置的實體 personName 擷取 `John Smith`。 每個實體都是較大父實體的一部分。 

1. 在應用程式中，從 [建置] 區段的左導覽列選取 [實體]，然後選取 [新增預先建置的實體]。

1. 新增預先建置的實體 **PersonName**。 如需相關指示，請參閱[新增預先建置的實體](#add-prebuilt-entity)。 

1. 從左側瀏覽列選取 [實體]，然後選取 [建立新實體]。

1. 在快顯對話方塊中，於 [實體名稱] 方塊中輸入 `SendHrForm`，然後從 [實體類型] 清單中選取 [複合]。

1. 選取 [新增子系] 來以新增子系。

1. 在 [第一個子系] 中，從清單選取實體 **number**。

1. 在 [第二個子系] 中，從清單選取實體 **Human resources form name**。 

1. 選取 [完成]。

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>新增模式。任何實體以捕獲自由形式實體

[Pattern.any](luis-concept-entity-types.md) 實體只有在[模式](luis-how-to-model-intent-pattern.md)中有效，在意圖中無效。 這類型的實體可協助 LUIS 尋找變動長度與文字選項的實體結尾。 由於此實體會在模式中使用，因此，LUIS 就能得知實體結尾是在語句範本中的何處。

如果應用程式有 `FindHumanResourcesForm` 意圖，則擷取的表單標題可能會干擾意圖預測。 若要釐清表單標題中有哪些文字，請在模式內使用 Pattern.any。 LUIS 預測會從語句開始。 首先，針對實體檢查語句並加以比對，找到實體時，接著會檢查並比對模式。 

在語句 `Where is Request relocation from employee new to the company on the server?` 中，表單標題有點棘手，因為在上下文中標題結束的地方與語句其餘部分開始的地方並不明顯。 標題可以包括任何順序的文字，包括單一文字、具有標點符號的複雜片語，以及以無意義方式排序的字組。 模式讓您能夠建立可擷取完整又準確之實體的實體。 一旦找到標題之後，就會預測 `FindHumanResourcesForm` 意圖，因為那是模式的意圖。

1. 從 [建置] 區段，選取左面板中的 [實體]，然後選取 [建立新實體]。

1. 在 [新增實體] 對話方塊中，於 [實體名稱] 方塊中輸入 `HumanResourcesFormTitle`，然後選取 [Pattern.any] 作為 [實體類型]。

    若要使用 pattern.any 實體，在 [模式] 頁面 (在 [改善應用程式效能] 區段中) 搭配正確的大括號語法來新增模式，例如 `Where is **{HumanResourcesFormTitle}** on the server?`。

    如果您發現模式在包含 Pattern.any 時所擷取的實體不正確，請使用[明確清單](luis-concept-patterns.md#explicit-lists)來更正此問題。 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>新增角色來區別不同的內容

角色是以內容為基礎的命名子型別。 其適用于所有實體, 包括預建和非機器學習的實體。 

角色的語法為 **`{Entityname:Rolename}`** , 其中的機構名稱後面接著冒號, 然後是角色名稱。 例如： `Move {personName} from {Location:Origin} to {Location:Destination}` 。

1. 從 [建置] 區段，選取左面板中的 [實體]。

1. 選取 [Create new entity] \(建立新實體\)。 輸入 `Location` 的名稱。 選取 [簡單] 類型，然後選取 [完成]。 

1. 從左面板中選取 [**實體**], 然後選取在上一個步驟中建立的新實體**位置**。

1. 在 [角色名稱] 文字方塊中，輸入角色 `Origin` 的名稱，然後按 Enter。 新增 `Destination` 的第二個角色名稱。 

    ![將 Origin 角色新增到 Location 實體的螢幕擷取畫面](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>新增完全相符專案的清單實體

清單實體代表一組固定且封閉的相關字組。 

在人力資源應用程式中，您可以有所有部門的清單，以及部門的所有同義字。 當您建立實體時，不需要知道所有值。 檢閱包含同義字的實際使用者語句之後，即可新增其他語句。

1. 從 [建置] 區段，選取左面板中的 [實體]，然後選取 [建立新實體]。

1. 在 [新增實體] 對話方塊中，於 [實體名稱] 方塊中輸入 `Department`，然後選取 [清單] 作為**實體類型**。 選取 [完成]。
  
1. 清單實體頁面可讓您新增標準化名稱。 在 [值] 文字方塊中，輸入清單的部門名稱 (例如 `HumanResources`)，然後按鍵盤上的 Enter。 

1. 在標準化值的右邊，輸入同義字，並且在輸入每個項目之後按鍵盤上的 Enter。

1. 如果您想要更多適用於該清單的標準化項目，請選取 [建議] 以查看[語意字典](luis-glossary.md#semantic-dictionary)的選項。

    ![選取建議功能的螢幕擷取畫面，以查看選項](./media/add-entities/hr-list-2.png)


1. 選取建議清單中的項目以將它新增為標準化的值，或選取 [全部新增] 以新增所有項目。 
    您可以使用下列 JSON 格式，將值匯入到現有的清單實體：

    ```JSON
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

<a name="change-entity-type"></a>

## <a name="do-not-change-entity-type"></a>不要變更實體類型

LUIS 不允許您變更實體的類型，因為它不知道要新增或移除哪些項目來建構該實體。 若要變更類型，更好的方式是使用稍微不同的名稱來建立正確類型的新實體。 一旦建立實體之後，在每個語句中，移除已加上標籤的舊實體名稱，並新增新的實體名稱。 為所有語句加上標籤之後，刪除舊實體。 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>從範例語句建立模式

請參閱[在意圖或實體頁面上從現有的語句新增模式](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)。

## <a name="train-your-app-after-changing-model-with-entities"></a>利用實體變更模型後將您的應用程式定型

在新增、編輯或移除實體之後，請將您的應用程式[定型](luis-how-to-train.md)並[發佈](luis-how-to-publish-app.md)，變更才會影響端點查詢。 

## <a name="next-steps"></a>後續步驟

如需預先建置的實體詳細資訊，請參閱 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 專案。 

如需實體如何顯示在 JSON 端點查詢回應中的相關資訊，請參閱[資料擷取](luis-concept-data-extraction.md)

既然您已新增意圖、語句和實體，您就會擁有基本的 LUIS 應用程式。 了解如何為您的應用程式[定型](luis-how-to-train.md)、[測試](luis-interactive-test.md)及[發佈](luis-how-to-publish-app.md)。
 
