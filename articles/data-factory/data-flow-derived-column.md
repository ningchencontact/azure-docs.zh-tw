---
title: 對應資料流程中的衍生資料行轉換-Azure Data Factory |Microsoft Docs
description: 瞭解如何使用對應的資料流程衍生的資料行轉換，在 Azure Data Factory 中大規模轉換資料。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 5a4ee90717d46fe593d9e10083b349e069216dac
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436753"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>對應資料流程中的衍生資料行轉換

使用「衍生的資料行」轉換，在資料流程中產生新的資料行，或修改現有的欄位。

## <a name="derived-column-settings"></a>衍生的資料行設定

若要覆寫現有的資料行，請透過 [資料行] 下拉式清單加以選取。 否則，請使用 [資料行選取] 欄位作為文字方塊，然後輸入新資料行的名稱。 若要建立衍生資料行的運算式，請按一下 [輸入運算式] 方塊，以開啟 [資料流程[運算式](concepts-data-flow-expression-builder.md)產生器]。

![衍生的資料行設定](media/data-flow/dc1.png "衍生的資料行設定")

若要加入其他衍生的資料行，請將滑鼠停留在現有的衍生資料行上，然後按一下加號圖示。 請選擇 [**加入資料行**] 或 [新增資料**行模式**]。 如果您的資料行名稱是來源的變數，則資料行模式可能會很有用。 如需詳細資訊，請參閱資料[行模式](concepts-data-flow-column-pattern.md)。

![新增衍生的資料行選取](media/data-flow/columnpattern.png "新增衍生的資料行選取")

## <a name="data-flow-script"></a>資料流程腳本

### <a name="syntax"></a>語法

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>範例

下列範例是名為 `CleanData` 的衍生資料行，其接受傳入串流 `MoviesYear`，並建立兩個衍生的資料行。 第一個衍生的資料行會將資料行 `Rating` 取代為整數類型的評等值。 第二個衍生的資料行是一種模式，它會比對名稱開頭為 ' 電影 ' 的每個資料行。 針對每個相符的資料行，它會建立一個 `movie` 的資料行，它等於前面加上 ' movie_ ' 的相符資料行的值。 在 Data Factory UX 中，這項轉換看起來如下圖所示：

![衍生範例](media/data-flow/derive-script1.png "衍生範例")

此轉換的資料流程腳本位於下列程式碼片段中：

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>後續步驟

- 深入瞭解對應的[資料流程運算式語言](data-flow-expression-functions.md)。
