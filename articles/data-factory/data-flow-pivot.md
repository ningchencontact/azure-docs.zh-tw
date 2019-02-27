---
title: Azure Data Factory 對應資料流程樞紐轉換
description: Azure Data Factory 對應資料流程樞紐轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 86404f4eb2eb2de4243c6bb725f4292e7b560d18
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271017"
---
# <a name="azure-data-factory-mapping-data-flow-pivot-transformation"></a>Azure Data Factory 對應資料流程樞紐轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用 ADF 資料流程中的樞紐做為彙總，其中一個或多個群組資料行都有個別的資料行值會轉換成相異資料列。 基本上，您可以將資料列值樞紐處理為新的資料行 (將資料轉換成中繼資料)。

![樞紐選項](media/data-flow/pivot1.png "樞紐 1")

## <a name="group-by"></a>分組依據

![樞紐選項](media/data-flow/pivot2.png "樞紐 2")

首先，設定您想要對於樞紐彙總分組所依據的資料行。 您可以在這裡使用資料行清單旁邊的 + 號設定 1 個以上資料行。

## <a name="pivot-key"></a>樞紐索引鍵

![樞紐選項](media/data-flow/pivot3.png "樞紐 3")

樞紐索引鍵是 ADF 從資料列樞紐處理為資料行的資料行。 根據預設，此欄位的資料集之內的每個唯一值都會樞紐處理為資料行。 不過，您可以選擇從資料集輸入您想要樞紐處理為資料行值的數值。

## <a name="pivoted-columns"></a>樞紐處理過的資料行

![樞紐選項](media/data-flow/pivot4.png "樞紐 4")

最後，選擇您想要對已樞紐的值所使用的彙總，以及您要如何在轉換的新輸出投影中顯示資料行。

(選擇性) 您可以設定對於每個新資料行名稱從資料列新增前置詞、中置詞及後置詞的命名模式。

例如，依「區域」樞紐處理「銷售」會從每個銷售值產生新的資料行值，也就是"25"、"50"、"1000" 等等。不過，如果您設定「銷售」的前置詞值。 

![樞紐選項](media/data-flow/pivot5.png "樞紐 5")

設定為「一般」資料行的排列方式，會使用彙總值將已樞紐的資料行分組在一起。 將資料行排列方式設定為「橫向」會在資料行和值之間交替。

### <a name="aggregation"></a>彙總

若要對於樞紐值設定您想要使用的彙總，請按一下「已樞紐的資料行」窗格底部的欄位。 您會進入 ADF 資料流程運算式產生器，您可以在其中建立彙總運算式，並對於新的彙總值提供描述性的別名名稱。

使用 ADF 資料流程運算式語言在運算式產生器描述已樞紐的資料行轉換： https://aka.ms/dataflowexpressions。

### <a name="how-to-rejoin-original-fields"></a>如何重新加入原始欄位
> [!NOTE]
> 樞紐轉換只會投出使用在彙總、分組和中樞動作中的資料行。 如果您想要納入流程上一個步驟的其他資料行，請從上一個步驟使用「新增分支」，並使用自我聯結模式來連接該流程和原始中繼資料
