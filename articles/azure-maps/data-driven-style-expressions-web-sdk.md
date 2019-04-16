---
title: 資料驅動樣式運算式中 Azure 地圖服務 Web SDK |Microsoft Docs
description: 如何在 Azure 地圖服務 Web SDK 中使用資料驅動樣式的運算式。
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3b234ca37783fe557baf307f198de9636b06a382
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579490"
---
# <a name="data-driven-style-expressions-web-sdk"></a>資料驅動樣式運算式 (Web SDK)

運算式可讓您將商務邏輯套用樣式的選項，觀察每個圖形中的資料來源中定義的屬性。 運算式也可用來篩選資料來源或圖層中的資料。 運算式可以包含條件式邏輯，例如 if 陳述式，和也可用來操作資料字串，邏輯運算子和數學運算子。 

資料驅動樣式可以減少實作樣式周圍的商務邏輯所需的程式碼數量。 圖層搭配使用時，會在轉譯階段提供更高的效能，相較於評估商務邏輯在 UI 執行緒上的個別執行緒上評估運算式。

下列影片提供 Azure 地圖服務 Web SDK 中的資料驅動樣式的概觀。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

運算式會以 JSON 陣列表示。 陣列中的第一個元素是運算式的指定名稱的運算式運算子的字串。 例如，"+"或"case"。 下一步 的項目 （如果有的話） 是運算式的引數。 每個引數是常值 (字串、 數字、 布林值，或`null`)，或另一個運算式的陣列。 下列虛擬程式碼會定義基本結構的運算式。 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure 地圖服務 Web SDK 支援許多類型的可用靠自己或其他運算式結合的運算式。

| 運算式的類型 | 描述 |
|---------------------|-------------|
| [布林值運算式](#boolean-expressions) | 布林運算式會提供一組布林運算子運算式的評估布林值比較。 |
| [色彩運算式](#color-expressions) | 色彩運算式，使您更輕鬆地建立及操作色彩值。 |
| [條件運算式](#conditional-expressions) | 條件運算式會提供類似 if 陳述式的邏輯作業。 |
| [資料運算式](#data-expressions) | 提供的功能中的屬性資料的存取權。 |
| [進行插補和步驟運算式](#interpolate-and-step-expressions) | 進行插補，步驟運算式可以用來計算總插補曲線或步驟函式值。 |
| [圖層的特定運算式](#layer-specific-expressions) | 特殊的運算式都只適用於單一層。 |
| [數學運算式](#math-expressions) | 提供來執行資料驅動的計算運算式架構中的數學運算子。 |
| [字串運算子運算式](#string-operator-expressions) | 字串運算子的運算式執行字串串連等轉換大小寫轉換作業。 |
| [類型運算式](#type-expressions) | 類型運算式提供工具來測試及轉換不同的資料類型，例如字串、 數字和布林值。 |
| [變數繫結運算式](#variable-binding-expressions) | 變數繫結運算式可讓儲存在變數中的計算結果，以及其他地方在運算式中參考多次而不需要重新計算儲存的值。 |
| [縮放運算式](#zoom-expression) | 在轉譯階段中擷取對應的目前縮放層級。 |

本文件中的所有範例會都使用下列功能來示範不同的方式，可以使用不同類型的運算式。 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {     
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>資料運算式

資料運算式提供功能中的屬性資料的存取權。 

| 運算是 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['at', number, array]` | 物件 | 從陣列擷取項目。 |
| `['geometry-type']` | 字串 | 取得功能的幾何類型：點、 MultiPoint、 LineString、 MultiLineString、 Polygon、 MultiPolygon。 |
| `['get', string]` | value | 取得從目前的功能屬性的屬性值。 如果遺漏要求的屬性，則，傳回 null。 |
| `['get', string, object]` | value | 取得屬性值從提供的物件的屬性。 如果遺漏要求的屬性，則，傳回 null。 |
| `['has', string]` | 布林值 | 判斷某項功能的屬性是否具有指定的屬性。 |
| `['has', string, object]` | 布林值 | 判斷物件的屬性是否具有指定的屬性。 |
| `['id']` | value | 如果有的話，請取得功能的識別碼。 |
| `['length', string | array]` | number | 取得字串或陣列的長度。 |

**範例**

可以在運算式中直接存取某項功能的屬性，使用`get`運算式。 下列範例會使用的功能 」 zoneColor"值，指定泡泡圖層的色彩屬性。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

上述範例中將會正常運作，如果所有點的功能都有`zoneColor`屬性，但如果沒有，色彩會可能改為使用"black"。 若要修改的後援的色彩`case`可以使用運算式，結合`has`運算式以檢查如果屬性存在，而且如果它不會改為傳回後援的色彩。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

泡泡圖和符號的圖層會預設呈現的資料來源中的所有形狀的座標。 做法是以反白顯示的多邊形或線的頂點。 `filter`層的選項可用來限制 geometry 類型，它會呈現所使用的功能`['geometry-type']`布林運算式中的運算式。 下列範例會限制泡泡圖層，使得只有`Point`轉譯功能。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

下列範例會同時允許`Point`和`MultiPoint`要呈現的功能。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

同樣地，多邊形的外框會轉譯線條圖層中。 若要停用此行為在線條圖層中的，新增 只允許的篩選條件`LineString`和`MultiLineString`功能。  

## <a name="math-expressions"></a>數學運算式

數學運算式提供來執行資料驅動的計算運算式架構中的數學運算子。

| 運算是 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | 計算指定的數字的總和。 |
| `['-', number]` | number | 減去指定的數字 0。 |
| `['-', number, number]` | number | 減去第二個數字的第一個數字。 |
| `['*', number, number, …]` | number | 將指定的個數字相乘在一起。 |
| `['/', number, number]` | number | 將第二個數字的第一個數字。 |
| `['%', number, number]` | number | 除以第二個數字的第一個數字時，請計算餘數。 |
| `['^', number, number]` | number | 計算乘冪的第二個數字的第一個值的值。 |
| `['abs', number]` | number | 計算指定之數字的絕對值。 |
| `['acos', number]` | number | 計算指定之數字的反餘弦值。 |
| `['asin', number]` | number | 計算指定的數字的反正弦值。 |
| `['atan', number]` | number | 計算指定的數字的反正切值。 |
| `['ceil', number]` | number | 將數值四捨五入到下一步 的整數。 |
| `['cos', number]` | number | 計算指定的數字的 cos 值。 |
| `['e']` | number | 傳回數學常數`e`。 |
| `['floor', number]` | number | 將數值四捨五入到先前的整數。 |
| `['ln', number]` | number | 計算指定之數字的自然對數。 |
| `['ln2']` | number | 傳回數學常數`ln(2)`。 |
| `['log10', number]` | number | 計算指定之數字的基底 10 對數。 |
| `['log2', number]` | number | 計算指定之數字的兩個的基底對數。 |
| `['max', number, number, …]` | number | 計算指定的數字集合中最大數目。 |
| `['min', number, number, …]` | number | 計算最小數目的數字指定集合中。 |
| `['pi']` | number | 傳回數學常數`PI`。 |
| `['round', number]` | number | 將數值四捨五入到最接近的整數。 中間值遠離零四捨五入。 比方說，`['round', -1.5]`評估為-2。 |
| `['sin', number]` | number | 計算指定之數字的正弦函數。 |
| `['sqrt', number]` | number | 計算指定之數字的平方根。 |
| `['tan', number]` | number | 計算指定之數字的正切。 |
## <a name="boolean-expressions"></a>布林運算式

布林運算式會提供一組布林運算子運算式的評估布林值比較。

在比較的值，比較嚴格的型別。 不同類型的值一律會視為不相等。 要知道類型是在剖析階段有不同的情況下會被視為無效，而且將會產生剖析錯誤。 

| 運算是 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['! ', boolean]` | 布林值 | 邏輯否定。 傳回`true`輸入是否`false`，並`false`如果輸入是`true`。 |
| `['!= ', value, value]` | 布林值 | 傳回`true`輸入的值是否不相等，`false`否則。 |
| `['<', value, value]` | 布林值 | 傳回`true`第一個輸入是絕對小於第二個，如果`false`否則。 引數都必須是同時為字串或兩個數字。 |
| `['<=', value, value]` | 布林值 | 傳回`true`第一個輸入是否小於或等於第二個，`false`否則。 引數都必須是同時為字串或兩個數字。 |
| `['==', value, value]` | 布林值 | 傳回`true`輸入的值是否相等，`false`否則。 引數都必須是同時為字串或兩個數字。 |
| `['>', value, value]` | 布林值 | 傳回`true`必大於第二個，第一個輸入是否`false`否則。 引數都必須是同時為字串或兩個數字。 |
| `['>=' value, value]` | 布林值 | 傳回`true`大於或等於第二個，第一個輸入是否`false`否則。 引數都必須是同時為字串或兩個數字。 |
| `['all', boolean, boolean, …]` | 布林值 | 傳回`true`如果所有輸入都都`true`，`false`否則。 |
| `['any', boolean, boolean, …]` | 布林值 | 傳回`true`任何輸入是否`true`，`false`否則。 |

## <a name="conditional-expressions"></a>條件運算式

條件運算式會提供類似 if 陳述式的邏輯作業。

下列運算式執行條件式邏輯作業的輸入資料。 例如，`case`運算式來提供時的"if/then/else"邏輯`match`運算式就像是"switch 陳述式 」。 

### <a name="case-expression"></a>Case 運算式

A`case`運算式是一種提供 if-陳述式，例如 (if/then/else) 邏輯的條件式運算式。 這種類型的運算式會逐步引導的布林值條件清單，並傳回第一個布林條件為 true 的輸出值。

下列虛擬程式碼定義的結構`case`運算式。 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**範例**

下列範例會逐步透過不同的布林值條件，直到它找到一個評估為`true`，並再傳回相關聯的值。 如果沒有，則為 true 的條件評估為`true`，將會傳回後援的值。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>比對運算式

A`match`運算式是提供例如邏輯，switch 陳述式的條件運算式的型別。 輸入可以是任何運算式例如`['get', 'entityType']`會傳回字串或數字。 每個標籤必須是單一的常值或陣列常值，其值必須是所有的字串或所有的數字。 如果任何一個陣列的相符項目中的值，則比對輸入。 每個標籤必須是唯一的。 如果輸入型別不符的標籤類型，結果會是後援的值。

下列虛擬程式碼定義的結構`match`運算式。 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**範例**

下列範例會查看`entityType`的泡泡圖層的點功能的屬性搜尋相符項目。 如果找到相符項目，指定會傳回值，或它會傳回後援的值。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

下列範例會使用陣列，列出一組的所有應會傳回相同值的標籤。 這會比個別列出每個標籤，更有效率。 在此情況下，如果`entityType`屬性 「 餐廳 」 或 「 grocery_store"，"red"的色彩將會傳回。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>聯合運算式

A`coalesce`運算式步驟透過一組運算式，直到取得第一個非 null 值，並傳回該值。 

下列虛擬程式碼定義的結構`coalesce`運算式。 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**範例**

下列範例會使用`coalesce`運算式來設定`textField`符號圖層的選項。 如果`title`遺漏屬性的功能或設為`null`，運算式接著將會嘗試尋找`subtitle`屬性，如果其遺漏或`null`，它將會改為空字串。 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

## <a name="type-expressions"></a>類型運算式

類型運算式提供工具來測試及轉換不同的資料類型，例如字串、 數字和布林值。

| 運算是 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | 陣列\|物件 | 傳回常值的陣列或物件值。 使用此運算式，以防止被評估為運算式的陣列或物件。 傳回運算式所需要的陣列或物件時，這是必要的。 |
| `['to-boolean', value]` | 布林值 | 輸入的值轉換為布林值。 結果是`false`輸入是空字串，當`0`， `false`， `null`，或`NaN`; 否則其`true`。 |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | 輸入的值轉換為的色彩。 如果提供多個值，則取得第一次成功的轉換之前評估每個順序。 如果可以轉換沒有任何輸入，則運算式會是錯誤。 |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | 轉換輸入的值的數字，如果可能的話。 如果輸入是`null`或`false`，則結果為 0。 如果輸入是`true`，則結果為 1。 如果輸入是字串，則會轉換數字 using [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) ECMAScript 語言規格中的函式的字串。 如果提供多個值，則取得第一次成功的轉換之前評估每個順序。 如果可以轉換沒有任何輸入，則運算式會是錯誤。 |
| `['to-string', value]` | 字串 | 將輸入的值轉換為字串。 如果輸入是`null`，結果是`""`。 如果輸入是布林值，結果會是`"true"`或`"false"`。 如果輸入是數字，它會轉換成字串，使用[ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type)編號 ECMAScript 語言規格中的函式。 如果輸入是一種色彩，則會轉換為 CSS RGBA 色彩字串`"rgba(r,g,b,a)"`。 否則，將輸入轉換成字串，使用[JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) ECMAScript 語言規格中的函式。 |
| `['typeof', value]` | 字串 | 傳回字串，描述指定值的型別。 |

> [!TIP]
> 如果錯誤訊息類似於`Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].`出現在瀏覽器主控台的它表示是在程式碼具有不需要其第一個值的字串陣列中的某個位置的運算式。 如果您想要傳回陣列的運算式時，自動換行的陣列`literal`運算式。 下列範例會設定圖示`offset`必須是陣列，包含兩個數字，使用一個符號圖層的選項`match`選擇兩個的位移值的運算式會根據值`entityType`點屬性功能。
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If there is no title, try getting the subtitle. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>色彩運算式

色彩運算式，使您更輕鬆地建立及操作色彩值。

| 運算是 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | 建立從色彩值*紅色*，*綠色*，並*藍色*必須介於範圍內的元件`0`和`255`，和的 alpha 元件為`1`. 如果任何元件超出範圍時，運算式是錯誤。 |
| `['rgba', number, number, number, number]` | color | 建立從色彩值*紅色*，*綠色*，*藍色*必須介於範圍內的元件`0`和`255`，並在範圍內的 alpha 元件`0`和`1`。 如果任何元件超出範圍時，運算式是錯誤。 |
| `['to-rgba']` | \[數字、 數目、 數字、 數字\] | 傳回四個元素陣列，包含將輸入的色彩*紅色*，*綠色*，*藍色*，以及*alpha*元件，依此順序。 |

**範例**

下列範例會建立並 RGB 色彩值含有*紅色*的值`255`，和*綠色*並*藍色*乘以計算的值`2.5`的值來`temperature`屬性。 隨著溫度變更色彩會變更為不同的灰色陰影*紅色*。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>字串運算子運算式

字串運算子的運算式執行字串串連等轉換大小寫轉換作業。 

| 運算是 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['concat', string, string, …]` | 字串 | 多個字串串連。 每個值必須是字串。 使用`to-string`輸入如有需要將其他實值型別轉換成字串的運算式。 |
| `['downcase', string]` | 字串 | 將指定的字串轉換成小寫。 |
| `['upcase', string]` | 字串 | 將指定的字串轉換成大寫。 |

**範例**

下列範例會將轉換`temperature`點屬性功能轉換為字串，然後將串連至結尾的"° F"。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

上述運算式會轉譯文字"64 ° F"在其下面的影像所示上重疊在地圖上的 pin。

<center>

![字串運算子的運算式範例](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>進行插補和步驟運算式

進行插補，步驟運算式可以用來計算總插補曲線或步驟函式值。 比方說，做為其輸入，會傳回數值的運算式中的這些運算式需要`['get',  'temperature']`。 輸入的值會評估對一組輸入和輸出值，稱為 「 停止 」，以判斷最適合的插補的曲線或步驟函式的值。 每個停止的輸入的值必須是數字，且可依遞增順序。 輸出值必須是數字和數字、 陣列或色彩。

### <a name="interpolate-expression"></a>進行插補運算式

`interpolate`運算式可用來計算透過在停止值之間的一組連續且順暢的值。 `interpolate`傳回色彩值的運算式會產生結果值會從選取的色彩漸層。

有三種類型的插補方法，可用於`interpolate`運算式：
 
* `['linear']` -間進行插補而呈線性成長的停駐點配對。
* `['exponential', base]` -間進行插補以指數方式停駐點。 `base`值控制項的輸出會增加的速率。 較高的值，使輸出增加更高範圍的結束。 A`base`接近 1 的值會產生的輸出，會更呈線性增加。
* `['cubic-bezier', x1, y1, x2, y2]` -插入使用[三次方貝茲曲線](https://developer.mozilla.org/docs/Web/CSS/timing-function)定義所指定的控制點。

以下是這些不同類型的內插補點的外觀的範例。 

| 線性  | 指數 | 三次方貝茲 |
|---------|-------------|--------------|
| ![線性插補圖形](media/how-to-expressions/linear-interpolation.png) | ![內插補的指數點圖形](media/how-to-expressions/exponential-interpolation.png) | ![三次方貝茲內插補點圖形](media/how-to-expressions/bezier-curve-interpolation.png) |

下列虛擬程式碼定義的結構`interpolate`運算式。 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**範例**

下列範例會使用`linear interpolate`運算式來設定`color`泡泡圖層的屬性，根據`temperature`點功能的屬性。 如果`temperature`值小於 60，"blue"會傳回，如果介於 60 和低於 70，黃色會傳回，如果與 70 低於 80，「 橙色 」 便會傳回，如果將傳回 80 或更大，"red"。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

下圖示範如何選擇色彩，如上面的運算式。
 
<center>

![進行插補運算式範例](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>步驟運算式

A`step`運算式可用來計算離散、 分層式的結果值，藉由評估[分段常數函數](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)停駐點所定義。 

下列虛擬程式碼定義的結構`step`運算式。 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

步驟運算式會傳回輸出的值小於第一站的輸入的值或如果輸入是第一個輸入的值之前停止。 

**範例**

下列範例會使用`step`運算式來設定`color`泡泡圖層的屬性，根據`temperature`點功能的屬性。 如果`temperature`值小於 60，"blue"會傳回，如果介於 60 和低於 70，「 黃色 」 便會傳回，如果與 70 低於 80，「 橙色 」 便會傳回，如果將傳回 80 或更大，"red"。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

下圖示範如何選擇色彩，如上面的運算式。
 
<center>

![步驟運算式範例](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>圖層的特定運算式

只會套用至特定層級的特殊運算式。

### <a name="heat-map-density-expression"></a>熱度圖密度運算式

熱度圖密度運算式擷取熱度圖地圖圖層內的每個像素的熱度圖對應密度值，並定義為`['heatmap-density']`。 這個值是介於`0`並`1`並用結合`interpolation`或`step`運算式以定義用來標示熱量分佈圖的色彩漸層。 此運算式僅適用於在[色彩選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color)的熱度圖地圖圖層。

> [!TIP]
> 位於索引 0 內插補點運算式中的色彩或預設色彩的色彩步驟，會定義區域的色彩沒有任何資料而且可用來定義背景色彩。 許多人偏好將此值設定為透明或半透明的黑色。 

**範例**

此範例會使用線性插補運算式建立平滑的色彩漸層來呈現的熱度圖。 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

除了使用平滑漸層熱度圖以色彩標示，色彩可以指定一組範圍內使用`step`運算式。 使用`step`來標示熱量分佈圖的色彩的運算式會以視覺化方式分成多個範圍，類似於分佈或雷達圖樣式對應的密度。  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

如需詳細資訊，請參閱 <<c0> [ 加入熱度圖地圖圖層](map-add-heat-map-layer.md)文件。

### <a name="line-progress-expression"></a>線條進度運算式

線條進度運算式擷取沿著漸層線的線條圖層中的進度，並定義為`['line-progress']`。 這個值是 0 和 1 之間的數字，而且會在結合`interpolation`或`step`運算式。 此運算式僅能搭配[strokeGradient 選項]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient)的線條圖層。 

> [!NOTE]
> `strokeGradient`線條圖層的選項需要`lineMetrics`設為資料來源的選項`true`。

**範例**

下列範例會使用`['line-progress']`来套用的筆觸線條的色彩漸層的運算式。

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[即時範例，請參閱](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>文字欄位格式運算式

在文字欄位格式運算式可以搭配`textField`符號圖層選項`textOptions`屬性以提供混合的文字格式設定。 此運算式可讓一組輸入的字串和指定的格式化選項。 可以針對每個輸入的字串，此運算式中指定下列選項。

 * `'font-scale'` -指定字型大小的縮放比例。 如果指定，這個值會覆寫`size`屬性`textOptions`個別字串。
 * `'text-font'` -指定應該用於這個字串的一或多個字型系列。 如果指定，這個值會覆寫`font`屬性`textOptions`個別字串。

下列虛擬程式碼會定義文字欄位格式運算式的結構。 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    …
]
```

**範例**

下列範例會格式化的文字欄位，藉由加入以粗體字相應增加的字型大小`title`功能的屬性。 這個範例也會新增`subtitle`屬性上新行字元、 使用縮放功能的向下字型大小。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 'font-scale': 0.75 }
        ]
    }
});
```

此圖層會轉譯 「 點 」 功能，如下圖所示：
 
<center>

![包含已格式化的文字欄位的點功能的映像](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>數字格式運算式

`number-format`運算式僅能搭配`textField`符號圖層的選項。 這個運算式會將提供的數字轉換成格式化的字串。 這個運算式會包裝 JavaScript 的[Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)函式，並支援下列一組選項。

 * `locale` -指定此選項可將數字轉換為字串與指定的語言一致的方式。 傳遞[BCP 47 語言標記](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation)到這個選項。
 * `currency` -若要將數值轉換成字串，代表貨幣。 可能的值為[ISO 4217 貨幣代碼](https://en.wikipedia.org/wiki/ISO_4217)，例如"美元"美元、"歐元 「 歐洲或為中國人民幣的 「 CNY"。
 * `'min-fraction-digits'` -指定要在數字的字串版本中小數位的數下限。
 * `'max-fraction-digits'` -指定要包含在數字的字串版本中的小數位數的數目上限。

下列虛擬程式碼會定義文字欄位格式運算式的結構。 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**範例**

下列範例會使用`number-format`若要修改的運算式如何`revenue`點功能的屬性呈現在`textField`符號的選項層，它會顯示美國貨幣值。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

此圖層會轉譯 「 點 」 功能，如下圖所示：

<center>

![數字格式運算式範例](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>縮放運算式

A`zoom`運算式用來在轉譯階段中擷取對應的目前縮放層級，並定義為`['zoom']`。 這個運算式會傳回數字之間的對應最小和最大縮放層級的範圍。 使用此運算式可讓樣式來變更地圖的縮放層級，動態修改。 `zoom`運算式只可搭配`interpolate`和`step`運算式。

**範例**

根據預設，在熱度圖地圖圖層中呈現的資料點的半徑會有固定的像素半徑，所有的縮放層級。 如已放大地圖放在一起的資料彙總與熱度圖地圖圖層看起來不同。 A`zoom`運算式可用來調整每個縮放層級的半徑，使得每個資料點涵蓋相同的實體區域的地圖。 靜態更一致，這會讓尋找熱度圖地圖圖層。 對應的每個縮放層級有兩倍的像素垂直和水平與先前的縮放層級。 調整 radius，使它與每個縮放層級會加倍，會建立熱度圖，在所有的縮放層級上一致的外觀。 這可藉由使用`zoom`運算式使用`base 2 exponential interpolation`運算式，如下所示。 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[即時範例，請參閱](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>變數繫結運算式

變數繫結運算式會將計算的結果儲存在變數中，以便它可以其他地方在運算式中參考多次而不需要重新計算它。 這是有用的最佳化，牽涉到多個計算的運算式

| 運算是 | 傳回類型 | 描述 |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' let'<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1： 字串，<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1： 的話<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2： 字串，<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2： 的話<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | 將一個或多個值儲存為變數，以供`var`所傳回結果的子運算式中的運算式。 |
| `['var', name: string]` | any | 參考此變數會使用建立`let`運算式。 |

**範例**

此範例使用的運算式會計算相對於溫度比率，然後再使用的平均營收`case`來評估不同的布林值作業，這個值的運算式。 `let`運算式用來儲存相對於溫度比例的營收，讓它只需要計算一次和`var`運算式通常視需要而不需要重新計算它參考此變數。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>後續步驟

請參閱以下文章中的其他實作運算式的程式碼範例：

> [!div class="nextstepaction"] 
> [新增符號圖層](map-add-pin.md)

> [!div class="nextstepaction"] 
> [新增泡泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [新增圖形](map-add-shape.md)

> [!div class="nextstepaction"] 
> [加入熱度圖地圖圖層](map-add-heat-map-layer.md)

深入了解支援運算式的圖層選項：

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
