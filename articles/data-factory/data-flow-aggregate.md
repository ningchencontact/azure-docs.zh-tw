---
title: 彙總轉換中對應的資料流量-Azure Data Factory |Microsoft Docs
description: 了解如何在 Azure Data Factory 中的小數位數，與對應的資料流動彙總 」 轉換的資料彙總。
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 21135b26d4bc840b3fcb091e675e5e6bd24d8548
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312133"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>對應資料流程中的彙總 」 轉換 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

彙總轉換可供您定義資料流資料行的彙總。 您可以使用運算式產生器，來定義不同類型的彙總，例如 SUM、 MIN、 MAX 和 COUNT 可以依現有或計算資料行來分組。

## <a name="group-by"></a>群組依據
選取現有的資料行，或建立新的計算資料行，以作為一組子句所您彙總。 若要使用現有的資料行，請從下拉式清單中選取所需的資料行。 若要建立新的計算資料行，將滑鼠移至 over 子句，按一下 「 計算資料行 」。 這會開啟[Data Flow 運算式產生器](concepts-data-flow-expression-builder.md)。 一旦您建立您的計算資料行，請輸入 '名稱為' 欄位下方的輸出資料行名稱。 如果您想要將其他群組加入子句，將滑鼠停留在現有子句，然後按一下 '+'。

![彙總 」 轉換所設定的群組](media/data-flow/agg.png "彙總 」 轉換所設定的群組")

> [!NOTE]
> Group by 子句是選擇性在彙總 」 轉換。

## <a name="aggregate-column"></a>彙總資料行 
選擇 '彙總' 索引標籤，來建立彙總運算式。 您可以選擇現有的資料行與彙總，來覆寫值中，或使用新名稱建立新的欄位中。 在右邊的方塊旁邊的資料行名稱選取器中輸入彙總運算式。 若要編輯運算式，請按一下 [運算式產生器開啟] 文字方塊。 加入額外的彙總，將滑鼠停在現有的運算式，然後按一下 [+] 以建立新的彙總資料行或[資料行模式](concepts-data-flow-column-pattern.md)。

![彙總轉換的彙總設定](media/data-flow/agg2.png "彙總轉換的彙總設定")

> [!NOTE]
> 每個彙總運算式必須包含至少一個彙總函式。

> [!NOTE]
> 在偵錯模式中，運算式產生器無法產生具有彙總函式的資料預覽。 若要檢視彙總 」 轉換的資料預覽，請關閉運算式產生器，檢視透過 「 資料預覽 」 索引標籤的資料。

## <a name="next-steps"></a>後續步驟

定義視窗型彙總使用[視窗轉換](data-flow-window.md)