---
title: Blob 編製索引包含多個搜尋索引文件，從 Azure Blob 索引子，供全文檢索搜尋-Azure 搜尋服務
description: 搜耙 Azure blob 以供使用的 Azure 搜尋服務 Blob 索引子的文字內容。 每個 blob 可能會包含一或多個 Azure 搜尋服務索引文件。
ms.date: 05/02/2019
author: arv100kri
manager: briansmi
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: 628ced069c9d32c6e874c2e36a1e3b752c476003
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024656"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>產生多個搜尋文件的索引編製 blob
根據預設，blob 索引子會將 blob 的內容，視為單一搜尋文件。 某些**parsingMode**值支援的案例，其中個別的 blob 可能會導致多個搜尋文件。 不同類型的**parsingMode** ，允許超過一個的搜尋服務文件，從 blob 擷取的索引子：
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>一對多文件索引鍵
文件索引鍵可唯一識別 Azure 搜尋服務索引中會顯示每個文件。 

指定沒有剖析模式時，而且如果有未明確對應的 Azure 搜尋服務索引中的索引鍵欄位自動[對應](search-indexer-field-mappings.md)`metadata_storage_path`做為索引鍵的屬性。 此對應可確保每個 blob，會顯示為不同的搜尋服務文件。

當使用任何上述的剖析模式時，一個 blob 會對應至 「 多 」 的搜尋文件，讓主要依據不適用的 blob 中繼資料文件索引鍵。 若要克服這個條件約束，Azure 搜尋服務可以產生從 blob 擷取的每個個別實體的 「 一對多 」 文件索引鍵。 這個屬性名為`AzureSearch_DocumentKey`並加入至從 blob 擷取的每個個別實體。 這個屬性的值保證是唯一的每個個別的實體_到 blob_和實體會顯示為個別的搜尋文件。

根據預設，如果未指定索引鍵索引欄位沒有明確的欄位對應`AzureSearch_DocumentKey`對應，使用`base64Encode`欄位對應函式。

## <a name="example"></a>範例
假設您已經有索引定義下列欄位：
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

而且您的 blob 容器具有下列結構的 blob:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

當您建立索引子，並設定**parsingMode**至`jsonLines`-而不指定任何明確的欄位對應的索引鍵的欄位，下列對應將會隱含地套用
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

此安裝程式會產生包含下列資訊 （簡稱為求簡單明瞭的 base64 編碼識別碼） 的 Azure 搜尋服務索引

| id | 溫度 | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0...YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0...YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0...YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0...YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>索引鍵欄位的自訂欄位對應

假設前一個範例相同的索引定義，假設您的 blob 容器具有下列結構的 blob:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

當您建立與索引子`delimitedText` **parsingMode**，它可能會感覺自然索引鍵欄位的欄位對應函式設定，如下所示：

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

不過，此對應會_未_導致在索引中，顯示 4 個文件，因為`recordid`不是唯一的欄位_到 blob_。 因此，我們建議您先使用從套用的隱含的欄位對應`AzureSearch_DocumentKey`「 一對多 」 剖析模式的索引鍵索引欄位的屬性。

如果您要設定明確的欄位對應，請確定_sourceField_不同的每個個別的實體**跨所有 blob**。

> [!NOTE]
> 所使用的方法`AzureSearch_DocumentKey`確保唯一性，每個擷取的實體可能有所變更，因此您不應依賴它的值符合您的應用程式需求。

## <a name="see-also"></a>請參閱

+ [Azure 搜尋服務中的索引子](search-indexer-overview.md)
+ [使用 Azure 搜尋服務編製 Azure Blob 儲存體的索引](search-howto-index-json-blobs.md)
+ [使用 Azure 搜尋服務 blob 索引子編製 CSV blob 的索引](search-howto-index-csv-blobs.md)
+ [使用 Azure 搜尋服務 blob 索引子編製索引 JSON blob](search-howto-index-json-blobs.md)

## <a name="NextSteps"></a>接續步驟
* 若要深入了解 Azure 搜尋服務，請參閱 [[搜尋服務] 頁面](https://azure.microsoft.com/services/search/)。