---
title: 在 Azure Data Factory 中的對應資料流程中使用 JSON
description: Azure Data Factory 對應資料流程具有內建功能，可處理具有階層的 JSON 檔
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: fe412e9e682fb55f1664c546e6b6c5a347527adb
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387353"
---
# <a name="mapping-data-flow-json-handling"></a>對應資料流程 JSON 處理

## <a name="creating-json-structures-in-derived-column"></a>在衍生的資料行中建立 JSON 結構

您可以透過衍生的資料行運算式產生器，將複雜的資料行加入至您的資料流程。 在 [衍生的資料行] 轉換中，加入新的資料行，然後按一下藍色方塊來開啟 [運算式產生器]。 若要讓資料行變得複雜，您可以手動輸入 JSON 結構，或使用 UX 以互動方式加入個子。

### <a name="using-the-expression-builder-ux"></a>使用運算式產生器 UX

在輸出架構側邊窗格中，將滑鼠停留在資料行上，然後按一下加號圖示。 選取 [**加入 subcolumn** ]，將資料行設為複雜類型。

![新增 subcolumn](media/data-flow/addsubcolumn.png "新增 Subcolumn")

您可以用相同的方式加入其他資料行和個子。 針對每個非複雜欄位，可以在右邊的運算式編輯器中加入運算式。

![複雜資料行](media/data-flow/complexcolumn.png "複雜資料行")

### <a name="entering-the-json-structure-manually"></a>手動輸入 JSON 結構

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

使用 JSON 資料集做為資料流程中的來源，可讓您設定五個額外的設定。 這些設定可以在 [**來源選項**] 索引標籤的 [ **JSON 設定**] [可折疊] 底下找到。  

![JSON 設定](media/data-flow/json-settings.png "JSON 設定")

### <a name="default"></a>預設值

根據預設，JSON 資料會以下列格式讀取。

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>單一檔

如果選取了 [**單一**檔]，則對應的資料流程會從每個檔案讀取一個 JSON 檔。 

``` json
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

如果選取了不具引號的資料**行名稱**，則對應資料流程會讀取未以引號括住的 JSON 資料行。 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>有批註

如果 JSON 資料具有 C 或C++樣式批註，請選取 [**具有批註**]。

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>單引號

如果 JSON 欄位和值使用單引號，而不是雙引號，請選取 [**單引號**]。

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>反斜線已轉義

如果使用反斜線來轉義 JSON 資料中的字元，請選取 [**單引號**]。

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>高階函數

較高順序的函式是接受一或多個函式做為引數的函數。 以下是對應可啟用陣列作業的資料流程所支援的高階函數清單。

### <a name="filter"></a>filter
篩選出不符合所提供述詞之陣列中的元素。 篩選準則預期述詞函式中某個元素的參考 #item。

#### <a name="examples"></a>範例
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>map
使用提供的運算式，將陣列的每個元素對應至新的專案。 對應預期運算式函數中的一個元素參考為 #item。

#### <a name="examples"></a>範例
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>減少
累加陣列中的元素。 減少預期第一個運算式函式中的累計和一個元素的參考是 #acc 和 #item，而且它預期產生的值會當做第二個運算式函式中使用的 #result。

#### <a name="examples"></a>範例
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>sort
使用提供的述詞函式來排序陣列。 排序需要運算式函式中兩個連續元素的參考做為 #item1 和 #item2。

#### <a name="examples"></a>範例
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>contains
如果提供之陣列中的任何元素在所提供的述詞中評估為 true，則傳回 true。 Contains 需要述詞函式中某個元素的參考做為 #item。

#### <a name="examples"></a>範例
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>後續步驟

* [使用「衍生的資料行」轉換來建立階層式結構](data-flow-derived-column.md)
