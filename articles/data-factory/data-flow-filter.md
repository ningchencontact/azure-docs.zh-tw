---
title: Azure Data Factory 對應資料流程篩選條件轉換
description: Azure Data Factory 對應資料流程篩選條件轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b7e7b123560aae3a2d3086c8536969297d31f7ba
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270968"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Azure Data Factory 對應資料流程篩選條件轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

篩選條件轉換會提供資料列篩選。 建置一個定義篩選條件的運算式。 在文字方塊中按一下，以啟動運算式建立器。 在運算式建立器中，建置一個篩選條件運算式，以控制允許目前資料流中的哪些資料列可以通過 (篩選) 到下一步的轉換。

也就是 loan_status 資料行的篩選條件：

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

影片示範中 [年] 資料行的篩選條件：

```
year > 1980
```
