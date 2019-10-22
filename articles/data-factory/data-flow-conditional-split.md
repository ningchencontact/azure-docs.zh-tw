---
title: Azure Data Factory 對應資料流程中的條件式分割轉換 |Microsoft Docs
description: 使用 Azure Data Factory 對應資料流程中的條件式分割轉換，將資料分割成不同的資料流程
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 2d794714f27340e8886843988b6c075dd8d3366e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72527434"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>對應資料流程中的條件式分割轉換

「條件式分割」轉換會根據比對條件，將資料列路由傳送至不同的資料流程。 「條件式分割」轉換類似于程式設計語言中的 CASE 決策結構。 轉換會評估運算式，並根據結果將資料列導向特定資料流。

## <a name="configuration"></a>組態

[**分割依據**] 設定會決定資料列是否流向第一個相符的資料流程或其符合的每個資料流程。

使用資料流程運算式產生器，為分割條件輸入運算式。 若要加入新的條件，請按一下現有資料列中的加號圖示。 您也可以針對不符合任何條件的資料列加入預設資料流程。

![條件式分割](media/data-flow/conditionalsplit1.png "條件式分割選項")

## <a name="data-flow-script"></a>資料流程腳本

### <a name="syntax"></a>語法

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>範例

下列範例是一個名為 `SplitByYear` 的條件式分割轉換，它會接受傳入的資料流程 `CleanData`。 此轉換有兩個 `year < 1960` 和 `year > 1980` 的分割條件。 `disjoint` 為 false，因為資料會移至第一個符合的條件。 符合第一個條件的每個資料列都會進入輸出資料流程 `moviesBefore1960`。 所有符合第二個條件的剩餘資料列都會移至輸出資料流程 `moviesAFter1980`。 所有其他資料列會流經預設資料流程 `AllOtherMovies`。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![條件式分割](media/data-flow/conditionalsplit1.png "條件式分割選項")

此轉換的資料流程腳本位於下列程式碼片段中：

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>後續步驟

搭配條件式分割使用的一般資料流程轉換包括[聯結轉換](data-flow-join.md)、[查閱轉換](data-flow-lookup.md)和[選取轉換](data-flow-select.md)
