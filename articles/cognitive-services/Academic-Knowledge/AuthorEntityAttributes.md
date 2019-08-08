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
ROBOTS: NOINDEX
ms.openlocfilehash: e63e9d3f8f17a2473caf3d31b83e318ddb132b43
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705097"
---
# <a name="author-entity"></a>作者實體
<sub> *下列屬性專屬於作者實體。(Ty = '1') </sub>

名稱    |描述                            |Type       | 作業
------- | ------------------------------------- | --------- | ----------------------------
Id      |實體 ID                              |Int64      |等於
AuN     |作者標準化名稱                 |String     |等於
DAuN    |作者顯示名稱                    |String     |無
CC      |作者引用總數            |Int32      |無  
ECC     |作者引用預估總數  |Int32      |無
E       |擴充中繼資料 (請參閱「擴充中繼屬性」表格)  |String     |無  


## <a name="extended-metadata-attributes"></a>擴充中繼資料屬性 ##

名稱    | 描述               
--------|---------------------------    
LKA.Afn     | 與作者相關聯的服務機關顯示名稱  
LKA.AfId        | 與作者相關聯的服務機關實體識別碼
