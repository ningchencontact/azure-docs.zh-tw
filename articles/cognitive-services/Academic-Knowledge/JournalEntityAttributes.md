---
title: 期刊實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在認知服務的學術知識 API 中搭配期刊實體使用的屬性。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 45b2860b56e79c197d96998350131a142326157a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902802"
---
# <a name="journal-entity"></a>期刊實體

<sub> *下列屬性專屬於期刊實體。(Ty = '2') </sub>

名稱    |說明                            |類型       | 作業
------- | ------------------------------------- | --------- | ----------------------------
id      |實體識別碼                              |Int64      |Equals
DJN     |期刊標準化名稱                |字串     |None
JN      |期刊顯示名稱                   |字串     |Equals
CC      |期刊引用總數           |Int32      |None  
ECC     |期刊引用預估總數 |Int32      |None