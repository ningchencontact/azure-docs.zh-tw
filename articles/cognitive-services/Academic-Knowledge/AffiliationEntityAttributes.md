---
title: 學術知識 API 中的服務機關實體屬性
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配服務機關實體使用的屬性。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 344b26b16f74cd44982e3c93fa69295792daa9a0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190618"
---
# <a name="affiliation-entity"></a>服務機關實體

<sub> *下列屬性專屬於服務機關實體。(Ty = '5') </sub>

Name    |說明                            |類型       | 作業
------- | ------------------------------------- | --------- | ----------------------------
id      |實體識別碼                              |Int64      |Equals
AfN     |服務機關標準化名稱        |字串     |Equals
DAfN    |服務機關顯示名稱       |字串     |None
CC      |服務機關引用總數           |Int32      |None  
ECC     |服務機關引用預估總數 |Int32      |None

## <a name="extended-metadata-attributes"></a>擴充中繼資料屬性 ##

Name    | 說明               
--------|---------------------------    
PC      |服務機關的論文計數
