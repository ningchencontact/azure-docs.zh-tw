---
title: Azure Data Factory 資料流 Surrogate 索引鍵轉換
description: Azure Data Factory 資料流 Surrogate 索引鍵轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6243905857f0450168541f556636d90bb4d855f7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734943"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Azure Data Factory 資料流 Surrogate 索引鍵轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用 Surrogate 索引鍵轉換，將遞增的非商務任意索引鍵值新增至您的資料流資料列集。 這在設計星型結構描述分析資料模型的維度資料表時非常有用，您維度資料表中的每個成員都需要有唯一的非商務索引鍵 (Kimball DW 方法論的一部分)。

![Surrogate 索引鍵轉換](media/data-flow/surrogate.png "Surrogate 索引鍵轉換")

[索引鍵資料行] 是您要為新 Surrogate 索引鍵資料行取的名稱。

[開始值] 是遞增值的開始點。
