---
title: Azure Data Factory 資料流 Surrogate 索引鍵轉換
description: 如何使用 Azure Data Factory 的對應資料流動 Surrogate 索引鍵 轉換產生循序的索引鍵值
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: eaa1c577f7e208400d3430222b006e0dbbd7956a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61350339"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>對應資料流程 Surrogate 索引鍵轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用 Surrogate 索引鍵轉換，將遞增的非商務任意索引鍵值新增至您的資料流資料列集。 這在設計星型結構描述分析資料模型的維度資料表時非常有用，您維度資料表中的每個成員都需要有唯一的非商務索引鍵 (Kimball DW 方法論的一部分)。

![Surrogate 索引鍵轉換](media/data-flow/surrogate.png "Surrogate 索引鍵轉換")

[索引鍵資料行] 是您要為新 Surrogate 索引鍵資料行取的名稱。

[開始值] 是遞增值的開始點。

## <a name="increment-keys-from-existing-sources"></a>遞增索引鍵，從現有來源

如果您想要啟動您的順序從來源中的值，您可以使用緊接 Surrogate 索引鍵轉換 「 衍生的資料行 」 轉換，並一起加入兩個值：

![SK 加入最大](media/data-flow/sk006.png "Surrogate 索引鍵轉換加入最大值")

若要植入使用先前的最大的索引鍵值，有兩種技術可供您：

### <a name="database-sources"></a>資料庫來源

您可以使用 「 查詢 」 選項，從您使用 「 來源 」 轉換的來源選取 max （）：

![Surrogate 索引鍵的查詢](media/data-flow/sk002.png "Surrogate 索引鍵轉換查詢")

### <a name="file-sources"></a>檔案來源

如果您先前的最大值是在檔案中，您可以使用您的來源轉型，以及彙總 」 轉換，並使用 max （） 運算式函數來取得先前的最大值：

![Surrogate 索引鍵的檔案](media/data-flow/sk008.png "Surrogate 索引鍵的檔案")

在這兩種情況下，您必須將您連入的新資料與您的來源，其中包含先前的最大值：

![Surrogate 索引鍵聯結](media/data-flow/sk004.png "Surrogate 索引鍵聯結")

## <a name="next-steps"></a>後續步驟

這些範例會使用[聯結](data-flow-join.md)並[衍生的資料行](data-flow-derived-column.md)轉換。
