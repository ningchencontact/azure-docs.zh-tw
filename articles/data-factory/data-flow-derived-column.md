---
title: 對應資料流程中的衍生資料行轉換-Azure Data Factory |Microsoft Docs
description: 瞭解如何使用對應的資料流程衍生的資料行轉換，在 Azure Data Factory 中大規模轉換資料。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aacd6f1799f1813e168bd04e78f18cf60ad5243f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026842"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>對應資料流程中的衍生資料行轉換

使用「衍生的資料行」轉換可在資料流程中產生新的資料行或修改現有欄位。

## <a name="derived-column-settings"></a>衍生的資料行設定

若要覆寫現有的資料行，請透過 [資料行] 下拉式清單加以選取。 否則，請使用 [資料行選取] 欄位作為文字方塊，然後輸入新資料行的名稱。 若要建立衍生資料行的運算式，請按一下 [輸入運算式] 方塊，以開啟 [資料流程[運算式](concepts-data-flow-expression-builder.md)產生器]。

![衍生的資料行設定](media/data-flow/dc1.png "衍生的資料行設定")

若要加入其他衍生的資料行，請將滑鼠停留在現有的衍生資料行上，然後按一下 [+]。 然後，選擇 [新增資料行] 或 [新增資料行模式]。 如果您的資料行名稱是來源的變數，則資料行模式可能會很有用。 如需詳細資訊，請參閱資料[行模式](concepts-data-flow-column-pattern.md)。

新增衍生的資料![行選取](media/data-flow/columnpattern.png "新的衍生資料行選取範圍")

## <a name="next-steps"></a>後續步驟

- 深入瞭解對應的[資料流程運算式語言](data-flow-expression-functions.md)。
