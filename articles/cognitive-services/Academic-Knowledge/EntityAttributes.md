---
title: 學術圖表實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配學術圖表使用的實體屬性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 596ce35a0d744691edd75d6071d4758691e0031b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705024"
---
# <a name="entity-attributes"></a>實體屬性

學術圖表是由 7 個實體類型組成。 所有實體都會有實體識別碼和實體類型。

## <a name="common-entity-attributes"></a>常見實體屬性
名稱    |描述                |Type       | 作業
------- | ------------------------- | --------- | ----------------------------
Id      |實體 ID                  |Int64      |等於
Ty      |實體類型                |列舉   |等於

## <a name="entity-type-enum"></a>實體類型列舉
名稱                                                            |value
----------------------------------------------------------------|-----
[文件](PaperEntityAttributes.md)                               |0
[作者](AuthorEntityAttributes.md)                             |1
[日誌](JournalEntityAttributes.md)                           |2
[會議系列](JournalEntityAttributes.md)                 |3
[會議執行個體](ConferenceInstanceEntityAttributes.md)    |4
[聯盟](AffiliationEntityAttributes.md)                   |5
[研究領域](FieldsOfStudyEntityAttributes.md)                      |6

