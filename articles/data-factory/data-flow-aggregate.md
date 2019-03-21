---
title: Azure Data Factory 對應資料流程彙總轉換
description: Azure Data Factory 資料流程彙總轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 7b488b243c0520befb6b5470598f460b5a759fed
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730031"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Azure Data Factory 對應資料流程彙總轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

彙總轉換可供您定義資料流資料行的彙總。 在運算式產生器中，您可以定義不同類型的彙總 (也就是 SUM、MIN、MAX、COUNT 等等)，並在輸出中建立新的欄位來包含這些彙總與選擇性的群組依據欄位。

![彙總轉換選項](media/data-flow/agg.png "彙總 1")

## <a name="group-by"></a>分組依據
(選擇性) 選擇您的彙總群組依據子句，並使用現有資料行的名稱，或使用新的名稱。 使用「新增資料行」新增更多的群組依據子句，然後按一下啟動運算式產生器的資料行名稱旁的文字方塊，僅選取現有資料行、資料行的組合或群組的運算式。

## <a name="the-aggregate-column-tab"></a>「彙總資料行」索引標籤 
(必要) 選擇「彙總資料行」索引標籤來建立彙總運算式。 您可以選擇要使用的彙總來覆寫現有的資料行，或使用彙總的新名稱建立新的欄位。 在資料行名稱選取器旁邊的右側方塊中，會輸入您想要對於彙總使用的運算式。 按一下該文字方塊會開啟運算式產生器。

![彙總轉換選項](media/data-flow/agg2.png "彙總工具")

## <a name="data-preview-in-expression-builder"></a>在運算式產生器中的資料預覽

在偵錯模式中，運算式產生器無法使用彙總函式產生資料預覽。 若要檢視彙總轉換的資料預覽，請關閉運算式產生器，然後從資料流程設計工具中檢視資料設定檔。
