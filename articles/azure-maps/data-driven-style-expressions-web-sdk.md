---
title: Azure 地圖服務 Web SDK 中的資料驅動樣式表達式 |Microsoft Docs
description: 如何在 Azure 地圖服務 Web SDK 中使用資料驅動樣式表達式。
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 507af54b8b4c2e7c67538a1a25a040c7ee5fdfd5
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976309"
---
# <a name="data-driven-style-expressions-web-sdk"></a>資料驅動樣式表達式 (Web SDK)

運算式可讓您將商務邏輯套用至樣式選項, 以觀察資料來源中每個圖形中所定義的屬性。 運算式也可以用來篩選資料來源或圖層中的資料。 運算式可以包含條件式邏輯, 例如 if 語句, 而且也可以用來操作的資料。字串、邏輯和數學運算子。 

資料驅動樣式可以減少在設定樣式前後實行商務邏輯所需的程式碼數量。 與圖層搭配使用時, 會在轉譯時于轉譯時間評估運算式, 以便在相較于在 UI 執行緒上評估商務邏輯時提供更高的效能。

下列影片提供 Azure 地圖服務 Web SDK 中資料驅動樣式的總覽。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

運算式會以 JSON 陣列表示。 陣列中運算式的第一個元素是指定運算式運算子名稱的字串。 例如, "+" 或 "case"。 下一個元素 (如果有的話) 是運算式的引數。 每個引數都是常值 (字串、數位、布林值或`null`) 或另一個運算式陣列。 下列虛擬程式碼會定義運算式的基本結構。 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure 地圖服務的 Web SDK 支援許多類型, 可以單獨使用或與其他運算式搭配使用。

| 運算式的類型 | 描述 |
|---------------------|-------------|
| [匯總運算式](#aggregate-expression) | 定義計算的運算式, 它會在一組資料上處理, 而且可以與`clusterProperties`的選項`DataSource`搭配使用。 |
| [布林運算式](#boolean-expressions) | 布林運算式提供一組布林運算子運算式來評估布林值比較。 |
| [色彩運算式](#color-expressions) | 色彩運算式可讓您更輕鬆地建立和操作色彩值。 |
| [條件運算式](#conditional-expressions) | 條件運算式會提供類似 if 語句的邏輯作業。 |
| [日期運算式](#data-expressions) | 提供功能中屬性資料的存取權。 |
| [插補和步驟運算式](#interpolate-and-step-expressions) | 插補和步驟運算式可以用來透過插入曲線或步驟函式來計算值。 |
| [圖層特定運算式](#layer-specific-expressions) | 僅適用于單一層的特殊運算式。 |
| [數學運算式](#math-expressions) | 提供數學運算子來執行運算式架構內的資料驅動計算。 |
| [字串運算子運算式](#string-operator-expressions) | 字串運算子運算式會對字串執行轉換作業, 例如串連和轉換案例。 |
| [類型運算式](#type-expressions) | 型別運算式提供的工具可用來測試和轉換不同的資料類型, 例如字串、數位和布林值。 |
| [變數系結運算式](#variable-binding-expressions) | 變數系結運算式可讓計算的結果儲存在變數中, 並在運算式中的其他地方多次參考, 而不需要重新計算儲存的值。 |
| [Zoom 運算式](#zoom-expression) | 在轉譯時期, 抓取地圖的目前縮放層級。 |

本檔中的所有範例都會使用下列功能, 示範可以使用不同類型運算式的不同方式。 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>日期運算式

日期運算式可讓您存取功能中的屬性資料。 

| 運算式 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['at', number, array]` | object | 從陣列中抓取專案。 |
| `['geometry-type']` | string | 取得功能的幾何類型:Point、MultiPoint、LineString、MultiLineString、多邊形、MultiPolygon。 |
| `['get', string]` | value | 從目前功能的屬性取得屬性值。 如果要求的屬性遺失, 則傳回 null。 |
| `['get', string, object]` | value | 從提供之物件的屬性取得屬性值。 如果要求的屬性遺失, 則傳回 null。 |
| `['has', string]` | boolean | 判斷功能的屬性是否具有指定的屬性。 |
| `['has', string, object]` | boolean | 判斷物件的屬性是否具有指定的屬性。 |
| `['id']` | value | 取得功能的識別碼 (如果有的話)。 |
| `['length', string | array]` | 號 | 取得字串或陣列的長度。 |

**範例**

您可以使用`get`運算式, 直接在運算式中存取功能的屬性。 下列範例會使用功能的 "zoneColor" 值來指定反升圖層的 color 屬性。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

如果所有點特徵都具有`zoneColor`屬性, 則上述範例會正常執行, 但如果沒有, 則色彩可能會切換回「黑色」。 若要修改回退色彩, `case`運算式可以搭配`has`運算式使用, 以檢查屬性是否存在, 以及是否不傳回回退色彩。

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

反升和符號圖層預設會轉譯資料來源中所有圖形的座標。 這可以完成以反白顯示多邊形或線條的頂點。 圖`filter`層的選項可使用布林運算式內的`['geometry-type']`運算式, 來限制它所呈現之功能的幾何類型。 下列範例會限制反升圖層, 以便`Point`只呈現特徵。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

下列範例會允許`Point`轉譯和`MultiPoint`功能。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

同樣地, 多邊形的外框也會以線條圖層呈現。 若要線上條圖層中停用此行為, 請加入僅`LineString`允許`MultiLineString`和功能的篩選。  

## <a name="math-expressions"></a>數學運算式

數學運算式提供數學運算子, 以在 expression framework 中執行資料驅動計算。

| 運算式 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['+', number, number, …]` | 號 | 計算指定數位的總和。 |
| `['-', number]` | 號 | 以指定的數位減去0。 |
| `['-', number, number]` | 號 | 將第一個數位減去第二個數字。 |
| `['*', number, number, …]` | 號 | 將指定的數位相乘。 |
| `['/', number, number]` | 號 | 將第一個數位除以第二個數字。 |
| `['%', number, number]` | 號 | 計算將第一個數位除以第二個數字時的餘數。 |
| `['^', number, number]` | 號 | 計算第一個值的值, 該值為第二個數字的乘冪。 |
| `['abs', number]` | 號 | 計算指定數位的絕對值。 |
| `['acos', number]` | 號 | 計算指定數位的反余弦值。 |
| `['asin', number]` | 號 | 計算指定數位的反正弦值。 |
| `['atan', number]` | 號 | 計算指定數位的反正切值。 |
| `['ceil', number]` | 號 | 將數位四捨五入到下一個整數。 |
| `['cos', number]` | 號 | 計算指定數位的 cos。 |
| `['e']` | 號 | 傳回數學常數`e`。 |
| `['floor', number]` | 號 | 將數位向下舍入到前一個整數。 |
| `['ln', number]` | 號 | 計算指定數位的自然對數。 |
| `['ln2']` | 號 | 傳回數學常數`ln(2)`。 |
| `['log10', number]` | 號 | 計算指定數位的底數為10的對數。 |
| `['log2', number]` | 號 | 計算指定數位的底數為2的對數。 |
| `['max', number, number, …]` | 號 | 計算指定數位集合中的最大數目。 |
| `['min', number, number, …]` | 號 | 計算指定數位集合中的最小數目。 |
| `['pi']` | 號 | 傳回數學常數`PI`。 |
| `['round', number]` | 號 | 將數位四捨五入至最接近的整數。 中間值會從零進位出來。 例如, `['round', -1.5]`會評估為-2。 |
| `['sin', number]` | 號 | 計算指定數位的正弦值。 |
| `['sqrt', number]` | 號 | 計算指定數位的平方根。 |
| `['tan', number]` | 號 | 計算指定數位的正切函數。 |

## <a name="aggregate-expression"></a>匯總運算式

匯總運算式會定義在一組資料上處理的計算, 並可與`clusterProperties`的選項`DataSource`搭配使用。 這些運算式的輸出必須是數位或布林值。 

匯總運算式採用三個值;運算子值和初始值, 以及從資料中的每項功能抓取屬性以套用匯總運算的運算式。 此運算式具有下列格式:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- 操作接著, 會針對叢集中每個點所計算的`mapExpression`所有值套用至運算式函數。 支援的運算子; 
    - 若為數字`+`: `*`、 `max`、、`min`
    - 針對布林值`all`:、`any`
- initialValue:初始值, 其中的第一個計算值是針對進行匯總。
- mapExpression:針對資料集內的每個點套用的運算式。

**範例**

如果資料集內的所有功能都有`revenue`一個是數位的屬性, 則為。 您可以使用下列匯總運算式來計算從資料集建立之叢集中所有點的總收益:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>布林運算式

布林運算式提供一組布林運算子運算式來評估布林值比較。

比較值時, 會以嚴格的類型進行比較。 不同類型的值一律視為不相等。 在剖析階段已知類型不同的情況會被視為無效, 而且會產生剖析錯誤。 

| 運算式 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | 邏輯否定。 如果輸入為`false`, 則傳回, `false`如果輸入為`true`, 則傳回。 `true` |
| `['!= ', value, value]` | boolean | 如果輸入值不相等, 則傳回, `false`否則傳回。 `true` |
| `['<', value, value]` | boolean | 如果第一個輸入嚴格小於第二個輸入, 則`false`傳回, 否則傳回。 `true` 引數必須是字串或兩個數字。 |
| `['<=', value, value]` | boolean | 如果第一個輸入小於或等於第二個, 則傳回, `false`否則傳回。 `true` 引數必須是字串或兩個數字。 |
| `['==', value, value]` | boolean | 如果輸入值相等, 則傳回, `false`否則傳回。 `true` 引數必須是字串或兩個數字。 |
| `['>', value, value]` | boolean | 如果第一個輸入嚴格大於第二個輸入, 則`false`傳回, 否則傳回。 `true` 引數必須是字串或兩個數字。 |
| `['>=' value, value]` | boolean | 如果第一個輸入大於或等於第二個, 則傳回, `false`否則傳回。 `true` 引數必須是字串或兩個數字。 |
| `['all', boolean, boolean, …]` | boolean | 如果所有輸入都是`true`, 則`false`傳回, 否則傳回。 `true` |
| `['any', boolean, boolean, …]` | boolean | 如果任何輸入為`true`, 則傳回, `false`否則傳回。 `true` |

## <a name="conditional-expressions"></a>條件運算式

條件運算式會提供類似 if 語句的邏輯作業。

下列運算式會針對輸入資料執行條件式邏輯作業。 例如, `case`運算式會提供 "if/then/else" 邏輯, `match`而運算式就像「switch 語句」。 

### <a name="case-expression"></a>Case 運算式

`case`運算式是一種條件運算式, 提供 if 語句, 例如邏輯 (if/then/else)。 這種類型的運算式會逐步執行布林條件清單, 並傳回第一個 true 布林條件的輸出值。

下列虛擬代碼會定義`case`運算式的結構。 

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

下列範例會逐步執行不同的布林值條件, 直到找到評估為`true`的運算式, 然後傳回該關聯的值為止。 如果沒有布林條件評估為`true`, 則會傳回 fallback 值。 

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

### <a name="match-expression"></a>Match 運算式

`match`運算式是提供 switch 語句 (例如邏輯) 的條件運算式類型。 輸入可以是任何傳回字串或數位`['get', 'entityType']`的運算式, 例如。 每個標籤都必須是單一常值或常值陣列, 其值必須是所有字串或所有數位。 如果陣列中有任何值相符, 則輸入會符合。 每個標籤都必須是唯一的。 如果輸入類型不符合標籤的類型, 則結果會是 fallback 值。

下列虛擬代碼會定義`match`運算式的結構。 

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

下列範例會查看`entityType`反升圖層中點特徵的屬性, 以搜尋相符項。 如果找到相符的值, 則會傳回指定的值, 否則會傳回 fallback 值。

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

下列範例會使用陣列來列出一組應全部傳回相同值的標籤。 這比個別列出每個標籤更有效率。 在此情況下, 如果`entityType`屬性是 "餐廳" 或 "grocery_store", 則會傳回 "red" 色彩。

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

下列範例會使用 match 運算式來執行「in 陣列」或「array contains」類型篩選準則, 在此情況下, 會篩選具有允許識別碼清單中識別碼值的資料。 當搭配使用運算式與篩選準則時, 結果必須是布林值。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>聯合運算式

`coalesce`運算式會逐步執行一組運算式, 直到取得第一個非 null 值, 並傳回該值。 

下列虛擬代碼會定義`coalesce`運算式的結構。 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**範例**

下列範例會使用`coalesce`運算式來`textField`設定符號圖層的選項。 如果功能`title`中遺漏了屬性或將設定為`null`, 則運算式會`subtitle`接著嘗試尋找屬性 (如果遺漏或`null`, 則會切換回空字串)。 

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

型別運算式提供的工具可用來測試和轉換不同的資料類型, 例如字串、數位和布林值。

| 運算式 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | array \|物件 | 傳回常值陣列或物件值。 使用此運算式來防止陣列或物件被評估為運算式。 當運算式必須傳回陣列或物件時, 這是必要的。 |
| `['to-boolean', value]` | boolean | 將輸入值轉換為布林值。 當輸入是`false`空字串`false`、 `0` `null`、、或`NaN`時, 結果為, 否則`true`為。 |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | 色彩 | 將輸入值轉換成色彩。 如果提供多個值, 則會依序評估每一個值, 直到取得第一個成功的轉換為止。 如果無法轉換任何輸入, 則運算式會是錯誤。 |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | 號 | 盡可能將輸入值轉換成數位。 如果輸入為`null`或`false`, 則結果為0。 如果輸入為`true`, 則結果為1。 如果輸入為字串, 則會使用 ECMAScript 語言規格的[ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type)字串函數轉換成數位。 如果提供多個值, 則會依序評估每一個值, 直到取得第一個成功的轉換為止。 如果無法轉換任何輸入, 則運算式會是錯誤。 |
| `['to-string', value]` | string | 將輸入值轉換成字串。 如果輸入為`null`, 則結果為`""`。 如果輸入是布林值, 則結果會是`"true"`或`"false"`。 如果輸入是數位, 則會使用 ECMAScript 語言規格的[ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) number 函數將它轉換成字串。 如果輸入是色彩, 則會轉換成 CSS RGBA 色彩字串`"rgba(r,g,b,a)"`。 否則, 會使用 ECMAScript 語言規格的[json.stringify](https://tc39.github.io/ecma262/#sec-json.stringify)函數, 將輸入轉換成字串。 |
| `['typeof', value]` | string | 傳回描述指定值之類型的字串。 |

> [!TIP]
> 如果類似`Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].`于的錯誤訊息出現在瀏覽器主控台中, 表示您的程式碼中有一個運算式, 其中的陣列沒有第一個值的字串。 如果您想要運算式傳回陣列, 請使用`literal`運算式包裝陣列。 下列範例會設定符號圖`offset`層的圖示選項, 其必須是包含兩個數字的陣列, 方法是`match`使用運算式, 根據點的`entityType`屬性值在兩個位移值之間做選擇。特徵.
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

色彩運算式可讓您更輕鬆地建立和操作色彩值。

| 運算式 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | 色彩 | 從*紅色*、*綠色*和*藍色*元件建立色彩值, 其範圍必須介於和`0` `255`之間, 以及的 Alpha 元件`1`。 如果有任何元件超出範圍, 運算式就會是錯誤。 |
| `['rgba', number, number, number, number]` | 色彩 | 從*紅色*、*綠色*、*藍色*元件建立色彩值, 其範圍必須介於`0`和`255`之間, 以及在`0`和`1`範圍內的 Alpha 元件。 如果有任何元件超出範圍, 運算式就會是錯誤。 |
| `['to-rgba']` | \[數位、數位、數位、數位\] | 傳回四個元素的陣列, 其中包含輸入色彩的*紅色*、*綠色*、*藍色*和*Alpha*元件 (依該順序)。 |

**範例**

下列範例會建立具有*紅色* `255`值的和 RGB 色彩值, 並以`temperature`屬性的值乘以`2.5`所計算的*綠色*和藍色值。 當溫度變更時, 色彩會變更為不同的*紅色*陰影。

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

字串運算子運算式會對字串執行轉換作業, 例如串連和轉換案例。 

| 運算式 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | 將多個字串串連在一起。 每個值都必須是字串。 如有需要, 請使用類型運算式將其他實數值型別轉換成字串。`to-string` |
| `['downcase', string]` | string | 將指定的字串轉換為小寫。 |
| `['upcase', string]` | string | 將指定的字串轉換成大寫。 |

**範例**

下列範例會將 point `temperature`功能的屬性轉換成字串, 然後將 "° f" 串連到它的結尾。

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

上述運算式會在地圖上轉譯一個釘選, 其中的文字 "64 ° F" 會重迭在其上方, 如下圖所示。

<center>

![字串運算子運算式範例](media/how-to-expressions/string-operator-expression.png)</center>

## <a name="interpolate-and-step-expressions"></a>插補和步驟運算式

插補和步驟運算式可以用來透過插入曲線或步驟函式來計算值。 這些運算式會採用傳回數值做為其輸入的`['get',  'temperature']`運算式, 例如。 輸入值會針對輸入和輸出值的配對進行評估 (稱為「停止」), 以判斷最適合插入曲線或步驟函式的值。 每個停止的輸入值都必須是數位, 並以遞增順序排列。 輸出值必須是數位、數位陣列或色彩。

### <a name="interpolate-expression"></a>插補運算式

`interpolate`運算式可以透過在停止值之間插入, 來計算連續、平滑的值集合。 傳回色彩值的運算式會產生色彩漸層,其中會選取結果值。`interpolate`

有三種類型的插補方法可以在`interpolate`運算式中使用:
 
* `['linear']`-在一組停止之間呈線性插補。
* `['exponential', base]`-在停止之間以指數方式插補。 `base`值控制輸出增加的速率。 較高的值會使輸出增加至範圍的高階。 接近`base` 1 的值會產生更線性增加的輸出。
* `['cubic-bezier', x1, y1, x2, y2]`-使用指定控制點所定義的[三次方貝茲曲線](https://developer.mozilla.org/docs/Web/CSS/timing-function)來進行內插。

以下是這些不同插補類型外觀的範例。 

| 線性  | 指數 | 三次方貝塞爾 |
|---------|-------------|--------------|
| ![線性插補圖形](media/how-to-expressions/linear-interpolation.png) | ![指數插補圖形](media/how-to-expressions/exponential-interpolation.png) | ![三次方貝塞爾插值圖形](media/how-to-expressions/bezier-curve-interpolation.png) |

下列虛擬代碼會定義`interpolate`運算式的結構。 

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

下列範例會根據 point `linear interpolate`功能的`temperature`屬性, `color`使用運算式來設定氣泡圖層的屬性。 `temperature`如果值小於 60, 則會傳回 "blue", 如果介於60到低於70之間, 則會傳回黃色, 如果介於70和小於 80, 則會傳回黃色, 如果有80或更大, 則會傳回「紅色」。

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

下圖將示範如何為上述運算式選擇色彩。
 
<center>

![插補運算式](media/how-to-expressions/interpolate-expression-example.png)範例</center>

### <a name="step-expression"></a>步驟運算式

運算式可以藉由評估停止所定義的[分段常數](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)函式, 來計算離散的階數結果值。 `step` 

下列虛擬代碼會定義`step`運算式的結構。 

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

步驟運算式會在輸入值之前傳回 stop 的輸出值, 如果輸入小於第一個停止, 則傳回第一個輸入值。 

**範例**

下列範例會根據 point `step`功能的`temperature`屬性, `color`使用運算式來設定氣泡圖層的屬性。 `temperature`如果值小於 60, 則會傳回「藍色」, 如果介於60到低於 70, 則會傳回「黃色」, 如果介於70和小於80之間, 則會傳回「橙色」, 如果有80或更大, 則會傳回「紅色」。

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

下圖將示範如何為上述運算式選擇色彩。
 
<center>

![步驟運算式範例](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>圖層特定運算式

僅適用于特定層級的特殊運算式。

### <a name="heat-map-density-expression"></a>熱度圖密度運算式

熱度圖密度運算式會抓取熱度圖圖層中每個圖元的熱度圖密度值, 並將其`['heatmap-density']`定義為。 這個`0`值是介於和`1`之間的數位, 搭配`interpolation` or `step`運算式使用, 以定義用來為熱度圖著色的色彩漸層。 這個運算式只能用在熱度圖圖層的[色彩選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color)中。

> [!TIP]
> 插補運算式中位於索引0的色彩, 或步驟色彩的預設色彩, 會定義沒有資料並可用來定義背景色彩的區域色彩。 許多人偏好將此值設定為透明或半透明的黑色。 

**範例**

這個範例會使用「程式插值」運算式來建立平滑的色彩漸層, 以呈現熱度圖。 

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

除了使用平滑漸層來為熱度圖著色之外, 也可以使用`step`運算式在一組範圍內指定色彩。 `step`使用運算式來上色熱度圖, 會以視覺化方式將密度分解成類似輪廓或雷達圖樣式地圖的範圍。  

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

如需詳細資訊, 請參閱[新增熱度圖層](map-add-heat-map-layer.md)檔。

### <a name="line-progress-expression"></a>行進度運算式

線條進度運算式會沿著線條圖層中的梯度線抓取進度, 並將定義為`['line-progress']`。 這個值是介於0和1之間的數位, 而且會與`interpolation`或`step`運算式搭配使用。 這個運算式只能與線條圖層的[strokeGradient 選項]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient)搭配使用。 

> [!NOTE]
> 線條圖層的`lineMetrics` `true`選項需要將資料來源的選項設定為。 `strokeGradient`

**範例**

下列範例會使用`['line-progress']`運算式, 將色彩漸層套用至線條的筆劃。

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

[查看即時範例](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>文字欄位格式運算式

文字欄位格式運算式可搭配 [符號圖層`textField` `textOptions` ] 屬性的選項使用, 以提供混合文字格式。 這個運算式允許指定一組輸入字串和格式化選項。 您可以在此運算式中指定每個輸入字串的下列選項。

 * `'font-scale'`-指定字型大小的縮放比例。 如果指定, 這個值將會覆`size`寫個別字串`textOptions`的的屬性。
 * `'text-font'`-指定此字串應使用的一或多個字型系列。 如果指定, 這個值將會覆`font`寫個別字串`textOptions`的的屬性。

下列虛擬代碼會定義文字欄位格式運算式的結構。 

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

下列範例會藉由新增粗體字型並相應增加功能`title`屬性的字型大小, 來格式化文字欄位。 這個範例也會將`subtitle`功能的屬性新增至含有相應縮小字型大小的新行上。

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

此圖層會轉譯點功能, 如下圖所示:
 
<center>

![具有格式化文字欄位](media/how-to-expressions/text-field-format-expression.png)的點特徵影像</center>

### <a name="number-format-expression"></a>數位格式運算式

運算式只能與符號圖層的`textField`選項搭配使用。 `number-format` 這個運算式會將提供的數位轉換成格式化字串。 這個運算式會包裝 JavaScript 的[toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)函數, 並支援下列選項組。

 * `locale`-指定此選項可將數位轉換成字串, 使其與指定的語言對齊。 將[BCP 47 語言標記](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation)傳遞到此選項。
 * `currency`-將數位轉換成代表貨幣的字串。 可能的值為[ISO 4217 貨幣代碼](https://en.wikipedia.org/wiki/ISO_4217), 例如美國元的「美元」、歐元的「歐元」或中文 RMB 的「CNY」。
 * `'min-fraction-digits'`-指定數位的字串版本中要包含的最小小數位數。
 * `'max-fraction-digits'`-指定數位的字串版本中要包含的最大小數位數。

下列虛擬代碼會定義文字欄位格式運算式的結構。 

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

下列範例會使用`number-format`運算式來修改點`textField`特徵的`revenue`屬性在符號圖層的選項中轉譯的方式, 使其顯示 US 美元的值。

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

此圖層會轉譯點功能, 如下圖所示:

<center>

![數位格式運算式範例](media/how-to-expressions/number-format-expression.png)</center>

## <a name="zoom-expression"></a>Zoom 運算式

運算式會在轉譯時用來抓取地圖的目前縮放層級, 且定義為`['zoom']`。 `zoom` 這個運算式會傳回地圖的最小和最大縮放層級範圍之間的數位。 使用此運算式可讓您以動態方式修改樣式, 因為地圖的縮放層級已變更。 運算式只能與`interpolate` 和`step`運算式搭配使用。 `zoom`

**範例**

根據預設, 熱度圖圖層中轉譯之資料點的半徑對於所有縮放層級都有固定圖元半徑。 因為地圖會將資料匯總放大, 而且熱度圖層看起來會不同。 `zoom`運算式可以用來調整每個縮放層級的半徑, 讓每個資料點都涵蓋對應的相同實體區域。 這會讓熱度圖層看起來更為靜態且一致。 地圖的每個縮放層級都會以垂直和水準方式, 與上一個縮放層級相同的圖元數倍。 調整半徑, 讓每個縮放層級加倍, 會建立在所有縮放比例上看起來一致的熱度圖。 您可以使用`zoom`運算式`base 2 exponential interpolation`搭配運算式來完成這項作業, 如下所示。 

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

[查看即時範例](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>變數系結運算式

變數系結運算式會將計算的結果儲存在變數中, 讓運算式中的其他地方可以多次參考, 而不必重新計算。 對於牽涉到許多計算的運算式而言, 這是有用的優化

| 運算式 | 傳回類型 | 描述 |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;「let」,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: string、<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: any、<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: 字串、<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: any、<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | 儲存一或多個值做為變數, 以`var`供傳回結果的子運算式中的運算式使用。 |
| `['var', name: string]` | 任何 | 參考使用`let`運算式建立的變數。 |

**範例**

這個範例會使用運算式來計算相對於溫度比率的收益, 然後使用`case`運算式來評估此值的不同布耳運算。 運算式用來儲存相對於溫度比率的收益, 因此它只需要計算一次`var` , 而且運算式會經常參考此變數而不需要重新計算。 `let`

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

如需更多執行運算式的程式碼範例, 請參閱下列文章:

> [!div class="nextstepaction"] 
> [新增符號圖層](map-add-pin.md)

> [!div class="nextstepaction"] 
> [新增泡泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)

> [!div class="nextstepaction"] 
> [新增熱度圖圖層](map-add-heat-map-layer.md)

深入瞭解支援運算式的圖層選項:

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
