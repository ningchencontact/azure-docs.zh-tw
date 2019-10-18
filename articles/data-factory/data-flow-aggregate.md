---
title: Azure Data Factory 對應資料流程中的匯總轉換 |Microsoft Docs
description: 瞭解如何使用對應資料流程匯總轉換，在 Azure Data Factory 中大規模匯總資料。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: add548a184440c408b8b74e131f2249b4f616ddc
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514838"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>對應資料流程中的匯總轉換 

「匯總」轉換會定義資料流程中資料行的匯總。 使用運算式產生器，您可以定義不同類型的匯總，例如 SUM、MIN、MAX 和 COUNT，並依現有或計算資料行分組。

## <a name="group-by"></a>分組依據

選取現有的資料行，或建立新的計算資料行，作為匯總的 group by 子句使用。 若要使用現有的資料行，請從下拉式清單中選取它。 若要建立新的計算資料行，請將滑鼠停留在子句上，然後按一下 [**計算資料行**]。 這會開啟[資料流程運算式](concepts-data-flow-expression-builder.md)產生器。 建立計算資料行之後，請在 [**名稱**] 欄位中輸入輸出資料行名稱。 如果您想要加入額外的 group by 子句，請將滑鼠停留在現有的子句上方，然後按一下加號圖示。

![依設定匯總轉換群組](media/data-flow/agg.png "依設定匯總轉換群組")

Group by 子句在匯總轉換中是選擇性的。

## <a name="aggregate-column"></a>匯總資料行 

移至 [**匯總**] 索引標籤以建立匯總運算式。 您可以使用匯總來覆寫現有的資料行，或使用新的名稱來建立新的欄位。 匯總運算式會在資料行名稱選取器旁的右側方塊中輸入。 若要編輯運算式，請按一下文字方塊來開啟 [運算式產生器]。 若要加入其他匯總，請將滑鼠停留在現有的運算式上，然後按一下加號圖示，以建立新的匯總資料行或資料[行模式](concepts-data-flow-column-pattern.md)。

每個匯總運算式都必須包含至少一個彙總函式。

![匯總轉換匯總設定](media/data-flow/agg2.png "匯總轉換匯總設定")


> [!NOTE]
> 在 [調試] 模式中，[運算式產生器] 無法使用彙總函式來產生資料預覽。 若要查看匯總轉換的資料預覽，請關閉 [運算式產生器]，並透過 [資料預覽] 索引標籤來查看資料。

## <a name="reconnect-rows-and-columns"></a>重新連接資料列和資料行

匯總轉換類似 SQL 匯總選取查詢。 不包含在 group by 子句或彙總函式中的資料行，將不會流經匯總轉換的輸出。 如果您想要在匯總輸出中包含其他資料行，請執行下列其中一種方法：

* 請使用彙總函式（例如 `last()` 或 `first()`）來包含該額外的資料行。
* 使用[自我聯結模式](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)，將資料行重新加入至您的輸出資料流程。

## <a name="data-flow-script"></a>資料流程腳本

### <a name="syntax"></a>語法

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>範例

下列範例會將傳入的資料流程 `MoviesYear`，並依資料行 `year` 將資料列分組。 轉換會建立 `avgrating` 的匯總資料行，評估為數據行 `Rating` 的平均值。 這個匯總轉換會命名為 `AvgComedyRatingsByYear`。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![依範例分組](media/data-flow/agg-script1.png "依範例分組")

![匯總範例](media/data-flow/agg-script2.png "匯總範例")

此轉換的資料流程腳本位於下列程式碼片段。

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>後續步驟

* 使用[視窗轉換](data-flow-window.md)來定義以視窗為基礎的匯總
