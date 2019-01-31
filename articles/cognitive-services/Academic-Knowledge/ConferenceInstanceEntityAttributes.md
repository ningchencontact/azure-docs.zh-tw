---
title: 會議執行個體實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配會議執行個體實體使用的屬性。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: db025f377a3fab2f788252db0c8e3555837a6de8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196101"
---
# <a name="conference-instance-entity"></a>會議執行個體實體

<sub> *下列屬性專屬於會議執行個體實體。(Ty = '4') </sub>

Name    |說明                            |類型       | 作業
------- | ------------------------------------- | --------- | ----------------------------
id      |實體識別碼                              |Int64      |Equals
CIN     |會議執行個體標準化名稱 ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |字串     |Equals
DCN     |會議執行個體顯示名稱 ({ConferenceSeriesName} : {ConferenceInstanceYear})       |字串     |None
CIL     |會議執行個體的位置    |字串     |Equals,<br/>StartsWith
CISD    |會議執行個體的開始日期  |日期       |Equals,<br/>IsBetween
CIED    |會議執行個體的結束日期    |日期       |Equals,<br/>IsBetween
CIARD   |擷取會議執行個體的註冊到期日  |日期       |Equals,<br/>IsBetween
CISDD   |會議執行個體的提交到期日     |日期       |Equals,<br/>IsBetween
CIFVD   |會議執行個體的最終版本到期日  |日期       |Equals,<br/>IsBetween
CINDD   |會議執行個體的通知日期   |日期       |Equals,<br/>IsBetween
CD.T    |會議執行個體事件的標題   |日期       |Equals,<br/>IsBetween
CD.D    |會議執行個體事件的日期    |日期       |Equals,<br/>IsBetween
PCS.CN  |執行個體的會議系列名稱 |字串     |Equals
PCS.CId |執行個體的會議系列識別碼 |Int64    |Equals
CC      |會議執行個體引用總數           |Int32      |None  
ECC     |會議執行個體預估引用總數 |Int32      |None


## <a name="extended-metadata-attributes"></a>擴充中繼資料屬性 ##

Name    | 說明               
--------|---------------------------    
FN      | 會議執行個體全名
