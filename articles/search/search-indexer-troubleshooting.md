---
title: 針對常見搜尋索引子問題進行疑難排解 - Azure 搜尋服務
description: 使用 Azure 搜尋服務中的索引子修正錯誤與一般問題，包括資料來源連線、防火牆及遺漏的文件。
author: mgottein
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: 256a38320c9b3ca826ee9c12ac0a437957f988e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539255"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>針對 Azure 搜尋服務中的常見索引子問題進行疑難排解

索引子可能會在將資料建立索引到 Azure 搜尋服務時發生一些問題。 失敗的主要類別包括：

* [連線到資料來源](#data-source-connection-errors)
* [文件處理](#document-processing-errors)
* [擷取文件到索引](#index-errors)

## <a name="data-source-connection-errors"></a>資料來源連線錯誤

### <a name="blob-storage"></a>Blob 儲存體

#### <a name="storage-account-firewall"></a>儲存體帳戶防火牆

Azure 儲存體提供可設定的防火牆。 根據預設，防火牆會停用，以讓 Azure 搜尋服務能連線到您的儲存體帳戶。

防火牆啟用時，不會有特定錯誤訊息。 一般而言，防火牆錯誤看起來會像 `The remote server returned an error: (403) Forbidden`。

您可以在[入口網站](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)驗證防火牆是否已啟用。 若防火牆已啟用，您有兩個可以因應此問題的選項：

1. 透過選擇允許來自[「所有網路」](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)的存取來停用防火牆
1. 為您搜尋服務的 IP 位址[新增例外狀況](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules)。 若要尋找此 IP 位址，請使用下列命令：

`nslookup <service name>.search.windows.net`

例外狀況不適用於[認知搜尋](cognitive-search-concept-intro.md)。 唯一的因應措施是停用防火牆。

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>未啟用編製索引

Azure 搜尋服務針對 Cosmos DB 編製索引具有隱含相依性。 若您在 Cosmos DB 中關閉自動編製索引，Azure 搜尋服務雖然會傳回成功狀態，但卻無法為容器內容建立索引。 如需如何檢查設定及開啟編製索引的指示，請參閱[管理 Azure Cosmos DB 中的編製索引](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#manage-indexing-using-azure-portal)。

## <a name="document-processing-errors"></a>文件處理錯誤

### <a name="unprocessable-or-unsupported-documents"></a>無法處理或不支援的文件

Blob 索引子會[記錄明確支援哪些文件格式](search-howto-indexing-azure-blob-storage.md#supported-document-formats)。 有時候，Blob 儲存體容器會包含不支援的文件。 有時候則會包含有問題的文件。 您可以透過[變更設定選項](search-howto-indexing-azure-blob-storage.md#dealing-with-errors)來避免您的索引子在這些文件上停止：

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>遺漏文件內容

Blob 索引子會[從容器的 Blob 中尋找並擷取文字](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs)。 擷取文字的某些問題包括：

* 文件僅包含掃描的影像。 具有非文字內容的 PDF Blob (例如掃描的影像 (JPG)) 不會在標準 Blob 編製索引管線中產生結果。 如果您有文字項目使用的映像內容，您可以使用[認知搜尋](cognitive-search-concept-image-scenarios.md)尋找及擷取文字。
* Blob 索引子已設為只為中繼資料建立索引。 若要擷取內容，Blob 索引子必須設為[同時擷取內容和中繼資料](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)：

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>索引錯誤

### <a name="missing-documents"></a>遺漏文件

索引子會從[資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)尋找文件。 有時候索引內可能會遺漏應為其建立索引的資料來源文件。 導致這些錯誤的幾個常見原因如下：

* 尚未為文件建立索引。 檢查入口網站以了解成功的索引子執行。
* 文件已在索引子執行後更新。 若您的索引子是根據[排程](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)執行，它最後將重新執行並擷取文件。
* 資料來源中指定的[查詢](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax)排除了文件。 若文件並非資料來源的一部分，索引子將無法為文件建立索引。
* [欄位對應](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings)或是[認知搜尋](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)已變更的文件，它看起來與預期不同。
* 請使用[查閱文件 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) 來尋找您的文件。
