---
title: 條件式認知搜尋技能 （Azure 搜尋服務） |Microsoft Docs
description: 篩選、 建立預設值，以及將值合併，可讓條件式的技能。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 149b701d4a1700787656448e2bdd0d92d2a93844
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791508"
---
#   <a name="conditional-skill"></a>條件式的技能

*條件式技能*可以實現需要布林作業，判斷將指派給輸出資料的 Azure 搜尋服務案例。 這些案例包括篩選、 指派預設值，以及根據條件的資料合併。

下列虛擬程式碼示範條件式技能的完成：

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 這項技術不繫結至 Azure 認知服務 API，而且您不需支付使用它。 不過，您應該仍然[附加認知服務資源](cognitive-search-attach-cognitive-services.md)來覆寫 「 免費 」 資源選項，以限制您一小部分的每日的類。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>已評估的欄位

這項技術是特殊的因為其輸入會評估的欄位。

下列項目是運算式的有效的值:

-   註解路徑 (在運算式中的路徑必須以 「 $("and")")
 <br/>
    範例：
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  常值 （字串、 數字、 true、 false、 null） <br/>
    範例：
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  使用比較運算子的運算式 (= =、 ！ =、 > =、 >、 < =、 <) <br/>
    範例：
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   使用布林運算子的運算式 (& &、 | |、 ！、 ^) <br/>
    範例：
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   使用數值運算子的運算式 (+、-、 \*、 /、 %) <br/>
    範例： 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

由於條件式的技術支援評估，因此您可以使用它做小幅度的轉換案例中。 例如，請參閱[技能定義 4](#transformation-example)。

## <a name="skill-inputs"></a>技能輸入
輸入會區分大小寫。

| 輸入   | 描述 |
|-------------|-------------|
| condition (條件)   | 此輸入是[評估欄位](#evaluated-fields)，表示要評估的條件。 這種情況應該評估為布林值 (*真*或是*false*)。   <br/>  範例： <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | 此輸入是[評估欄位](#evaluated-fields)表示要傳回如果條件評估為值 *，則為 true*。 常數字串應傳回在單引號 （' 和'）。 <br/>範例值： <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | 此輸入是[評估欄位](#evaluated-fields)表示要傳回如果條件評估為值*false*。 <br/>範例值： <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>技能輸出
沒有單一的輸出，就稱為 「 輸出 」。 它會傳回值*whenFalse*如果條件為 false 或*whenTrue*如果條件為 true。

## <a name="examples"></a>範例

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>範例遊戲定義 1:篩選文件，以傳回法文的文件

如果文件的語言為法文，下列的輸出會傳回陣列的句子 (「 文件/frenchSentences")。 如果該語言不是法文，要將值設為*null*。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
如果有 「 文件/frenchSentences 」 會作為*內容*的另一個技巧，該技能才會執行 「 文件/frenchSentences 「 未設定為*null*。


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>技能定義 2 範例：設定的值不存在的預設值

下列的輸出建立註解 ("/ 文件/languageWithDefault")，如果您未設定語言的文件的語言，或"es"設定。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>技能定義 3 範例：從兩個欄位的值合併成一個

在此範例中，有一些句子*frenchSentiment*屬性。 每當*frenchSentiment*屬性為 null，我們想要使用*englishSentiment*值。 我們將輸出指派給成員，稱為*情感*(「 文件/情感 / * / 情感 」)。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>轉換範例
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>技能定義 4 範例：對單一欄位的資料轉換

在此範例中，我們會收到*情感*這是介於 0 和 1 之間。 我們想要轉換為-1 和 1 之間。 我們可以使用條件式的技能，以執行此次要的轉換。

在此範例中，我們不使用條件式的技術層面，因為條件一律為 *，則為 true*。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>特殊考量
某些參數被評估，因此您必須特別小心遵循記載的模式。 運算式的開頭必須是等號。 路徑必須以 「 $("and")"。 請務必將字串放在單引號中。 這樣可以協助您區別字串和實際路徑和運算子，評估工具。 此外，請確定將運算子前後的空白字元 (比方說，"*"路徑中表示 multiply 以外)。


## <a name="next-steps"></a>後續步驟

+ [預先定義的技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
