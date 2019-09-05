---
title: 限制和界限 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 知識庫和服務的某些部分有中繼資料的限制。 請務必將您的知識庫保持在這些限制範圍內，以便進行測試及發佈。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c7b0dc39d2da403383f245b9ff3227734c58cbbe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193474"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker 知識庫的限制和界限

以下提供的 QnA Maker 限制是[Azure 搜尋服務定價層限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)和[QnA Maker 定價層限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)的組合。 您必須知道這兩組限制，以瞭解您可以為每個資源建立多少知識庫，以及每個知識庫可成長的大小。

## <a name="knowledge-bases"></a>知識庫

知識庫的最大數目是以[Azure 搜尋服務層限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)為基礎。

|**Azure 搜尋服務層** | **免費** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|允許的已發佈知識庫數目上限|2|14|49|199|199|2,999|

 比方說，如果您的層次有 15 個允許的索引，您可以發佈 14 個知識庫 (每個已發佈的知識庫 1 個索引)。 第十五個索引 (`testkb`) 用於撰寫和測試所有知識庫。 

## <a name="extraction-limits"></a>擷取限制

### <a name="maximum-number-of-files"></a>檔案數目上限

可以解壓縮的檔案數目上限，以及檔案大小上限是以您 **[QnA Maker 定價層限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** 為基礎。

### <a name="maximum-number-of-deep-links-from-url"></a>來自 URL 的深度連結數目上限

可以進行編目以從 URL 頁面解壓縮 Qna 的深層連結數目上限為**20**。

## <a name="metadata-limits"></a>中繼資料限制

### <a name="by-azure-search-pricing-tier"></a>依 Azure 搜尋服務定價層

每個知識庫的元資料欄位數目上限是以您的 **[Azure 搜尋服務層限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** 為基礎。

|**Azure 搜尋服務層** | **免費** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|每項 QnA Maker 服務的中繼資料欄位數上限 (橫跨所有的知識庫)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>依名稱和值

中繼資料名稱和值的長度和可接受字元會列在下表中。

|項目|允許的字元|Regex 模式比對|最大字元數|
|--|--|--|--|
|Name|可讓<br>英數位元（字母和數位）<br>`_`底線|`^[a-zA-Z0-9_]+$`|100|
|值|允許以外的所有專案<br>`:`加<br>`|`（垂直管道）|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>知識庫的內容限制
知識庫內容的整體限制：
* 解答文字的長度：25,000
* 問題文字的長度：1,000
* 中繼資料索引鍵/值文字的長度：100
* 中繼資料名稱的支援字元：字母、數位和`_`  
* 中繼資料值的支援字元：除了和`:`以外的所有`|` 
* 檔案名稱的長度：200
* 支援的檔案格式：".tsv"、".pdf"、".txt"、".docx"、".xlsx"。
* 替代問題數目上限：300
* 問答組數目上限：視選擇的 **[Azure 搜尋服務層](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** 而定。 問與答配對會對應到 Azure 搜尋服務索引上的檔。 
* URL/HTML 頁面：1000000個字元

## <a name="create-knowledge-base-call-limits"></a>建立知識庫呼叫限制：
這些代表每個建立知識庫動作的限制；也就是，按一下 [建立知識庫] 或呼叫 CreateKnowledgeBase API。
* 每個解答的替代問題數目上限：300
* URL 數目上限︰10
* 檔案數目上限︰10

## <a name="update-knowledge-base-call-limits"></a>更新知識庫呼叫限制
這些代表每個更新動作的限制；也就是，按一下 [儲存及訓練] 或呼叫 UpdateKnowledgeBase API。
* 每個來源名稱的長度：300
* 新增或刪除的替代問題數目上限：300
* 新增或刪除的中繼資料欄位數目上限：10
* 可重新整理的 URL 數目上限：5

## <a name="next-steps"></a>後續步驟

瞭解何時及如何變更[服務定價層](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)。
