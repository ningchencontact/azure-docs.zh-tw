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
ROBOTS: NOINDEX
ms.openlocfilehash: feed324202f6a75ceb7e9089875b899c51cd8ae6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705034"
---
# <a name="conference-series-entity"></a>會議系列實體

<sub> *下列屬性專屬於會議系列實體。(Ty = '3') </sub>

名稱    |描述                            |Type       | 作業
------- | ------------------------------------- | --------- | ----------------------------
Id      |實體 ID                              |Int64      |等於
CN      |會議系列標準化名稱      |String     |等於
DCN     |會議系列顯示名稱         |String     |無
CC      |會議系列引用總數         |Int32      |無  
ECC     |會議系列引用預估總數   |Int32      |無
F.FId   |與會議系列相關聯的研究領域實體識別碼 |Int64  | 等於
F.FN    |與會議系列相關聯的研究領域名稱  | Equals,<br/>StartsWith
