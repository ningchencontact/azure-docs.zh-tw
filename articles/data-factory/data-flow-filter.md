---
title: Azure Data Factory 對應資料流程中的篩選轉換
description: 使用 Azure Data Factory 對應資料流程中的篩選準則轉換來篩選出資料列
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 1daff431fc217c08f3bc3c5aeb3b4711691909c0
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132530"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>對應資料流程中的篩選轉換

篩選轉換可讓您根據條件來進行資料列篩選。 輸出資料流程包含符合篩選準則的所有資料列。 篩選器轉換類似于 SQL 中的 WHERE 子句。

## <a name="configuration"></a>組態

使用資料流程運算式產生器，為篩選準則輸入運算式。 若要開啟 [運算式產生器]，請按一下藍色方塊。 篩選準則的類型必須是布林值。 如需如何建立運算式的詳細資訊，請參閱[expression builder](concepts-data-flow-expression-builder.md)檔。

![篩選轉換](media/data-flow/filter1.png "篩選轉換")

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>範例

下列範例是一個篩選器轉換，名為 `FilterBefore1960`，接受傳入串流 `CleanData`。 篩選準則是運算式 `year <= 1960`。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![篩選轉換](media/data-flow/filter1.png "篩選轉換")

此轉換的資料流程腳本位於下列程式碼片段中：

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>後續步驟

使用[select 轉換](data-flow-select.md)來篩選出資料行
