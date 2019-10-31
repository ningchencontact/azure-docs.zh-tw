---
title: 從 Azure Blob 索引子將一個 blob 索引到許多搜尋索引檔以進行全文檢索搜尋
description: 使用 Azure 搜尋服務 Blob 索引子來編目適用于文字內容的 Azure blob。 每個 blob 可能會產生一或多個 Azure 搜尋服務的索引檔。
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: d58be681c6a3e609712f9b0206de69f01d6a35f6
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177992"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>編制 blob 的索引以產生多個搜尋檔
根據預設，blob 索引子會將 blob 的內容視為單一搜尋檔。 某些**parsingMode**值支援個別 blob 可能會導致多個搜尋檔的案例。 不同類型的**parsingMode**可讓索引子從 blob 解壓縮一個以上的搜尋檔，如下所示：
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>一對多檔索引鍵
顯示在 Azure 搜尋服務索引中的每份檔都是由檔索引鍵唯一識別。 

當未指定剖析模式時，如果索引中沒有索引鍵欄位的明確對應，Azure 搜尋服務會自動將 `metadata_storage_path` 屬性[對應](search-indexer-field-mappings.md)為索引鍵。 此對應可確保每個 blob 都會顯示為不同的搜尋檔。

使用上述任何一種剖析模式時，一個 blob 會對應至「多個」搜尋檔，只根據不適用的 blob 中繼資料來製作檔索引鍵。 為了克服此條件約束，Azure 搜尋服務能夠為從 blob 解壓縮的每個個別實體產生「一對多」檔金鑰。 這個屬性的名稱為 `AzureSearch_DocumentKey`，而且會新增至從 blob 解壓縮的每個個別實體。 對於_跨 blob_的每個個別實體，此屬性的值一定是唯一的，而且實體會顯示為個別的搜尋檔。

根據預設，如果未指定索引鍵索引欄位的明確欄位對應，則會使用 `base64Encode` 欄位對應函式來對應 `AzureSearch_DocumentKey`。

## <a name="example"></a>範例
假設您有一個索引定義，其中包含下欄欄位：
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

而您的 blob 容器具有具有下列結構的 blob：

_Blob1 json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2 json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

當您建立索引子並將**parsingMode**設定為 `jsonLines` 時-若未指定索引鍵欄位的任何明確欄位對應，則會隱含套用下列對應
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

這項設定會導致 Azure 搜尋服務索引包含下列資訊（為了簡潔起見，已縮短 base64 編碼識別碼）

| id | 溫度 | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ...YjEuanNvbjsx | 100 | 100 | 2019-02-13T00：00：00Z |
| aHR0 ...YjEuanNvbjsy | 33 | 30 | 2019-02-14T00：00：00Z |
| aHR0 ...YjIuanNvbjsx | 1 | 1 | 2018-01-12T00：00：00Z |
| aHR0 ...YjIuanNvbjsy | 120 | 3 | 2013-05-11T00：00：00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>索引鍵欄位的自訂欄位對應

假設您的 blob 容器具有下列結構的相同索引定義：

_Blob1 json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2 json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

當您使用 `delimitedText` **parsingMode**建立索引子時，可能會很自然地將欄位對應函數設定為索引鍵欄位，如下所示：

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

不過，此對應_不_會導致在索引中顯示4份檔，因為 `recordid` 欄位在_blob 之間_不是唯一的。 因此，我們建議您針對「一對多」剖析模式，使用從 `AzureSearch_DocumentKey` 屬性套用至索引鍵索引欄位的隱含欄位對應。

如果您想要設定明確的欄位對應，請確定_sourceField_在**所有 blob**的個別實體中都是相異的。

> [!NOTE]
> `AzureSearch_DocumentKey` 確保每個已解壓縮實體的唯一性都有可能變更，因此您不應該依賴它的價值來滿足您的應用程式需求。

## <a name="next-steps"></a>後續步驟

如果您還不熟悉 blob 索引編制的基本結構和工作流程，您應該先參閱[Azure 搜尋服務的索引編制 Azure Blob 儲存體](search-howto-index-json-blobs.md)。 如需不同 blob 內容類型剖析模式的詳細資訊，請參閱下列文章。

> [!div class="nextstepaction"]
> [編制 CSV blob 的索引](search-howto-index-csv-blobs.md)
> [編制 JSON blob 的索引](search-howto-index-json-blobs.md)
