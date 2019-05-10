---
title: Azure Data Factory 對應資料流程存在的轉換
description: 如何檢查現有資料列，使用 data factory 的對應資料流使用 Exists 轉換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b98b7afb21f2f50d44ba93ed793b6efb20f75164
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235977"
---
# <a name="mapping-data-flow-exists-transformation"></a>對應資料流程存在轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

存在轉換是能停止或允許資料中的資料列流經的資料列篩選轉換。 存在轉換類似於 ```SQL WHERE EXISTS``` 和 ```SQL WHERE NOT EXISTS```。 有轉換之後，從您的資料流產生的資料列會包含來自來源 1 資料行值所在來源 2 中的所有資料列或不存在於來源 2。

![存在設定](media/data-flow/exists.png "存在 1")

為您的「存在」選擇第二個來源，使資料流程可以比較來自資料流 1 與資料流 2 的值。

從「來源 1」和「來源 2」選取您想要針對 [存在] 或 [不存在] 檢查其值的資料行。

## <a name="multiple-exists-conditions"></a>多個存在條件

旁邊的 Exists 在資料行條件中每個資料列，您會發現 + 符號可用時暫留於達到資料列。 這可讓您將新增多個資料列，Exists 條件。 每個額外的條件是"And"。

## <a name="custom-expression"></a>自訂運算式

![存在自訂的設定](media/data-flow/exists1.png "存在自訂")

您可以按一下 [自訂運算式] 改為建立自由格式運算式，做為您存在或不存在的條件。 核取此方塊，可讓您輸入條件運算式中。

## <a name="next-steps"></a>後續步驟

類似的轉換[查閱](data-flow-lookup.md)並[聯結](data-flow-join.md)。
