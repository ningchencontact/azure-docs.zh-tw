---
title: Azure Data Factory 對應資料流程篩選條件轉換
description: Azure Data Factory 對應資料流程篩選條件轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: e0b41850c149ff7095333cf77b780dec1f03b882
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234421"
---
# <a name="azure-data-factory-filter-transformation"></a>Azure 資料處理站的篩選條件轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

篩選條件轉換會提供資料列篩選。 建置一個定義篩選條件的運算式。 在文字方塊中按一下，以啟動運算式建立器。 在運算式建立器中，建置一個篩選條件運算式，以控制允許目前資料流中的哪些資料列可以通過 (篩選) 到下一步的轉換。 將 SQL 陳述式的 WHERE 子句篩選轉換。

## <a name="filter-on-loanstatus-column"></a>篩選 loan_status 資料行：

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

影片示範中 [年] 資料行的篩選條件：

```
year > 1980
```

## <a name="next-steps"></a>後續步驟

請嘗試篩選轉換的資料行[選取轉換](data-flow-select.md)
