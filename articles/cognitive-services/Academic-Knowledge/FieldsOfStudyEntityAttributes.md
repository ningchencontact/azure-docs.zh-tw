---
title: 研究領域實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配研究領域實體使用的屬性。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 862fd6d506d5f1ca6f7f532f80f53a29200f33db
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900422"
---
# <a name="field-of-study-entity"></a>研究領域實體

<sub> *下列屬性專屬於研究領域實體。(Ty = '6') </sub>

名稱    |說明                            |類型       | 作業
------- | ------------------------------------- | --------- | ----------------------------
id      |實體識別碼                              |Int64      |Equals
FN      |研究領域標準化名稱         |字串     |Equals
DFN     |研究領域顯示名稱            |字串     |None
CC      |研究領域引用總數    |Int32      |None  
ECC     |領域引用預估總數|Int32      |None
FL      |研究領域階層中的層級     |Int32      |Equals, <br/>IsBetween
FP.FN   |父代研究領域名稱             |字串     |Equals
FP.FId  |父代研究領域識別碼               |Int64      |Equals
FC.FN   |子系研究領域名稱              |字串     |Equals
FC.FId  |子系研究領域識別碼                |Int64      |Equals