---
title: 在 LUIS 應用程式中新增實體
titleSuffix: Azure Cognitive Services
description: 在 Language Understanding (LUIS) 應用程式中新增實體 (您應用程式定義域中的關鍵資料)。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/24/2018
ms.author: diberry
ms.openlocfilehash: 5beb952c2330d44ffe090fc422afa6da67376b0d
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288045"
---
# <a name="create-entities-without-utterances"></a>建立不含語句的實體

實體代表語句中您想要擷取的單字或片語。 一個語句可以包含許多實體，也可以完全不包含實體。 一個實體代表一個類別，其中包含相似物件 (地點、物件、人員、事件或概念) 的集合。 實體描述與意圖有關的資訊，有時是您應用程式執行其工作的必備要素。 

您可以在建立意圖之前建立實體，或分開建立。

您可以在 LUIS 應用程式中，透過 [實體] 頁面上的 [實體清單] 來新增、編輯或刪除實體。 LUIS 提供兩種主要類型的實體：[預先建置的實體](luis-reference-prebuilt-entities.md)，以及您自己的[自訂實體](luis-concept-entity-types.md#types-of-entities)。

建立實體後，即可從 [意圖] 詳細資料頁面中意圖的範例語句中標記該實體。 

## <a name="add-prebuilt-entity"></a>新增預先建置的實體

新增到應用程式的常見預先建置實體為 *number* 和 *datetimeV2*。 

1. 在應用程式的 [建置] 區段中，選取左面板中的 [實體]。
 
1. 在 [實體] 頁面上，選取 [新增預先建置的實體]。

1. 在 [新增預先建置的實體] 對話方塊中，選取 [number] 和 [datetimeV2] 預先建置的實體。 然後選取 [完成]。

    ![[新增預先建置的實體] 對話方塊的螢幕擷取畫面](./media/add-entities/list-of-prebuilt-entities.png)

## <a name="add-simple-entities"></a>新增簡單實體

簡單實體能描述單一概念。 使用下列程序來建立實體，以擷取公司部門名稱，例如「人力資源」或「營運」。   

1. 在應用程式中，選取 [建置] 區段，選取左面板中的 [實體]，然後選取 [建立新實體]。

1. 在快顯對話方塊中，於 [實體名稱] 方塊中輸入 `Location`、從 [實體類型] 清單中選取 [簡單]，然後選取 [完成]。

    建立此實體後，請移至具有範例語句 (含有此實體) 的所有意圖。 選取範例語句中的文字並將此文字標示為實體。 

    [片語清單](luis-concept-feature.md)常被用來提升簡單實體的訊號。

## <a name="add-regular-expression-entities"></a>新增規則運算式實體

規則運算式實體可根據您提供的規則運算式，用來從語句中提取出資料。 

1. 在應用程式中，從左側瀏覽列選取 [實體]，然後選取 [建立新實體]。

1. 在快顯對話方塊中，於 [實體名稱] 方塊中輸入 `Human resources form name`、從 [實體類型] 清單中選取 [規則運算式]、輸入規則運算式 `hrf-[0-9]{6}`，然後選取 [完成]。 

    此規則運算式常值字元 `hrf-`，其後 6 位數表示人力資源表單的表單號碼。

## <a name="add-hierarchical-entities"></a>新增階層式實體

階層式實體是內容學習和內容相關實體的分類。 在下列範例中，實體包含來源和目的地位置。 

在語句 `Move John Smith from Seattle to Cairo` 中，Seattle 是來源位置，而 Cairo 是目的地位置。 每個位置的內容都不同，並且學習自語句中的字組順序和字組選擇。

若要新增階層式實體，請完成下列步驟： 

1. 在應用程式中，從左側瀏覽列選取 [實體]，然後選取 [建立新實體]。

1. 在快顯對話方塊中，於 [實體名稱] 方塊中輸入 `Location`，然後從 [實體類型] 清單中選取 [階層式]。

    ![新增階層式實體](./media/add-entities/hier-location-entity-creation.png)

1. 選取 [新增子系]，然後在 [第一個子系] 方塊中輸入 `Origin`。 

1. 選取 [新增子系]，然後在 [第二個子系] 方塊中輸入 `Destination`。 選取 [完成] 。

    >[!CAUTION]
    >在單一應用程式的所有實體中，子實體名稱必須是唯一的。 兩個不同的階層式實體不能包含同名的子實體。 

    建立此實體後，請移至具有範例語句 (含有此實體) 的所有意圖。 選取範例語句中的文字並將此文字標示為實體。 

## <a name="add-composite-entities"></a>新增複合實體

您可藉由建立複合實體，以定義不同實體類型之間的關聯性。 在下列範例中，實體包含規則運算式，以及預先建置的實體名稱。  

在語句 `Send hrf-123456 to John Smith` 中，文字 `hrf-123456` 會與人力資源 [規則運算式](#add-regular-expression-entities)配對，而且會使用預先建置的實體 personName 擷取 `John Smith`。 每個實體都是較大父實體的一部分。 

1. 在應用程式中，從 [建置] 區段的左導覽列選取 [實體]，然後選取 [新增預先建置的實體]。

1. 新增預先建置的實體 **PersonName**。 如需相關指示，請參閱[新增預先建置的實體](#add-prebuilt-entity)。 

1. 從左側瀏覽列選取 [實體]，然後選取 [建立新實體]。

1. 在快顯對話方塊中，於 [實體名稱] 方塊中輸入 `SendHrForm`，然後從 [實體類型] 清單中選取 [複合]。

1. 選取 [新增子系] 來以新增子系。

1. 在 [第一個子系] 中，從清單選取實體 **number**。

1. 在 [第二個子系] 中，從清單選取實體 **Human resources form name**。 

1. 選取 [完成] 。

## <a name="add-patternany-entities"></a>新增 pattern.any 實體

[Pattern.any](luis-concept-entity-types.md) 實體只有在[模式](luis-how-to-model-intent-pattern.md)中有效，在意圖中無效。 這類型的實體可協助 LUIS 尋找變動長度與文字選項的實體結尾。 由於此實體會在模式中使用，因此，LUIS 就能得知實體結尾是在語句範本中的何處。

如果應用程式有 `FindHumanResourcesForm` 意圖，則擷取的表單標題可能會干擾意圖預測。 若要釐清表單標題中有哪些文字，請在模式內使用 Pattern.any。 LUIS 預測會從語句開始。 首先，針對實體檢查語句並加以比對，找到實體時，接著會檢查並比對模式。 

在語句 `Where is Request relocation from employee new to the company on the server?` 中，表單標題有點棘手，因為在上下文中標題結束的地方與語句其餘部分開始的地方並不明顯。 標題可以包括任何順序的文字，包括單一文字、具有標點符號的複雜片語，以及以無意義方式排序的字組。 模式讓您能夠建立可擷取完整又準確之實體的實體。 一旦找到標題之後，就會預測 `FindHumanResourcesForm` 意圖，因為那是模式的意圖。

1. 從 [建置] 區段，選取左面板中的 [實體]，然後選取 [建立新實體]。

1. 在 [新增實體] 對話方塊中，於 [實體名稱] 方塊中輸入 `HumanResourcesFormTitle`，然後選取 [Pattern.any] 作為 [實體類型]。

    若要使用 pattern.any 實體，在 [模式] 頁面 (在 [改善應用程式效能] 區段中) 搭配正確的大括號語法來新增模式，例如 `Where is **{HumanResourcesFormTitle}** on the server?`。

    如果您發現模式在包含 Pattern.any 時所擷取的實體不正確，請使用[明確清單](luis-concept-patterns.md#explicit-lists)來更正此問題。 

## <a name="add-a-role-to-pattern-based-entity"></a>將角色新增到以模式為基礎的實體

角色是以內容為基礎之實體的具名子類型。 它相當於[階層式](#add-hierarchical-entities)實體，但角色只適用於[模式](luis-how-to-model-intent-pattern.md)。 

使用相同範例作為來源和目的地城市的階層式實體時，差異在於角色會命名為來源，而不是階層式子系。 

角色的語法為 **{Entity name:Rolename}**，其中的實體名稱後面會接著冒號，然後是角色名稱。 例如： `Move {personName} from {LocationUsingRoles:Origin} to {LocationUsingRoles:Destination}`。

1. 從 [建置] 區段，選取左面板中的 [實體]。

1. 選取 [Create new entity] \(建立新實體\)。 輸入 `LocationUsingRoles` 的名稱。 選取 [簡單] 類型，然後選取 [完成]。 

1. 從左面板中選取 [實體]，然後選取在上一步中建立的新實體 **LocationUsingRoles**。

1. 在 [角色名稱] 文字方塊中，輸入角色 `Origin` 的名稱，然後按 Enter。 新增 `Destination` 的第二個角色名稱。 

    ![將 Origin 角色新增到 Location 實體的螢幕擷取畫面](./media/add-entities/roles-enter-role-name-text.png)

## <a name="add-list-entities"></a>新增清單實體

清單實體代表一組固定且封閉的相關字組。 

在人力資源應用程式中，您可以有所有部門的清單，以及部門的所有同義字。 當您建立實體時，不需要知道所有值。 檢閱包含同義字的實際使用者語句之後，即可新增其他語句。

1. 從 [建置] 區段，選取左面板中的 [實體]，然後選取 [建立新實體]。

1. 在 [新增實體] 對話方塊中，於 [實體名稱] 方塊中輸入 `Department`，然後選取 [清單] 作為**實體類型**。 選取 [完成] 。
  
1. 清單實體頁面可讓您新增標準化名稱。 在 [值] 文字方塊中，輸入清單的部門名稱 (例如 `HumanResources`)，然後按鍵盤上的 Enter。 

1. 在標準化值的右邊，輸入同義字，並且在輸入每個項目之後按鍵盤上的 Enter。

1. 如果您想要更多適用於該清單的標準化項目，請選取 [建議] 以查看[語意字典](luis-glossary.md#semantic-dictionary)的選項。

    ![將 Origin 角色新增到 Location 實體的螢幕擷取畫面](./media/add-entities/hr-list-2.png)


1. 選取建議清單中的項目以將它新增為標準化的值，或選取 [全部新增] 以新增所有項目。 
    您可以使用下列 JSON 格式，將值匯入到現有的清單實體：

    ```
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


## <a name="change-entity-type"></a>變更實體類型

LUIS 不允許您變更實體的類型，因為它不知道要新增或移除哪些項目來建構該實體。 若要變更類型，更好的方式是使用稍微不同的名稱來建立正確類型的新實體。 一旦建立實體之後，在每個語句中，移除已加上標籤的舊實體名稱，並新增新的實體名稱。 為所有語句加上標籤之後，刪除舊實體。 

## <a name="create-a-pattern-from-an-utterance"></a>從語句建立模式

請參閱[在意圖或實體頁面上從現有的語句新增模式](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)。

## <a name="train-your-app-after-changing-model-with-entities"></a>利用實體變更模型後將您的應用程式定型

在新增、編輯或移除實體之後，請將您的應用程式[定型](luis-how-to-train.md)並[發佈](luis-how-to-publish-app.md)，變更才會影響端點查詢。 

## <a name="next-steps"></a>後續步驟

如需預先建置的實體詳細資訊，請參閱 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 專案。 

如需實體如何顯示在 JSON 端點查詢回應中的相關資訊，請參閱[資料擷取](luis-concept-data-extraction.md)

既然您已新增意圖、語句和實體，您就會擁有基本的 LUIS 應用程式。 了解如何為您的應用程式[定型](luis-how-to-train.md)、[測試](luis-interactive-test.md)及[發佈](luis-how-to-publish-app.md)。
 
