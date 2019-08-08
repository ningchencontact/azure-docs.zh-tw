---
title: 會議執行個體實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配會議執行個體實體使用的屬性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 37a353fbb86ca199b2316dcfba5904f4b46b0276
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705066"
---
# <a name="conference-instance-entity"></a>會議執行個體實體

<sub> *下列屬性專屬於會議執行個體實體。(Ty = '4') </sub>

名稱    |描述                            |Type       | 作業
------- | ------------------------------------- | --------- | ----------------------------
Id      |實體 ID                              |Int64      |等於
CIN     |會議執行個體標準化名稱 ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |String     |等於
DCN     |會議執行個體顯示名稱 ({ConferenceSeriesName} : {ConferenceInstanceYear})       |String     |無
CIL     |會議執行個體的位置    |String     |Equals,<br/>StartsWith
CISD    |會議執行個體的開始日期  |Date       |Equals,<br/>IsBetween
CIED    |會議執行個體的結束日期    |Date       |Equals,<br/>IsBetween
CIARD   |擷取會議執行個體的註冊到期日  |Date       |Equals,<br/>IsBetween
CISDD   |會議執行個體的提交到期日     |Date       |Equals,<br/>IsBetween
CIFVD   |會議執行個體的最終版本到期日  |Date       |Equals,<br/>IsBetween
CINDD   |會議執行個體的通知日期   |Date       |Equals,<br/>IsBetween
CD.T    |會議執行個體事件的標題   |Date       |Equals,<br/>IsBetween
CD.D    |會議執行個體事件的日期    |Date       |Equals,<br/>IsBetween
PCS.CN  |執行個體的會議系列名稱 |String     |等於
PCS.CId |執行個體的會議系列識別碼 |Int64    |等於
CC      |會議執行個體引用總數           |Int32      |無  
ECC     |會議執行個體預估引用總數 |Int32      |無


## <a name="extended-metadata-attributes"></a>擴充中繼資料屬性 ##

名稱    | 描述               
--------|---------------------------    
FN      | 會議執行個體全名
