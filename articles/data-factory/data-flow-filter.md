---
title: Azure Data Factory 對應資料流程篩選條件轉換
description: Azure Data Factory 對應資料流程篩選條件轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d1751c47ad4507260d9f8d6ea44fcb32ed0e7338
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57864723"
---
# <a name="azure-data-factoryfilter-transformation"></a>Azure 資料 FactoryFilter 轉換

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
