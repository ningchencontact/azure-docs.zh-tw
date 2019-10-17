---
title: Azure Data Factory 對應的資料流程代理金鑰轉換
description: 如何使用 Azure Data Factory 的對應資料流程代理金鑰轉換來產生連續索引鍵值
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 144d6298a13d35d94a68b35c443a3a47cefcfc2a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387169"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>對應資料流程代理金鑰轉換



使用 Surrogate 索引鍵轉換，將遞增的非商務任意索引鍵值新增至您的資料流資料列集。 這在設計星型結構描述分析資料模型的維度資料表時非常有用，您維度資料表中的每個成員都需要有唯一的非商務索引鍵 (Kimball DW 方法論的一部分)。

![代理鍵轉換](media/data-flow/surrogate.png "代理鍵轉換")

[索引鍵資料行] 是您要為新 Surrogate 索引鍵資料行取的名稱。

[開始值] 是遞增值的開始點。

## <a name="increment-keys-from-existing-sources"></a>從現有來源遞增索引鍵

如果您想要從存在於來源中的值開始順序，可以在您的代理鍵轉換後立即使用「衍生的資料行」轉換，並將這兩個值加在一起：

![SK 新增上限](media/data-flow/sk006.png "代理鍵轉換新增最大值")

若要使用先前的 max 來植入索引鍵值，有兩種技術可供您使用：

### <a name="database-sources"></a>資料庫來源

使用 [查詢] 選項，從您的來源使用來源轉換來選取 MAX （）：

![代理鍵查詢](media/data-flow/sk002.png "代理金鑰轉換查詢")

### <a name="file-sources"></a>檔案來源

如果您先前的最大值是在檔案中，您可以使用來源轉換搭配「匯總」轉換，並使用 MAX （）運算式函數來取得先前的最大值：

![代理金鑰檔案](media/data-flow/sk008.png "代理金鑰檔案")

在這兩種情況下，您都必須將傳入的新資料與包含先前最大值的來源聯結在一起：

![代理鍵聯結](media/data-flow/sk004.png "代理鍵聯結")

## <a name="next-steps"></a>後續步驟

這些範例使用[聯結](data-flow-join.md)和衍生的資料[行](data-flow-derived-column.md)轉換。
