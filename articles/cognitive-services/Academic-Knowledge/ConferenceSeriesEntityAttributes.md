---
title: 會議系列實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以搭配會議系列實體使用的屬性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 38b4aa4c899668a68041f042ce6981ddd8c58219
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340191"
---
# <a name="conference-series-entity"></a>會議系列實體

<sub> *下列屬性專屬於會議系列實體。(Ty = '3') </sub>

名稱    |描述                            |類型       | 作業
------- | ------------------------------------- | --------- | ----------------------------
Id      |實體識別碼                              |Int64      |Equals
CN      |會議系列標準化名稱      |String     |Equals
DCN     |會議系列顯示名稱         |String     |None
CC      |會議系列引用總數         |Int32      |None  
ECC     |會議系列引用預估總數   |Int32      |None
F.FId   |與會議系列相關聯的研究領域實體識別碼 |Int64  | Equals
F.FN    |與會議系列相關聯的研究領域名稱  | Equals,<br/>StartsWith
