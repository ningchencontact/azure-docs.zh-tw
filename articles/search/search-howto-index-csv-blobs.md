---
title: 使用 Azure 搜尋服務 Blob 索引子，為 CSV Blob 編製索引 - Azure 搜尋服務
description: 使用 Azure 搜尋服務索引，搜耙 Azure Blob 儲存體中的 CSV Blob 以用於全文檢索搜尋。 索引子可為選取的資料來源 (例如 Azure Blob 儲存體) 將資料擷取自動化。
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 0bbb131b5fb155443c8c3dc340185f3a6fa950a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871258"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>使用 Azure 搜尋服務 Blob 索引子編製索引 CSV Blob
根據預設， [Azure 搜尋服務 Blob 索引子](search-howto-indexing-azure-blob-storage.md) 會將分隔符號文字 Blob 剖析為單一的文字區塊。 不過，使用包含 CSV 資料的 blob，您通常想要將 blob 中的每一行當做個別文件。 例如，在給訂下列分隔文字時，您可能想要將它剖析為 2 個文件，每個都包含 [識別碼]、[發佈日期] 和 [標籤] 欄位： 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

在本文中，您將深入了解如何使用 Azure 搜尋服務 Blob 索引子來剖析 CSV Blob。 

> [!NOTE]
> 請依照下列中的索引子組態建議[-一對多編製索引](search-howto-index-one-to-many-blobs.md)輸出從一個 Azure blob 的多個搜尋文件。

## <a name="setting-up-csv-indexing"></a>設定 CSV 編製索引
若要對 CSV blob 編製索引，請使用 `delimitedText` 剖析模式建立或更新索引子定義︰  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

如需建立索引子 API 的詳細資訊，請參閱 [建立索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

`firstLineContainsHeaders` 指出每個 blob 的第一個 (非空白) 行包含標頭。
如果 blob 不包含初始的標頭行，應該在索引子組態中指定標頭︰ 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

您可以使用 `delimitedTextDelimiter` 組態集來自訂分隔符號字元。 例如︰

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

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

索引子：

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
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

