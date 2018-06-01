---
title: 工作流程定義語言結構描述 - Azure Logic Apps | Microsoft Docs
description: 使用工作流程定義語言撰寫 Azure Logic Apps 的自訂工作流程定義
services: logic-apps
author: ecfan
manager: cfowler
editor: ''
documentationcenter: ''
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: efbfffec10b665ebab230375e774e476199c4ad5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886798"
---
# <a name="logic-apps-workflow-definitions-with-the-workflow-definition-language-schema"></a>使用工作流程定義語言撰寫 Logic Apps 工作流程定義

當您使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 建立邏輯應用程式工作流程時，您工作流程的基礎定義會描述針對您的邏輯應用程式執行的實際邏輯。 此描述會遵循工作流程定義語言結構描述所定義和驗證的結構，其使用 [JavaScript 物件標記法 (JSON)](https://www.json.org/) 格式。 
  
## <a name="workflow-definition-structure"></a>工作流程定義結構

工作流程定義至少有一個觸發程序可具現化您的應用程式邏輯，再加上您的邏輯應用程式所執行的一或多個動作。 

以下是工作流程定義的高階結構︰  
  
```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```
  
| 元素 | 必要 | 說明 | 
|---------|----------|-------------| 
| 定義 | yes | 工作流程定義的起始元素 | 
| $schema | 只有在外部參考工作流程定義時 | JSON 結構描述檔案的位置，該檔案說明工作流程定義語言版本，您可以在此找到此版本： <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | 否 | 您的工作流程定義版本號碼，預設為 "1.0.0.0"。 若要在部署工作流程時協助識別及確認正確的定義，請指定要使用的值。 | 
| parameters | 否 | 一或多個參數的定義，此參數可將資料傳入您的工作流程中 <p><p>參數上限：50 | 
| 觸發程序 | 否 | 一或多個觸發程序的定義，此觸發程序可具現化您的工作流程。 您可以定義多個觸發程序，但只能利用工作流程定義語言，而不會透過 Logic Apps 設計工具呈現。 <p><p>觸發程序上限：10 | 
| 動作 | 否 | 要在工作流程執行階段執行之一或多個動作的定義 <p><p>動作上限：250 | 
| 輸出 | 否 | 從工作流程執行傳回之輸出的定義 <p><p>輸出上限：10 |  
|||| 

## <a name="parameters"></a>參數

在 `parameters` 區段中，定義邏輯應用程式在部署時用於接受輸入的所有工作流程參數。 在部署時需要有參數宣告和參數值。 請務必先在這些區段中宣告所有參數，您才可以在其他工作流程區段中使用這些參數。 

以下是參數定義的一般結構︰  

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": { 
      "key": { 
        "name": "<key-value>"
      } 
    }
  }
},
```

| 元素 | 必要 | 類型 | 說明 |  
|---------|----------|------|-------------|  
| type | yes | int、float、string、securestring、bool、array、JSON 物件、secureobject <p><p>**注意**：對於所有密碼、金鑰和祕密，使用 `securestring` 和 `secureobject` 類型，因為 `GET` 作業不會傳回這些類型。 | 參數的類型 |
| defaultValue | 否 | 與 `type` 相同 | 在工作流程具現化時，未指定任何值時的預設參數值 | 
| allowedValues | 否 | 與 `type` 相同 | 具有參數可接受值的陣列 |  
| 中繼資料 | 否 | JSON 物件 | 任何參數詳細資訊，例如：邏輯應用程式的名稱或可讀取描述，或是 Visual Studio 或其他工具所使用的設計階段資料 |  
||||

## <a name="triggers-and-actions"></a>觸發程序及動作  

在工作流程定義中，`triggers` 和 `actions` 區段會定義在工作流程執行期間發生的呼叫。 如需這些區段的語法和詳細資訊，請參閱[工作流程觸發程序和動作](../logic-apps/logic-apps-workflow-actions-triggers.md)。
  
## <a name="outputs"></a>輸出 

在 `outputs` 區段中，定義工作流程在完成執行時可傳回的資料。 例如，若要追蹤來自每次執行的特定狀態或值，請指定工作流程輸出會傳回該資料。 

> [!NOTE]
> 當回應來自服務 REST API 的傳入要求時，請勿使用 `outputs`。 請改用 `Response` 動作類型。 如需詳細資訊，請參閱[工作流程觸發程序和動作](../logic-apps/logic-apps-workflow-actions-triggers.md)。

以下是輸出定義的一般結構： 

```json
"outputs": {
  "<key-name>": {  
    "type": "<key-type>",  
    "value": "<key-value>"  
  }
} 
```

| 元素 | 必要 | 類型 | 說明 | 
|---------|----------|------|-------------| 
| <*key-name*> | yes | 字串 | 輸出傳回值的索引鍵名稱 |  
| type | yes | int、float、string、securestring、bool、array、JSON 物件 | 輸出傳回值的類型 | 
| value | yes | 與 `type` 相同 | 輸出傳回值 |  
||||| 

若要取得工作流程執行的輸出，請在 Azure 入口網站中或使用 [Workflow REST API](https://docs.microsoft.com/rest/api/logic/workflows) 檢閱邏輯應用程式的執行歷程記錄和詳細資料。 您也可以將輸出傳遞至外部系統 (例如 PowerBI)，以便建立儀表板。 

<a name="expressions"></a>

## <a name="expressions"></a>運算式

使用 JSON，您可以擁有在設計階段存在的常值，例如：

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

您也可以擁有在執行階段才存在的值。 若要表示這些值，您可以使用會在執行階段評估的「運算式」。 運算式是一個序列，可包含一或多個[函式](#functions)[運算子](#operators)、變數、明確值或常數。 在工作流程定義中，您可以在 JSON 字串值中的任意處使用運算式，方法是在運算式前面加上 @ 符號。 在評估代表 JSON 值的運算式時，移除 @ 字元即可擷取運算式主體，而且一律會產生其他 JSON 值。 

例如，對於先前定義的 `customerName` 屬性，您可以在運算式中使用 [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 函式來取得此屬性值，並將該值指派給 `accountName` 屬性：

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

「字串插補」也可讓您在字串內使用多個運算式，其以 @ 字元和大括號 ({}) 圍住。 語法如下：

```json
@{ "<expression1>", "<expression2>" }
```

結果一律是字串，讓這項功能類似於 `concat()` 函式，例如： 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

如果您有開頭為 @ 字元的常值字串，請在 @ 字元前面加上另一個 @ 字元作為逸出字元：@@

下列範例顯示如何評估運算式：

| JSON 值 | 結果 |
|------------|--------| 
| "Sophia Owen" | 傳回這些字元：'Sophia Owen' |
| "array[1]" | 傳回這些字元：'array[1]' |
| "\@@\" | 以一個字元的字串形式傳回這些字元：'@' |   
| \" \@\" | 以兩個字元的字串形式傳回這些字元：' @' |
|||

在這些範例中，假設您定義 "myBirthMonth" 等於 "January" 以及 "myAge" 等於數字 42：  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

下列範例顯示如何評估下列運算式：

| JSON 運算式 | 結果 |
|-----------------|--------| 
| "@parameters('myBirthMonth')" | 傳回這個字串："January" |  
| "@{parameters('myBirthMonth')}" | 傳回這個字串："January" |  
| "@parameters('myAge')" | 傳回這個數字：42 |  
| "@{parameters('myAge')}" | 以字串形式傳回這個數字："42" |  
| "My age is @{parameters('myAge')}" | 傳回這個字串："My age is 42" |  
| "@concat('My age is ', string(parameters('myAge')))" | 傳回這個字串："My age is 42" |  
| "My age is @@{parameters('myAge')}" | 傳回這個字串，其中包含運算式："My age is @{parameters('myAge')}` | 
||| 

當您在 Logic Apps 設計工具中以視覺化方式運作時，您可以透過運算式產生器建立運算式，例如： 

![Logic Apps 設計工具 > 運算式產生器](./media/logic-apps-workflow-definition-language/expression-builder.png)

當您完成時，運算式會對工作流程定義中對應的屬性顯示，例如，以下的 `searchQuery` 屬性：

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
       "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>運算子

在[運算式](#expressions)和[函式](#functions)中，運算子會執行特定工作，例如參考屬性或陣列中的值。 

| 運算子 | Task | 
|----------|------|
| ' | 若要將字串常值作為輸入或使用於運算式和函式中，只用單引號圍住此字串，例如 `'<myString>'`。 請勿使用雙引號 ("")，這會與整個運算式的 JSON 格式設定發生衝突。 例如︰ <p>**是**：length('Hello') </br>**否**：length("Hello") <p>當您傳遞陣列或數字時，您不需要包圍標點符號。 例如︰ <p>**是**：length([1, 2, 3]) </br>**否**：length("[1, 2, 3]") | 
| [] | 若要參考陣列中特定位置 (索引) 的值，請使用方括號。 例如，若要取得陣列中的第二個項目： <p>`myArray[1]` | 
| . | 若要參考物件中的屬性，請使用點運算子。 例如，若要取得 `customer` JSON 物件的 `name` 屬性： <p>`"@parameters('customer').name"` | 
| ? | 若要在不會發生執行階段錯誤的情況下，參考物件中的 null 屬性，請使用問號運算子。 例如，若要處理來自觸發程序的 null 輸出，您可以使用此運算式︰ <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>Functions

某些運算式會從邏輯應用程式開始執行時還不存在的執行階段動作中取得其值。 若要在運算式中參考或使用這些值，您可以使用[「函式」](../logic-apps/workflow-definition-language-functions-reference.md)。 例如，您可以使用數學函式進行計算，例如 [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) 函式，以傳回整數或浮點數總和。 如需有關每個函式的詳細資訊，請參閱[依字母順序排列的參考文章](../logic-apps/workflow-definition-language-functions-reference.md)。
或者，繼續了解函式及其一般用途。

以下是您可以使用函式執行的幾個範例工作： 

| Task | 函式語法 | 結果 | 
| ---- | --------------- | -------------- | 
| 傳回小寫格式的字串。 | toLower('<*text*>') <p>例如：toLower('Hello') | "hello" | 
| 傳回全域唯一識別碼 (GUID)。 | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

此範例示範如何藉由在運算式中使用 [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 函式，從 `customerName` 參數取得值並將該值指派給 `accountName` 屬性：

```json
"accountName": "@parameters('customerName')"
```

以下是您可以在運算式中使用函式的一些其他一般方式：

| Task | 運算式中的函式語法 | 
| ---- | -------------------------------- | 
| 將該項目傳遞至函式，以執行項目處理。 | "@<*functionName*>(<*item*>)" | 
| 1.使用巢狀 `parameters()` 函式來取得 parameterName 的值。 </br>2.將該值傳遞至 functionName，以執行結果處理。 | "@<*functionName*>(parameters('<*parameterName*>'))" | 
| 1.從巢狀內部函式 functionName 取得結果。 </br>2.將結果傳遞至外部函式 functionName2。 | "@<*functionName2*>(<*functionName*>(<*item*>))" | 
| 1.從 functionName 取得結果。 </br>2.假設結果是具有 propertyName 屬性的物件，則會取得該屬性的值。 | "@<*functionName*>(<*item*>).<*propertyName*>" | 
||| 

例如，`concat()` 函式可以採用兩個或多個字串值作為參數。 此函式會將這些字串合併成一個字串。 您可以傳入字串常值，例如，"Sophia" 和 "Owen"，以便取得合併字串 "SophiaOwen"：

```json
"customerName": "@concat('Sophia', 'Owen')"
```

或者，您可以從參數取得字串值。 這個範例在每個 `concat()` 參數以及 `firstName` 和 `lastName`參數中使用 `parameters()` 函式。 接著，您會將所產生的字串傳遞至 `concat()` 函式，以便取得合併字串，例如 "SophiaOwen"：

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

無論如何，這兩個範例都會將結果指派給 `customerName` 屬性。 

如需有關每個函式的詳細資訊，請參閱[依字母順序排列的參考文章](../logic-apps/workflow-definition-language-functions-reference.md)。
或者，繼續依照其一般用途來了解函式。

<a name="string-functions"></a>

### <a name="string-functions"></a>字串函數

若要處理字串，您可以使用這些字串函式以及一些[集合函式](#collection-functions)。 字串函式只能用於字串。 

| 字串函式 | Task | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | 結合兩個或多個字串，並傳回合併的字串。 | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | 檢查字串是否以指定的子字串結束。 | 
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | 以字串形式產生全域唯一識別碼 (GUID)。 | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | 傳回子字串的起始位置。 | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | 傳回子字串的結束位置。 | 
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | 使用指定字串取代子字串，並傳回已更新的字串。 | 
| [分割](../logic-apps/workflow-definition-language-functions-reference.md#split) | 傳回具有字串中所有字元的陣列，並以特定分隔符號字元將每個字元隔開。 | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | 檢查字串是否以特定的子字串開始。 | 
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | 傳回字串中的字元 (從指定的位置起始)。 | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | 傳回小寫格式的字串。 | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | 傳回大寫格式的字串。 | 
| [修剪](../logic-apps/workflow-definition-language-functions-reference.md#trim) | 移除字串的開頭和尾端空白字元，並傳回更新後的字串。 | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>集合函式

若要處理集合 (通常為陣列、字串，而有時候為字典)，您可以使用這些集合函式。 

| 集合函式 | Task | 
| ------------------- | ---- | 
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | 檢查集合是否具有特定項目。 |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | 檢查集合是否是空的。 | 
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | 傳回集合中的第一個項目。 | 
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | 在指定的多個集合中，傳回「只有」共同項目的集合。 | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | 傳回具有陣列中「所有」項目 (以指定的字元隔開) 的字串。 | 
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | 傳回集合中的最後一個項目。 | 
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | 傳回字串或陣列中的項目數目。 | 
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | 移除集合前端的項目，並傳回「其他所有」項目。 | 
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | 傳回集合中的前端項目。 | 
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | 傳回具有指定集合中「所有」項目的集合。 | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>比較函式

若要處理條件、比較值和運算式結果，或評估各種不同的邏輯，您可以使用下列比較函式。 如需有關每個函式的完整參考，請參閱[依字母順序排列的參考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

| 比較函式 | Task | 
| ------------------- | ---- | 
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | 檢查是否所有運算式都是 True。 | 
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | 檢查兩個值是否相等。 | 
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | 檢查第一個值是否大於第二個值。 | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | 檢查第一個值是否大於或等於第二個值。 | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | 檢查運算式是 True 或 False。 根據結果，傳回指定的值。 | 
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | 檢查第一個值是否小於第二個值。 | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | 檢查第一個值是否小於或等於第二個值。 | 
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | 檢查運算式是否為 False。 | 
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | 檢查是否至少有一個運算式是 True。 |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>轉換函式

若要變更值的類型或格式，您可以使用這些轉換函式。 例如，您可以將一個值從布林值變更為整數。 若要深入了解 Logic Apps 在轉換期間如何處理內容類型，請參閱[處理內容類型](../logic-apps/logic-apps-content-type.md)。 如需有關每個函式的完整參考，請參閱[依字母順序排列的參考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

| 轉換函式 | Task | 
| ------------------- | ---- | 
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | 從單一指定輸入傳回的陣列。 關於多個輸入的資訊，請參閱 [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray)。 | 
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | 傳回字串的 base64 編碼版本。 | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | 傳回 base64 編碼字串的二進位版本。 | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | 傳回 base64 編碼字串的字串版本。 | 
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | 傳回輸入值的二進位版本。 | 
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | 傳回輸入值的布林值版本。 | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | 從多個輸入傳回陣列。 | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | 傳回輸入值的資料 URI。 | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | 傳回資料 URI 的二進位版本。 | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | 傳回資料 URI 的字串版本。 | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | 傳回 base64 編碼字串的字串版本。 | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | 傳回資料 URI 的二進位版本。 | 
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | 傳回以已解碼版本取代逸出字元的字串。 | 
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | 傳回以逸出字元取代 URL 中 Unsafe 字元的字串。 | 
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | 傳回輸入值的浮點數。 | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | 傳回字串的整數版本。 | 
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | 傳回字串或 XML 的 JavaScript 物件標記法 (JSON) 類型值或物件。 | 
| [字串](../logic-apps/workflow-definition-language-functions-reference.md#string) | 傳回輸入值的字串版本。 | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | 藉由以逸出字元取代 URL 中的 Unsafe 字元，傳回輸入值的 URI 編碼版本。 | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | 傳回 URI 編碼字串的二進位版本。 | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | 傳回 URI 編碼字串的字串版本。 | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | 傳回字串的 SML 版本。 | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>數學函式

若要處理整數和浮點數，您可以使用這些數學函式。 如需有關每個函式的完整參考，請參閱[依字母順序排列的參考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

| 數學函式 | Task | 
| ------------- | ---- | 
| [新增](../logic-apps/workflow-definition-language-functions-reference.md#add) | 傳回兩個數字相加的結果。 | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | 傳回兩個數字相除的結果。 | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | 從數字集合或陣列中傳回最大值。 | 
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | 從數字集合或陣列中傳回最小值。 | 
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | 傳回兩數相除的餘數。 | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | 傳回將兩數相乘的乘積。 | 
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | 從指定範圍傳回隨機整數。 | 
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | 傳回從指定整數開始的整數陣列。 | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | 傳回第一個數字減去第二個數字的結果。 | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>日期和時間函式

若要處理日期和時間，您可以使用這些日期和時間函式。
如需有關每個函式的完整參考，請參閱[依字母順序排列的參考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

| 日期或時間函式 | Task | 
| --------------------- | ---- | 
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | 將天數加入時間戳記。 | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | 將時數加入時間戳記。 | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | 將分鐘數加入時間戳記。 | 
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | 將秒數加入時間戳記。 |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | 將時間單位數字加入時間戳記。 另請參閱 [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime)。 | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | 將時間戳記從國際標準時間 (UTC) 轉換為目標時區。 | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | 將時間戳記從來源時區轉換為目標時區。 | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | 將時間戳記從來源時區轉換為國際標準時間 (UTC)。 | 
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | 傳回時間戳記中的當月日期元件。 | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | 傳回時間戳記中的星期幾元件。 | 
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | 傳回時間戳記中一年的第幾天元件。 | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | 傳回時間戳記中的日期。 | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | 傳回目前時間戳記加上指定時間單位的結果。 另請參閱 [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime)。 | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | 傳回目前時間戳記減去指定時間單位的結果。 另請參閱 [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime)。 | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | 傳回時間戳記中當天的起始點。 | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | 傳回時間戳記中小時的起始點。 | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | 傳回時間戳記中月份的起始點。 | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | 從時間戳記減去時間單位數字。 另請參閱 [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime)。 | 
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | 傳回指定時間戳記的 `ticks` 屬性值。 | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | 傳回目前的時間戳記作為字串。 | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>工作流程函式

這些工作流程函式可協助您：

* 在執行階段取得有關工作流程執行個體的詳細資料。 
* 處理用於具現化邏輯應用程式的輸入。
* 參考來自觸發程序和動作的輸出。

例如，您可以參考來自一個動作的輸出，並在稍後動作中使用該資料。 如需有關每個函式的完整參考，請參閱[依字母順序排列的參考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

| 工作流程函式 | Task | 
| ----------------- | ---- | 
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | 傳回目前動作在執行階段的輸出，或來自其他 JSON 名稱與值配對中的值。 另請參閱 [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions)。 | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | 傳回動作在執行階段的 `body` 輸出。 另請參閱 [body](../logic-apps/workflow-definition-language-functions-reference.md#body)。 | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | 傳回動作在執行階段的輸出。 請參閱 [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions)。 | 
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | 傳回動作在執行階段的輸出，或來自其他 JSON 名稱與值配對中的值。 另請參閱 [action](../logic-apps/workflow-definition-language-functions-reference.md#action)。  | 
| [body](#body) | 傳回動作在執行階段的 `body` 輸出。 另請參閱 [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody)。 | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | 建立帶有值的陣列，這些值會符合「表單資料」或「表單編碼」動作輸出中的索引鍵名稱。 | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | 傳回單一值，此值會符合動作「表單資料」或「表單編碼」輸出的索引鍵名稱。 | 
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | 若用於透過陣列進行的重複動作中，則會在動作的目前反覆運算期間，傳回陣列中的目前項目。 | 
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | 若用於 for-each 或 do-until-loop 內，則會從指定的迴圈傳回目前的項目。| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | 傳回呼叫觸發程序或動作的「回呼 URL」。 | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | 在具有多個部分的動作輸出中，傳回特定部分的內容。 | 
| [參數](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | 傳回邏輯應用程式定義中描述的參數值。 | 
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | 傳回觸發程序在執行階段的輸出，或來自其他 JSON 名稱與值配對中的值。 另請參閱 [triggerOutputs](#triggerOutputs) 和 [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody)。 | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | 傳回觸發程序在執行階段的 `body` 輸出。 請參閱 [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger)。 | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | 在「表單資料」或「表單編碼」觸發程序輸出中，傳回符合索引鍵名稱的單一值。 | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | 在觸發程序的多部分輸出中，傳回特定部分的內容。 | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | 建立帶有值的陣列，這些值會符合「表單資料」或「表單編碼」觸發程序輸出中的索引鍵名稱。 | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | 傳回觸發程序在執行階段的輸出，或來自其他 JSON 名稱與值配對中的值。 請參閱 [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger)。 | 
| [變數](../logic-apps/workflow-definition-language-functions-reference.md#variables) | 傳回指定變數的值。 | 
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | 傳回執行階段期間與工作流程本身相關的所有詳細資料。 | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>URI 剖析函式

若要處理統一資源識別項 (URI) 並取得這些 URI 的各種屬性值，您可以使用這些 URI 剖析函式。 如需有關每個函式的完整參考，請參閱[依字母順序排列的參考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

| URI 剖析函式 | Task | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | 傳回統一資源識別項 (URI) 的 `host` 值。 | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | 傳回統一資源識別項 (URI) 的 `path` 值。 | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | 傳回統一資源識別項 (URI) 的 `path` 和 `query` 值。 | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | 傳回統一資源識別項 (URI) 的 `port` 值。 | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | 傳回統一資源識別項 (URI) 的 `query` 值。 | 
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | 傳回統一資源識別項 (URI) 的 `scheme` 值。 | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>JSON 和 XML 函式

若要處理 JSON 物件和 XML 節點，您可以使用這些操作函式。 如需有關每個函式的完整參考，請參閱[依字母順序排列的參考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

| 操作函式 | Task | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | 將屬性和其值或成對的名稱和數值新增至 JSON 物件，並傳回更新的物件。 | 
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | 從一個或多個參數中傳回第一個非 Null 值。 | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | 從 JSON 物件中移除屬性，並傳回更新的物件。 | 
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | 設定 JSON 物件屬性的值，並傳回更新的物件。 | 
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | 檢查 XML 中是否有符合 XPath (XML 路徑語言) 運算式的節點或值，並傳回符合的節點或值。 | 
||| 

## <a name="next-steps"></a>後續步驟

* 深入了解[工作流程定義語言動作和觸發程序](../logic-apps/logic-apps-workflow-actions-triggers.md)
* 深入了解如何透過 [Workflow REST API](https://docs.microsoft.com/rest/api/logic/workflows) 以程式設計方式建立及管理邏輯應用程式
