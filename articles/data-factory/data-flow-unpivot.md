---
title: Azure Data Factory 對應資料流程樞紐轉換
description: Azure Data Factory 對應資料流程樞紐轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 160ff8bbcb8ce5a4f1f32245cc366281640c5919
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348033"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory 取消樞紐轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用 ADF 對應資料流程中的 [取消樞紐] 來將非正常化資料集轉換成較正常化的版本，作法是將單一資料列之多個資料行的值展開為單一資料行中相同值的多個資料列。

![取消樞紐轉換](media/data-flow/unpivot1.png "取消樞紐選項 1")

## <a name="ungroup-by"></a>取消分組依據

![取消樞紐轉換](media/data-flow/unpivot5.png "取消樞紐選項 2")

首先，設定您想要對於樞紐彙總分組所依據的資料行。 使用資料行清單旁邊的 [+] 號，設定要取消群組的一或多個資料行。

## <a name="unpivot-key"></a>取消樞紐索引鍵

![取消樞紐轉換](media/data-flow/unpivot6.png "取消樞紐選項 3")

樞紐索引鍵是 ADF 從資料列樞紐處理為資料行的資料行。 根據預設，此欄位的資料集之內的每個唯一值都會樞紐處理為資料行。 不過，您可以選擇從資料集輸入您想要樞紐處理為資料行值的數值。

## <a name="unpivoted-columns"></a>取消資料行樞紐

![取消樞紐轉換](media/data-flow//unpivot7.png "取消樞紐選項 4")

最後，選擇您想要對已樞紐的值所使用的彙總，以及您要如何在轉換的新輸出投影中顯示資料行。

(選擇性) 您可以設定對於每個新資料行名稱從資料列值新增前置詞、中置詞及後置詞的命名模式。

比方說，根據 "Region" 對 "Sales" 進行樞紐處理，就會得到來自每個銷售值的新資料行值。 例如︰"25"、"50"、"1000"、...不過，如果您設定前置詞值為 "Sales"，則系統就會以 "Sales" 作為值的前置詞。

<img src="media/data-flow/unpivot3.png" width="400">

設定為「一般」資料行的排列方式，會使用彙總值將已樞紐的資料行分組在一起。 將資料行排列方式設定為「橫向」會在資料行和值之間交替。

![取消樞紐轉換](media/data-flow//unpivot7.png "取消樞紐選項 5")

最終之取消樞紐的資料結果集所顯示的資料行總計，現已取消樞紐為個別的資料列值。

## <a name="next-steps"></a>後續步驟

使用[樞紐轉換](data-flow-pivot.md)樞紐資料行的資料列。
