---
title: Azure Data Factory 對應資料流程篩選準則轉換
description: Azure Data Factory 對應資料流程篩選準則轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 2afe079c346a15ec212664ce022ac5e2926b12d4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387816"
---
# <a name="azure-data-factory-filter-transformation"></a>Azure data factory 篩選器轉換



篩選條件轉換會提供資料列篩選。 建置一個定義篩選條件的運算式。 在文字方塊中按一下，以啟動運算式建立器。 在運算式建立器中，建置一個篩選條件運算式，以控制允許目前資料流中的哪些資料列可以通過 (篩選) 到下一步的轉換。 請將篩選準則轉換視為 SQL 語句的 WHERE 子句。

## <a name="filter-on-loan_status-column"></a>篩選 loan_status 資料行：

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

影片示範中 [年] 資料行的篩選條件：

```
year > 1980
```

## <a name="next-steps"></a>後續步驟

嘗試「資料行篩選」轉換，也就是「[選取」轉換](data-flow-select.md)
