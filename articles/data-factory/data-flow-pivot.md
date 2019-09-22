---
title: Azure Data Factory 對應資料流程樞紐轉換
description: 使用 Azure Data Factory 對應資料流程樞紐分析表轉換，從資料列到資料行的樞紐分析表
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 0b68007f8c3383997f0d31888198af866d38b590
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178647"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure data factory 資料透視轉換
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用 ADF 資料流程中的樞紐做為彙總，其中一個或多個群組資料行都有個別的資料行值會轉換成相異資料列。 基本上，您可以將資料列值樞紐處理為新的資料行 (將資料轉換成中繼資料)。

![樞紐選項](media/data-flow/pivot1.png "樞紐 1")

## <a name="group-by"></a>分組依據

![樞紐選項](media/data-flow/pivot2.png "樞紐 2")

首先，設定您想要對於樞紐彙總分組所依據的資料行。 您可以在這裡使用資料行清單旁邊的 + 號設定 1 個以上資料行。

## <a name="pivot-key"></a>Pivot 索引鍵

![樞紐選項](media/data-flow/pivot3.png "樞紐 3")

樞紐索引鍵是 ADF 從資料列樞紐處理為資料行的資料行。 根據預設，此欄位的資料集之內的每個唯一值都會樞紐處理為資料行。 不過，您可以選擇從資料集輸入您想要樞紐處理為資料行值的數值。 這是將會決定要建立之新資料行的資料行。

## <a name="pivoted-columns"></a>透視資料行

![樞紐選項](media/data-flow/pivot4.png "樞紐 4")

最後，選擇您想要對已樞紐的值所使用的彙總，以及您要如何在轉換的新輸出投影中顯示資料行。

(選擇性) 您可以設定對於每個新資料行名稱從資料列新增前置詞、中置詞及後置詞的命名模式。

例如，依「區域」樞紐處理「銷售」會從每個銷售值產生新的資料行值，也就是"25"、"50"、"1000" 等等。不過，如果您設定「銷售-」的前置詞值，每個資料行值都會在值的開頭加上「Sales-」。

![樞紐選項](media/data-flow/pivot5.png "樞紐 5")

設定為「一般」資料行的排列方式，會使用彙總值將已樞紐的資料行分組在一起。 將資料行排列方式設定為「橫向」會在資料行和值之間交替。

### <a name="aggregation"></a>彙總

若要對於樞紐值設定您想要使用的彙總，請按一下「已樞紐的資料行」窗格底部的欄位。 您會進入 ADF 資料流程運算式產生器，您可以在其中建立彙總運算式，並對於新的彙總值提供描述性的別名名稱。

使用 ADF 資料流程運算式語言在運算式產生器描述已樞紐的資料行轉換： https://aka.ms/dataflowexpressions 。

## <a name="pivot-metadata"></a>Pivot 中繼資料

「資料透視」轉換會根據您的傳入資料，產生動態的新資料行名稱。 Pivot 索引鍵會針對每個新的資料行名稱產生值。 如果您未指定個別的值，而且想要在資料透視機碼中建立每個唯一值的動態資料行名稱，則 UI 不會在 [檢查] 中顯示中繼資料，而且不會對接收轉換進行任何資料行傳播。 如果您設定了 Pivot 索引鍵的值，則 ADF 可以決定新的資料行名稱，而這些資料行名稱將可供您在檢查和接收對應中使用。

### <a name="generate-a-new-model-from-dynamic-columns"></a>從動態資料行產生新的模型

Pivot 會根據資料列值動態產生新的資料行名稱。 您可以將這些新的資料行轉換成中繼資料，稍後可在資料流程中加以參考。 若要這麼做，請按一下 [資料預覽] 索引標籤。您的 Pivot 轉換所產生的所有新資料行都會顯示在資料表標頭中具有 "漂移" 圖示。 按一下 [地圖漂移] 按鈕，將新的資料行轉換成中繼資料，使其成為資料流程模型的一部分。

![Pivot 資料行](media/data-flow/newpivot1.png "Map 漂移 Pivot 資料行")

### <a name="landing-new-columns-in-sink"></a>登陸接收器中的新資料行

即使在 Pivot 中使用動態資料行名稱，您仍然可以在目的地存放區中接收新的資料行名稱和值。 直接在您的接收設定中，將 [允許架構漂移] 設定為 [開啟]。 您不會在資料行中繼資料中看到新的動態名稱，但是 [架構漂移] 選項可讓您放入資料。

### <a name="view-metadata-in-design-mode"></a>在設計模式中查看中繼資料

如果您想要在 [檢查] 中將新的資料行名稱當做中繼資料來查看，而且您希望查看資料行明確地傳播到接收轉換，請在 [Pivot 索引鍵] 索引標籤中設定明確的值。

### <a name="how-to-rejoin-original-fields"></a>如何重新加入原始欄位
樞紐轉換只會投出使用在彙總、分組和中樞動作中的資料行。 如果您想要在流程中包含上一個步驟中的其他資料行，請使用上一個步驟中的新分支，並使用自我聯結模式來連接流程與原始的中繼資料。

## <a name="next-steps"></a>後續步驟

嘗試[unpivot 轉換](data-flow-unpivot.md)，將資料行值轉換成資料列值。 
