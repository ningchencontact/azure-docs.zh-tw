---
title: 限制和界限 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 知識庫和服務的某些部分有中繼資料的限制。 請務必將您的知識庫保持在這些限制範圍內，以便進行測試及發佈。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/26/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: fe15cafceea8128735f7241fa5e4187d4d9c47a9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960049"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker 知識庫的限制和界限
QnA Maker 的完整限制清單。

## <a name="knowledge-bases"></a>知識庫

* 以 [Azure 搜尋服務層限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)為基礎的知識庫數目上限

|**Azure 搜尋服務層** | **免費** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|允許的已發佈知識庫數目上限|2|14|49|199|199|2,999|

 比方說，如果您的層次有 15 個允許的索引，您可以發佈 14 個知識庫 (每個已發佈的知識庫 1 個索引)。 第十五個索引 (`testkb`) 用於撰寫和測試所有知識庫。 

## <a name="extraction-limits"></a>擷取限制
* 可擷取的檔案數目上限與檔案大小上限：請參閱 [QnAMaker 定價](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)
* 可以從常見問題集 HTML 頁面搜耙，以便擷取 QnAs 的深層連結數目上限：20

## <a name="metadata-limits"></a>中繼資料限制
* 每個知識庫的中繼資料欄位數目上限 (以 [Azure 搜尋服務層限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)為基礎)

|**Azure 搜尋服務層** | **免費** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|每項 QnA Maker 服務的中繼資料欄位數上限 (橫跨所有的知識庫)|1,000|100*|1,000|1,000|1,000|1,000|

## <a name="knowledge-base-content-limits"></a>知識庫的內容限制
知識庫內容的整體限制：
* 解答文字的長度：25,000
* 問題文字的長度：1,000
* 中繼資料索引鍵/值文字的長度：100
* 中繼資料名稱的支援字元：英文字母、數字和 _  
* 中繼資料值的支援字元：: 和 | 以外的所有字元 
* 檔案名稱的長度：200
* 支援的檔案格式：".tsv"、".pdf"、".txt"、".docx"、".xlsx"。
* 替代問題數目上限：100
* 問答組數目上限：取決於[Azure 搜尋服務層](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)選擇。 問題和答案組會對應至 Azure 搜尋服務索引上的文件。 

## <a name="create-knowledge-base-call-limits"></a>建立知識庫呼叫限制：
這些代表每個建立知識庫動作的限制；也就是，按一下 [建立知識庫] 或呼叫 CreateKnowledgeBase API。
* 每個解答的替代問題數目上限：100
* URL 數目上限︰10
* 檔案數目上限︰10

## <a name="update-knowledge-base-call-limits"></a>更新知識庫呼叫限制
這些代表每個更新動作的限制；也就是，按一下 [儲存及訓練] 或呼叫 UpdateKnowledgeBase API。
* 每個來源名稱的長度：300
* 新增或刪除的替代問題數目上限：100
* 新增或刪除的中繼資料欄位數目上限：10
* 可重新整理的 URL 數目上限：5

## <a name="next-steps"></a>後續步驟

了解何時及如何變更服務層級：

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku):當您需要有多個原始程式檔，或在您的知識庫，超越您目前的層次，文件愈大升級 QnA Maker 服務定價層。
* [App Service](how-to/upgrade-qnamaker-service.md#upgrade-app-service):當您的知識庫需要為來自用戶端應用程式的更多要求提供服務時，請將 App Service 定價層升級。
* [Azure 搜尋服務](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service):如果您打算有許多知識庫，請將 Azure 搜尋服務定價層升級。
