---
title: 使用 Azure 搜尋服務 Blob 索引子，為 CSV Blob 編製索引 - Azure 搜尋服務
description: 使用 Azure 搜尋服務索引，搜耙 Azure Blob 儲存體中的 CSV Blob 以用於全文檢索搜尋。 索引子可為選取的資料來源 (例如 Azure Blob 儲存體) 將資料擷取自動化。
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: e7d959e77d27fb04b18f402e4056d4dea1607039
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522905"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>使用 Azure 搜尋服務 Blob 索引子編製索引 CSV Blob

> [!Note]
> delimitedText 剖析模式是處於預覽階段，應用程式不是用於生產環境而定。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前沒有 .NET SDK 支援。
>

根據預設， [Azure 搜尋服務 Blob 索引子](search-howto-indexing-azure-blob-storage.md) 會將分隔符號文字 Blob 剖析為單一的文字區塊。 不過，使用包含 CSV 資料的 blob，您通常想要將 blob 中的每一行當做個別文件。 例如，在給訂下列分隔文字時，您可能想要將它剖析為 2 個文件，每個都包含 [識別碼]、[發佈日期] 和 [標籤] 欄位： 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

在本文中，您將了解如何使用 Azure 搜尋服務 blob indexerby 設定剖析 CSV blob`delimitedText`剖析模式。 

> [!NOTE]
> 請依照下列中的索引子組態建議[-一對多編製索引](search-howto-index-one-to-many-blobs.md)輸出從一個 Azure blob 的多個搜尋文件。

## <a name="setting-up-csv-indexing"></a>設定 CSV 編製索引
編製索引 CSV blob、 建立或更新與索引子定義`delimitedText`剖析模式[建立索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)要求：

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` 指出每個 blob 的第一個 (非空白) 行包含標頭。
如果 blob 不包含初始的標頭行，應該在索引子組態中指定標頭︰ 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

您可以使用 `delimitedTextDelimiter` 組態集來自訂分隔符號字元。 例如:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> 目前，只支援 UTF-8 編碼。 如果您需要支援其他編碼，請在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search) 票選該編碼。

> [!IMPORTANT]
> 當您使用分隔符號文字剖析模式時，Azure 搜尋服務會假設您的資料來源中所有 Blob 都為 CSV。 如果您需要支援在相同的資料來源中混用 CSV 和非 CSV Blob，請在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search) 票選該編碼。
> 
> 

## <a name="request-examples"></a>要求範例
總而言之，以下是完整的承載範例。 

資料來源： 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

索引子：

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>協助我們改進 Azure 搜尋服務
如果您有功能要求或改進的想法，請在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/) 提供。

