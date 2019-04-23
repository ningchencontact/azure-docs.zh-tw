---
title: Azure Data Factory 對應資料流程樞紐轉換
description: 使用 Azure Data Factory 對應資料流動樞紐轉換的資料行的 powerpivot 資料的資料列
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e16cac281b77f3ca93d9ef358ae806203bc8b663
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794358"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure 資料處理站樞紐轉換
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用 ADF 資料流程中的樞紐做為彙總，其中一個或多個群組資料行都有個別的資料行值會轉換成相異資料列。 基本上，您可以將資料列值樞紐處理為新的資料行 (將資料轉換成中繼資料)。

![樞紐選項](media/data-flow/pivot1.png "樞紐 1")

## <a name="group-by"></a>分組依據

![樞紐選項](media/data-flow/pivot2.png "樞紐 2")

首先，設定您想要對於樞紐彙總分組所依據的資料行。 您可以在這裡使用資料行清單旁邊的 + 號設定 1 個以上資料行。

## <a name="pivot-key"></a>樞紐索引鍵

![樞紐選項](media/data-flow/pivot3.png "樞紐 3")

樞紐索引鍵是 ADF 從資料列樞紐處理為資料行的資料行。 根據預設，此欄位的資料集之內的每個唯一值都會樞紐處理為資料行。 不過，您可以選擇從資料集輸入您想要樞紐處理為資料行值的數值。 這是會決定將會建立新的資料行的資料行。

## <a name="pivoted-columns"></a>樞紐資料行

![樞紐選項](media/data-flow/pivot4.png "樞紐 4")

最後，選擇您想要對已樞紐的值所使用的彙總，以及您要如何在轉換的新輸出投影中顯示資料行。

(選擇性) 您可以設定對於每個新資料行名稱從資料列新增前置詞、中置詞及後置詞的命名模式。

例如，依「區域」樞紐處理「銷售」會從每個銷售值產生新的資料行值，也就是"25"、"50"、"1000" 等等。不過，如果您設定的 「 銷售-"前置詞值，每個資料行值會新增"Sales-"開頭的值。

![樞紐選項](media/data-flow/pivot5.png "樞紐 5")

設定為「一般」資料行的排列方式，會使用彙總值將已樞紐的資料行分組在一起。 將資料行排列方式設定為「橫向」會在資料行和值之間交替。

### <a name="aggregation"></a>彙總

若要對於樞紐值設定您想要使用的彙總，請按一下「已樞紐的資料行」窗格底部的欄位。 您會進入 ADF 資料流程運算式產生器，您可以在其中建立彙總運算式，並對於新的彙總值提供描述性的別名名稱。

使用 ADF 資料流程運算式語言在運算式產生器描述已樞紐的資料行轉換： https://aka.ms/dataflowexpressions。

## <a name="pivot-metadata"></a>Pivot 中繼資料

「 樞紐 」 轉換會產生是動態的內送資料為基礎的新資料行名稱。 樞紐索引鍵會產生每個新的資料行名稱的值。 如果您未指定個別的值及想要建立您的樞紐索引鍵中的每個唯一值的動態資料行名稱，然後 UI 不會顯示在檢查的中繼資料，而且會有任何的資料行傳播到 「 接收 」 轉換。 如果您為樞紐索引鍵的值，然後 ADF 可以判斷新的資料行名稱，這些資料行名稱將會為您提供的檢查，並接收對應

### <a name="landing-new-columns-in-sink"></a>登陸在接收中的新資料行

即使使用 Pivot 中的動態資料行名稱，您仍然可以到您的目的地存放區接收您的新資料行名稱和值。 只接收設定中設定為在 「 允許結構描述漂移 」。 您不會看到新的動態名稱資料行的中繼資料，但結構描述漂移選項可讓您讓資料登陸到。

### <a name="view-metadata-in-design-mode"></a>在設計模式中檢視中繼資料

如果您想要檢視新的資料行名稱做為在檢查的中繼資料，而您想要查看明確地傳播到 「 接收 」 轉換的資料行，然後在 [樞紐索引鍵] 索引標籤中設定明確的值。

### <a name="how-to-rejoin-original-fields"></a>如何重新加入原始欄位
樞紐轉換只會投出使用在彙總、分組和中樞動作中的資料行。 如果您想要納入您的流程中的上一個步驟中的其他資料行，使用新的分支，從上一個步驟，並使用自我聯結模式來連接使用原始的中繼資料的流程。

## <a name="next-steps"></a>後續步驟

請嘗試[取消樞紐轉換](data-flow-unpivot.md)來將資料行值轉換成資料列的值。 
