---
title: 條件式認知搜尋技能 （Azure 搜尋服務） |Microsoft Docs
description: 條件式技術可篩選、 建立預設值，以及將值合併。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 6a203a38437ccb6a9c325e6594289744e0148c84
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028421"
---
#   <a name="conditional-skill"></a>條件式的技能

**條件式技能**可讓各種需要布林作業，決定應該指派給輸出資料的案例。 這些案例包括： 篩選、 指派的預設值及合併資料根據條件。

下列虛擬程式碼說明條件式技能的完成：

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 這項技能不受限於認知服務 API，您不需支付其使用費用。 不過，您仍然應該[連結認知服務資源](cognitive-search-attach-cognitive-services.md)，以覆寫限制您每天少量擴充的**免費**資源選項。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>已評估的欄位

這項技術是特殊的因為其輸入會評估的欄位。

以下是運算式的有效的值:

-   註解路徑 (在運算式中的路徑必須以 「 $("and")") <br/>
    範例：
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  常值 （字串、 數字、 true、 false、 null） <br/>
    範例：
    ```
       "= 'this is a string'"   // string, note the single quotes
       "= 34"                   // number
       "= true"                 // boolean
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

支援的評估，因為條件式技能可以用於次要的轉換案例。 請參閱範例[技能定義 4](#transformation-examples)的範例。

## <a name="skill-inputs"></a>技能輸入
輸入會區分大小寫。

| 輸入      | 描述 |
|-------------|-------------|
| condition   | 此輸入是[評估欄位](#evaluated-fields)，表示要評估的條件。 這種情況應該評估為布林值 （true 或 false）。   <br/>  範例： <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | 此輸入是[評估欄位](#evaluated-fields)。 如果評估條件所傳回的值為 true。 應該傳回常數字串 '' 括住。 <br/>範例值： <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | 此輸入是[評估欄位](#evaluated-fields)。 要傳回如果條件評估為 false 的值。  <br/>範例值： <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>技能輸出
沒有單一輸出，稱為 「 輸出 」。 如果條件為 true，它會傳回 whenFalse 如果條件為 false 或 whenTrue 的值。

## <a name="examples"></a>範例

### <a name="sample-skill-definition-1-filtering-documents-to-return-only-french-documents"></a>範例遊戲定義 1:篩選傳回 「 法文 」 的文件的文件

下列的輸出會傳回陣列的句子 (「 文件/frenchSentences")，如果文件的語言是法文。 如果該語言不是法文的該值將為 null。

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
如果有 「 文件/frenchSentences 」 會作為*內容*的另一個技巧，該技術只會執行 「 文件/frenchSentences 「 未設定為 null


### <a name="sample-skill-definition-2-setting-a-default-value-when-it-does-not-exist"></a>技能定義 2 範例：它不存在時，請設定預設值。

下列的輸出將會建立註解 ("/ 文件/languageWithDefault")，如果未設定語言設定為任一語言的文件或"es"。

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

### <a name="sample-skill-definition-3-merging-values-from-two-different-fields-into-a-single-field"></a>技能定義 3 範例：將兩個不同的欄位中的值合併到單一欄位

在此範例中，有一些句子*frenchSentiment*屬性。 每當*frenchSentiment*屬性為 null 時，我們想要使用*englishSentiment*值。 我們將輸出指派給一個名為只要成員*情感*(「 文件/情感 / * / 情感 」)。

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

## <a name="transformation-examples"></a>轉換範例
### <a name="sample-skill-definition-4-performing-data-transformations-on-a-single-field"></a>技能定義 4 範例：執行的單一欄位上的資料轉換

在此範例中，我們收到 0 和 1 之間的情感，我們想要轉換它，使它是-1 和 1 之間。 這是小型的數學轉換，我們可以執行使用條件式的技能。

在此特定範例中，我們永遠不會使用條件式的技術層面，因為條件一律為 true。 

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
請注意，某些參數都會經過評估，因此您必須要特別小心遵循記載的模式。 運算式必須以等號 「 = 」 開頭，且必須用來分隔路徑"$("and") 」。 請務必將字串放在 '單引號'，因為這可協助區分字串和實際路徑和運算子，評估工具。 此外，請確定將運算子前後的空白字元 (例如 * 路徑中的意義會因為乘法運算子)。


## <a name="next-steps"></a>後續步驟

+ [預先定義的技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
