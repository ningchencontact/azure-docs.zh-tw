---
title: 新功能公告
titleSuffix: Azure Cognitive Search
description: 全新和增強功能的公告，包括將 Azure 搜尋服務的服務重新命名為 Azure 認知搜尋。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 26f6c651b78099eff80b6af57d2047cc8696f4c2
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112193"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure 認知搜尋的新功能

了解該服務的新功能。 將此頁面加入書簽，以掌握服務的最新狀態。

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Azure 搜尋服務的新服務名稱

Azure 搜尋服務現在已重新命名為**Azure 認知搜尋**，以反映核心作業中的認知技能和 AI 處理的擴充使用。 雖然認知技能新增了新功能，但使用 AI 是完全選擇性的。 您可以繼續使用不含 AI 的 Azure 認知搜尋，在您于雲端建立和管理的索引中，透過私人、未經處理、以文字為基礎的內容來建立豐富的全文檢索搜尋解決方案。 

API 版本、Nuget 套件、命名空間和端點都不會變更。 您現有的搜尋解決方案不受服務名稱變更的影響。

## <a name="feature-announcements"></a>功能公告

2019年11月4日-Ignite 會議

+ 累加[式索引編制（預覽）](cognitive-search-incremental-indexing-conceptual.md)可讓您只處理或重新處理對擴充管線進行修改時絕對必要的步驟。 如果您有先前分析的影像內容，這會特別有用。 成本高昂分析的輸出會儲存起來，然後用來做為額外索引或擴充的基礎。

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ 檔[解壓縮（預覽）](cognitive-search-skill-document-extraction.md)是在編制索引期間使用的認知技能，可讓您從技能集中解壓縮檔案的內容。 過去，只有在技能集執行之前才會發生檔破解。 隨著這項技能的增加，您也可以在技能集執行期間執行此作業。

+ [文字翻譯（預覽）](cognitive-search-skill-text-translation.md)是在索引編制期間所使用的認知技能，可評估文字，而針對每一筆記錄，會傳回轉譯為指定目的語言的文字。

+ [Power BI 範本](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md)可以快速開始您的視覺效果，並分析 Power BI desktop 中的知識存放區中豐富的內容。 此範本是針對透過 [匯[入資料] wizard](knowledge-store-create-portal.md)建立的 Azure 資料表投影所設計。

+ [Azure Data Lake Storage Gen2 （預覽）](search-howto-index-azure-data-lake-storage.md)、 [Cosmos DB Gremlin API （預覽）](search-howto-index-cosmosdb.md)和[Cosmos DB Cassandra API （預覽）](search-howto-index-cosmosdb.md)現在已在索引子中支援。 您可以使用[此表單](https://aka.ms/azure-cognitive-search/indexer-preview)進行註冊。 一旦您接受預覽計畫，就會收到一封確認電子郵件。

## <a name="service-updates"></a>服務更新

Azure 認知搜尋的[服務更新宣告](https://azure.microsoft.com/updates/?product=search&status=all)可在 azure 網站上找到。