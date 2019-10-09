---
title: Azure Data Factory 對應資料流程聯結轉換
description: Azure Data Factory 對應資料流程聯結轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 48cf9d58c8acd85e545a5bcb5104d7069670e349
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029317"
---
# <a name="mapping-data-flow-join-transformation"></a>對應資料流程聯結轉換



在資料流程中使用聯結來結合來自兩個資料表中的資料。 按一下將成為左側關聯的轉換，並且從工具箱加入「聯結」轉換。 在聯結轉換內，您將從資料流程選取另一個資料流程成為右側關聯。

![聯結轉換](media/data-flow/join.png "聯結")

## <a name="join-types"></a>聯結類型

選取聯結轉換所需的聯結類型。

### <a name="inner-join"></a>內部聯結

內部聯結只會傳遞符合兩個數據表之資料行條件的資料列。

### <a name="left-outer"></a>左方外部

左側資料流所有不符合聯結條件的資料列為可通過，而除了內部聯結所傳回的資料列之外，另一個資料表的輸出資料行是設為 NULL。

### <a name="right-outer"></a>右方外部

右側資料流所有不符合聯結條件的資料列可通過，而除了內部聯結所傳回的所有資料列之外，對應於另一個資料表的輸出資料行是設為 NULL。

### <a name="full-outer"></a>完整外部

Full Outer 會從兩端產生具有 Null 值的所有資料行和資料列，而不會出現在另一個資料表中。

### <a name="cross-join"></a>交叉聯結

使用運算式指定兩個數據流的交叉乘積。 您可以使用這個來建立自訂的聯結條件。

## <a name="specify-join-conditions"></a>指定聯結條件

左方聯結條件來自與聯結的左側連接的資料串流。 右側聯結是與底部的聯結相連接的第二個資料串流，這是另一個資料流的直接連接器或另一個資料流的參考。

您必須至少輸入 1 (1..n) 個聯結條件。 這些可能是直接參考、從下拉式選單中選取或運算式的欄位。

## <a name="join-performance-optimizations"></a>聯結效能最佳化

不同於 SSIS 之類的工具中的合併聯結，ADF 資料流程不是必要的合併聯結作業。 因此，聯結索引鍵不需要先排序。 聯結作業會根據 Spark 中的最佳聯結作業進行：廣播 / 對應端聯結：

![聯結轉換最佳化](media/data-flow/joinoptimize.png "聯結最佳化")

如果您的資料集可以放入背景工作節點記憶體中，我們可以將您的聯結效能優化。 您也可以對於聯結作業指定資料分割，以建立更適合每個背景工作記憶體的資料集。

## <a name="self-join"></a>自我聯結

您可以使用選取轉換來設定現有資料流的別名，以達到 ADF 資料流程中的自我聯結條件。 首先，從資料流建立「新分支」，然後新增「選取」來設定整個原始資料串流的別名。

![自我聯結](media/data-flow/selfjoin.png "自我聯結")

在上圖中，選取轉換位於最上層。 這是在設定原始資料流的別名為「OrigSourceBatting」。 在下方反白顯示的聯結轉換中，您可以看到，我們使用這個選取別名資料流做為右邊的聯結，以便我們參考內部聯結左右兩邊的同個索引鍵。

## <a name="composite-and-custom-keys"></a>複合和自訂索引鍵

您可以在聯結轉換內即時建立自訂和複合索引鍵。 使用每個關聯性資料列旁的加號（+），加入其他聯結資料行的資料列。 或者，在 [運算式產生器] 中為即時聯結值計算新的索引鍵值。

## <a name="next-steps"></a>後續步驟

聯結資料之後，您可以[建立新](data-flow-derived-column.md)的資料行，並將[您的資料接收到目的地資料存放區](data-flow-sink.md)。
