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
ms.openlocfilehash: 485775660ecfdf2291365ab98c9188295ea2cbde
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183487"
---
# <a name="entity-attributes"></a>實體屬性

學術圖表是由 7 個實體類型組成。 所有的實體會將實體識別碼 」 及 「 實體類型。

## <a name="common-entity-attributes"></a>常見實體屬性
名稱    |描述                |類型       | 作業
------- | ------------------------- | --------- | ----------------------------
id      |實體識別碼                  |Int64      |Equals
Ty      |實體類型                |列舉   |Equals

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

