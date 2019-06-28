---
title: 衍生資料行 」 轉換中對應的資料流量-Azure Data Factory |Microsoft Docs
description: 了解如何在 Azure Data Factory 中對應資料流程衍生的資料行轉換的大規模轉換資料。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 941c629fd8359edc7fc1cf364a6735314044d95e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312216"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>對應資料流程中的衍生資料行轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用「衍生的資料行」轉換可在資料流程中產生新的資料行或修改現有欄位。

## <a name="derived-column-settings"></a>在衍生的資料行設定

若要覆寫現有的資料行，請透過資料行的下拉式清單中選取它。 否則，使用資料行選取項目欄位做為文字方塊並輸入新的資料行名稱。 若要建立衍生的資料行運算式，按一下 '輸入運算式' 方塊以開啟[Data Flow 運算式產生器](concepts-data-flow-expression-builder.md)。

![衍生資料行設定](media/data-flow/dc1.png "衍生資料行設定")

若要新增其他衍生的資料行，將滑鼠停在現有的衍生資料行，然後按一下 '+'。 然後，選擇 加入資料行' 或 '新增資料行模式'。 資料行模式可能派上用場如果您的資料行名稱是從您的來源的變數。 如需詳細資訊，請參閱 <<c0> [ 資料行模式](concepts-data-flow-column-pattern.md)。

![新增衍生資料行選取項目](media/data-flow/columnpattern.png "新增衍生資料行選取範圍")

## <a name="next-steps"></a>後續步驟

- 深入了解[對應資料流程運算式語言](data-flow-expression-functions.md)。
