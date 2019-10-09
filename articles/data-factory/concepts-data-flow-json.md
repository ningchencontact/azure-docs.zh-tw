---
title: Azure Data Factory 對應的資料流程 JSON 概念
description: Data Factory 對應資料流程具有內建功能，可處理具有階層的 JSON 檔
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 37db3e153e8dfcbc1120fcb1f6d2f77187edc78e
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029658"
---
# <a name="mapping-data-flow-json-handling"></a>對應資料流程 JSON 處理



## <a name="creating-json-structures-in-expression-editor"></a>在運算式編輯器中建立 JSON 結構
### <a name="derived-column-transformation"></a>衍生的資料行轉換
透過衍生的「資料行運算式編輯器」，可以更輕鬆地將複雜的資料行加入至您的資料流程。 加入新的資料行並開啟編輯器之後，有兩個選項：手動輸入 JSON 結構，或使用 UI 以互動方式加入個子。

#### <a name="interactive-ui-json-design"></a>互動式 UI JSON 設計
從 [輸出架構] 窗格中，您可以使用 [`+`] 功能表來加入新的個子：![新增 subcolumn](media/data-flow/addsubcolumn.png "新增 subcolumn")

您可以從該處以相同的方式加入新的資料行和個子。 針對每個非複雜欄位，可以在右邊的運算式編輯器中加入運算式。

![複雜資料行](media/data-flow/complexcolumn.png "複雜資料行")

#### <a name="manual-json-design"></a>手動 JSON 設計
若要手動加入 JSON 結構，請加入新的資料行，並在編輯器中輸入運算式。 運算式會遵循下列一般格式：
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
如果已針對名為 "complexColumn" 的資料行輸入此運算式，則會將它寫入至接收，做為下列 JSON：
```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>完整階層式定義的範例手動腳本
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>來源格式選項
### <a name="default"></a>預設
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>單一檔
* 選項一
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* 選項二
```
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>不具引號的資料行名稱
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>有批註
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>單引號
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>反斜線已轉義
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>高階函數
## <a name="filter"></a>filter
篩選出不符合所提供述詞之陣列中的元素。 篩選準則預期述詞函式中某個元素的參考 #item。

### <a name="examples"></a>範例
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>map
使用提供的運算式，將陣列的每個元素對應至新的專案。 對應預期運算式函數中的一個元素參考為 #item。

### <a name="examples"></a>範例
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>減少
累加陣列中的元素。 減少預期第一個運算式函式中的累計和一個元素的參考是 #acc 和 #item，而且它預期產生的值會當做第二個運算式函式中使用的 #result。

### <a name="examples"></a>範例
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>sort
使用提供的述詞函式來排序陣列。 排序需要運算式函式中兩個連續元素的參考做為 #item1 和 #item2。

### <a name="examples"></a>範例
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>contains
如果提供之陣列中的任何元素在所提供的述詞中評估為 true，則傳回 true。 Contains 需要述詞函式中某個元素的參考做為 #item。

### <a name="examples"></a>範例
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>後續步驟

* [使用「衍生的資料行」轉換來建立階層式結構](data-flow-derived-column.md)
