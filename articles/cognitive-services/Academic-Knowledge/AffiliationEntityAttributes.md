---
title: 學術知識 API 中的服務機關實體屬性
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配服務機關實體使用的屬性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 333875472d9b859196c4d828061301b9d32c4d5a
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705102"
---
# <a name="affiliation-entity"></a>服務機關實體

<sub> *下列屬性專屬於服務機關實體。(Ty = '5') </sub>

名稱    |描述                            |Type       | 作業
------- | ------------------------------------- | --------- | ----------------------------
Id      |實體 ID                              |Int64      |等於
AfN     |服務機關標準化名稱        |String     |等於
DAfN    |服務機關顯示名稱       |String     |無
CC      |服務機關引用總數           |Int32      |無  
ECC     |服務機關引用預估總數 |Int32      |無

## <a name="extended-metadata-attributes"></a>擴充中繼資料屬性 ##

名稱    | 描述               
--------|---------------------------    
電腦      |服務機關的論文計數
