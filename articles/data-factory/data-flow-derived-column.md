---
title: Azure Data Factory 將資料流對應衍生資料行轉換
description: 如何大規模轉換資料與 Azure Data Factory 對應資料流程衍生資料行轉換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 6568e5ebf356bb0e6b4ac8ff6059cd093f8da821
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917570"
---
# <a name="mapping-data-flow-derived-column-transformation"></a>對應資料流程衍生資料行轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用「衍生的資料行」轉換可在資料流程中產生新的資料行或修改現有欄位。

![衍生資料行](media/data-flow/dc1.png "衍生的資料行")

您可以在單一「衍生的資料行」轉換中執行多個衍生的資料行動作。 按一下 [新增資料行] 以在單一轉換步驟中轉換多個資料行。

在 [資料行] 欄位中，選取要使用新的衍生值覆寫現有的資料行，或按一下 [建立新資料行] 以使用衍生的新值來產生新的資料行。

[運算式] 文字方塊中將開啟運算式產生器，您可以使用運算式函式為衍生資料行建置運算式。

## <a name="column-patterns"></a>資料行模式

如果您的資料行名稱是從您的來源的變數，您可能想要建立衍生的資料行內的轉換使用而不使用名為資料行的資料行模式。 請參閱[結構描述漂移](concepts-data-flow-schema-drift.md)文章以取得詳細資料。

![資料行模式](media/data-flow/columnpattern.png "資料行模式")

## <a name="next-steps"></a>後續步驟

深入了解[Data Factory 進行轉換的運算式語言](https://aka.ms/dataflowexpressions)而[運算式產生器](concepts-data-flow-expression-builder.md)
