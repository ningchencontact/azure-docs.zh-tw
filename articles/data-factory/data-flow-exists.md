---
title: Azure Data Factory 對應資料流程存在轉換
description: 如何使用具有 Exists 轉換的 data factory 對應資料流程來檢查現有的資料列
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8b488a079b2da1bcf0dd064025ed251a1dc25213
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029398"
---
# <a name="mapping-data-flow-exists-transformation"></a>對應資料流程存在轉換



存在轉換是能停止或允許資料中的資料列流經的資料列篩選轉換。 存在轉換類似於 ```SQL WHERE EXISTS``` 和 ```SQL WHERE NOT EXISTS```。 在 Exists 轉換之後，來自您資料流程的結果資料列將會包含來源1中的資料行值存在於來源2中，或不存在於來源2中的所有資料列。

![存在設定](media/data-flow/exists.png "存在 1")

為您的「存在」選擇第二個來源，使資料流程可以比較來自資料流 1 與資料流 2 的值。

從「來源 1」和「來源 2」選取您想要針對 [存在] 或 [不存在] 檢查其值的資料行。

## <a name="multiple-exists-conditions"></a>多個存在條件

在資料行條件中的每個資料列旁，當您將滑鼠游標移至 [觸達資料列] 時，您會發現可以使用 + 符號。 這可讓您針對 Exists 條件加入多個資料列。 每個額外的條件都是 "And"。

## <a name="custom-expression"></a>自訂表格達式

![存在]自(media/data-flow/exists1.png "定義")的自訂設定

您可以按一下 [自訂表格達式]，改為建立自由形式的運算式做為現有或不存在的條件。 核取此方塊可讓您輸入自己的運算式做為條件。

## <a name="next-steps"></a>後續步驟

類似的轉換是[查閱](data-flow-lookup.md)和[聯結](data-flow-join.md)。
