---
author: brjohnstmsft
ms.service: search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: b4d0bc73e652a1cd6ef59589318b92f63727c7f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079635"
---
| 数据类型 | 允許在 lambda 運算式中使用的功能 `any` | 允許在 lambda 運算式中使用的功能 `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | 以外的所有內容`search.ismatch`和 `search.ismatchscoring` | 相同 |
| `Collection(Edm.String)` | 比較`eq`或 `search.in` <br/><br/> 結合使用的子運算式 `or` | 比較`ne`或 `not search.in()` <br/><br/> 結合使用的子運算式 `and` |
| `Collection(Edm.Boolean)` | 比較`eq`或 `ne` | 相同 |
| `Collection(Edm.GeographyPoint)` | 使用`geo.distance`與`lt`或 `le` <br/><br/> `geo.intersects` <br/><br/> 結合使用的子運算式 `or` | 使用`geo.distance`與`gt`或 `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> 結合使用的子運算式 `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | 使用比較`eq`， `ne`， `lt`， `gt`， `le`，或 `ge` <br/><br/> 結合使用其他子運算式的比較 `or` <br/><br/> 結合除了比較`ne`與使用其他子運算式 `and` <br/><br/> 運算式使用的組合`and`並`or`在[Disjunctive Normal Form （） 來表示](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | 使用比較`eq`， `ne`， `lt`， `gt`， `le`，或 `ge` <br/><br/> 結合使用其他子運算式的比較 `and` <br/><br/> 結合除了比較`eq`與使用其他子運算式 `or` <br/><br/> 運算式使用的組合`and`並`or`在[連結一般表單 (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
