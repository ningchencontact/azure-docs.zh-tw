---
title: 對應資料流程中的匯總轉換-Azure Data Factory |Microsoft Docs
description: 瞭解如何使用對應資料流程匯總轉換, 在 Azure Data Factory 中大規模匯總資料。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 778aefc05a9b12648e60d752a3c281cb18323125
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314238"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>對應資料流程中的匯總轉換 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

彙總轉換可供您定義資料流資料行的彙總。 使用運算式產生器, 您可以定義不同類型的匯總, 例如 SUM、MIN、MAX 和 COUNT, 其可依現有或計算資料行分組。

## <a name="group-by"></a>分組依據
選取現有的資料行, 或建立新的計算資料行, 作為匯總的 group by 子句使用。 若要使用現有的資料行, 請從下拉式清單中選取所需的資料行。 若要建立新的計算資料行, 請將滑鼠停留在子句上, 然後按一下 [計算資料行]。 這會開啟[資料流程運算式](concepts-data-flow-expression-builder.md)產生器。 建立計算資料行之後, 請在 [名稱為] 欄位底下輸入輸出資料行名稱。 如果您想要加入額外的 group by 子句, 請將滑鼠停留在現有子句上, 然後按一下 [+]。

![依設定匯總轉換群組](media/data-flow/agg.png "依設定匯總轉換群組")

> [!NOTE]
> Group by 子句在匯總轉換中是選擇性的。

## <a name="aggregate-column"></a>匯總資料行 
選擇 [匯總] 索引標籤來建立匯總運算式。 您可以選擇現有的資料行, 並使用匯總來覆寫值, 或使用新的名稱來建立新的欄位。 匯總運算式會在資料行名稱選取器旁的右側方塊中輸入。 若要編輯運算式, 請按一下文字方塊來開啟 [運算式產生器]。 若要加入額外的匯總, 請將滑鼠停留在現有的運算式上, 然後按一下 [+] 以建立新的匯總資料行或資料[行模式](concepts-data-flow-column-pattern.md)。

![匯總轉換匯總設定](media/data-flow/agg2.png "匯總轉換匯總設定")

> [!NOTE]
> 每個匯總運算式都必須包含至少一個彙總函式。

> [!NOTE]
> 在 [調試] 模式中, [運算式產生器] 無法使用彙總函式來產生資料預覽。 若要查看匯總轉換的資料預覽, 請關閉 [運算式產生器], 並透過 [資料預覽] 索引標籤來查看資料。

## <a name="reconnect-rows-and-columns"></a>重新連接資料列和資料行
匯總轉換十分等同于 SQL 匯總選取查詢。 不包含在 Group By 子句或彙總函式中的資料行, 將不會流經匯總轉換的輸出。 如果您想要在匯總的資料列輸出中包含其他資料行, 您必須執行下列其中一項動作:

* 使用彙總函式包含該額外的資料行, 例如 Last () 或 First ()
* 使用[自我聯結模式](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)重新聯結匯總之前的資料行。

## <a name="next-steps"></a>後續步驟

* 使用[視窗轉換](data-flow-window.md)來定義以視窗為基礎的匯總
