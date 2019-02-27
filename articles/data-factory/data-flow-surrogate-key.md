---
title: Azure Data Factory 資料流 Surrogate 索引鍵轉換
description: Azure Data Factory 資料流 Surrogate 索引鍵轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 62e9879f6bc75f042669ecb931ca7459d1317cc7
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271016"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Azure Data Factory 資料流 Surrogate 索引鍵轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用 Surrogate 索引鍵轉換，將遞增的非商務任意索引鍵值新增至您的資料流資料列集。 這在設計星型結構描述分析資料模型的維度資料表時非常有用，您維度資料表中的每個成員都需要有唯一的非商務索引鍵 (Kimball DW 方法論的一部分)。

![Surrogate 索引鍵轉換](media/data-flow/surrogate.png "Surrogate 索引鍵轉換")

[索引鍵資料行] 是您要為新 Surrogate 索引鍵資料行取的名稱。

[開始值] 是遞增值的開始點。
