---
title: 研究領域實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配研究領域實體使用的屬性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878947"
---
# <a name="field-of-study-entity"></a>研究領域實體

<sub> *下列屬性專屬於研究領域實體。(Ty = '6') </sub>

Name    |說明                            |類型       | 作業
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
