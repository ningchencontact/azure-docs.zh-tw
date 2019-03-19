---
title: Azure Data Factory 對應資料流程存在轉換
description: Azure Data Factory 對應資料流程存在轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 18d8a0e231e8b4dbe33911dd6267966674366904
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734484"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Azure Data Factory 對應資料流程存在轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

存在轉換是能停止或允許資料中的資料列流經的資料列篩選轉換。 存在轉換類似於 ```SQL WHERE EXISTS``` 和 ```SQL WHERE NOT EXISTS```。 在篩選轉換之後，來自您資料流的結果資料列將會包含來自「來源 1」的資料行值同時也存在於「來源 2」的所有資料列，或是那些資料行值不存在於「來源 2」的所有資料列。

![存在設定](media/data-flow/exsits.png "存在 1")

為您的「存在」選擇第二個來源，使資料流程可以比較來自資料流 1 與資料流 2 的值。

從「來源 1」和「來源 2」選取您想要針對 [存在] 或 [不存在] 檢查其值的資料行。
