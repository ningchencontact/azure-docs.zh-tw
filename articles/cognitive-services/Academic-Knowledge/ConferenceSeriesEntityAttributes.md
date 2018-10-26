---
title: 會議系列實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以搭配會議系列實體使用的屬性。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f9f28afd7005d7a61aa0d2f4dba69ca598034b52
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900743"
---
# <a name="conference-series-entity"></a>會議系列實體

<sub> *下列屬性專屬於會議系列實體。(Ty = '3') </sub>

名稱    |說明                            |類型       | 作業
------- | ------------------------------------- | --------- | ----------------------------
id      |實體識別碼                              |Int64      |Equals
CN      |會議系列標準化名稱      |字串     |Equals
DCN     |會議系列顯示名稱         |字串     |None
CC      |會議系列引用總數         |Int32      |None  
ECC     |會議系列引用預估總數   |Int32      |None
F.FId   |與會議系列相關聯的研究領域實體識別碼 |Int64  | Equals
F.FN    |與會議系列相關聯的研究領域名稱  | Equals,<br/>StartsWith