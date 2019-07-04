---
title: 作者實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配作者實體使用的屬性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d4b33c06ab023023aadf403cf0ef0b08c2bafc5f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609750"
---
# <a name="author-entity"></a>作者實體
<sub> *下列屬性專屬於作者實體。(Ty = '1') </sub>

名稱    |描述                            |類型       | 作業
------- | ------------------------------------- | --------- | ----------------------------
Id      |實體識別碼                              |Int64      |Equals
AuN     |作者標準化名稱                 |String     |Equals
DAuN    |作者顯示名稱                    |String     |None
CC      |作者引用總數            |Int32      |None  
ECC     |作者引用預估總數  |Int32      |None
E       |擴充中繼資料 (請參閱「擴充中繼屬性」表格)  |String     |None  


## <a name="extended-metadata-attributes"></a>擴充中繼資料屬性 ##

名稱    | 描述               
--------|---------------------------    
LKA.Afn     | 與作者相關聯的服務機關顯示名稱  
LKA.AfId        | 與作者相關聯的服務機關實體識別碼
