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
ROBOTS: NOINDEX
ms.openlocfilehash: d54f3c72462e6702b09068092b7c18ea50f12048
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704985"
---
# <a name="field-of-study-entity"></a>研究領域實體

<sub> *下列屬性專屬於研究領域實體。(Ty = '6') </sub>

名稱    |描述                            |Type       | 作業
------- | ------------------------------------- | --------- | ----------------------------
Id      |實體 ID                              |Int64      |等於
FN      |研究領域標準化名稱         |String     |等於
DFN     |研究領域顯示名稱            |String     |無
CC      |研究領域引用總數    |Int32      |無  
ECC     |領域引用預估總數|Int32      |無
FL      |研究領域階層中的層級     |Int32      |Equals, <br/>IsBetween
FP.FN   |父代研究領域名稱             |String     |等於
FP.FId  |父代研究領域識別碼               |Int64      |等於
FC.FN   |子系研究領域名稱              |String     |等於
FC.FId  |子系研究領域識別碼                |Int64      |等於
