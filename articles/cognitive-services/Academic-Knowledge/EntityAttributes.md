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
ms.openlocfilehash: accc2803895f3892075cdd9877ca98344ab88bd1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884812"
---
# <a name="entity-attributes"></a>實體屬性

學術圖表是由 7 個實體類型組成。 所有實體都有實體識別碼和實體類型。

## <a name="common-entity-attributes"></a>常見實體屬性
Name    |說明                |類型       | 作業
------- | ------------------------- | --------- | ----------------------------
id      |實體識別碼                  |Int64      |Equals
Ty      |實體類型                |列舉   |Equals

## <a name="entity-type-enum"></a>實體類型列舉
Name                                                            |value
----------------------------------------------------------------|-----
[文件](PaperEntityAttributes.md)                               |0
[作者](AuthorEntityAttributes.md)                             |1
[日誌](JournalEntityAttributes.md)                           |2
[會議系列](JournalEntityAttributes.md)                 |3
[會議執行個體](ConferenceInstanceEntityAttributes.md)    |4
[聯盟](AffiliationEntityAttributes.md)                   |5
[研究領域](FieldsOfStudyEntityAttributes.md)                      |6

