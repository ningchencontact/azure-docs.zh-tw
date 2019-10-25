---
title: 針對常見的搜尋索引子問題進行疑難排解
titleSuffix: Azure Cognitive Search
description: 修正 Azure 認知搜尋中索引子的錯誤和常見問題，包括資料來源連線、防火牆和遺失的檔。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c5a16d957f1e0414f92d0cc03442d88d438e4c92
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793619"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>針對 Azure 認知搜尋中的常見索引子問題進行疑難排解

將資料編制索引至 Azure 認知搜尋時，索引子可能會遇到一些問題。 失敗的主要類別包括：

* [連線到資料來源](#data-source-connection-errors)
* [文件處理](#document-processing-errors)
* [擷取文件到索引](#index-errors)

## <a name="data-source-connection-errors"></a>資料來源連線錯誤

### <a name="blob-storage"></a>Blob 儲存體

#### <a name="storage-account-firewall"></a>儲存體帳戶防火牆

Azure 儲存體提供可設定的防火牆。 預設會停用防火牆，因此 Azure 認知搜尋可以連接到您的儲存體帳戶。

防火牆啟用時，不會有特定錯誤訊息。 一般而言，防火牆錯誤看起來會像 `The remote server returned an error: (403) Forbidden`。

您可以在[入口網站](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)驗證防火牆是否已啟用。 唯一支援的解決方法是選擇允許從 [[所有網路](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)] 進行存取，以停用防火牆。

如果您的索引子沒有附加的技能集，您_可能會_嘗試為搜尋服務的 IP 位址[新增例外](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules)狀況。 不過，這種情況不受支援，而且不保證能正常執行。

您可以藉由 ping 其 FQDN （`<your-search-service-name>.search.windows.net`）來找出搜尋服務的 IP 位址。

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>未啟用編製索引

Azure 認知搜尋具有 Cosmos DB 索引的隱含相依性。 如果您關閉 Cosmos DB 中的自動編制索引，Azure 認知搜尋會傳回成功的狀態，但無法為容器內容編制索引。 如需如何檢查設定及開啟編製索引的指示，請參閱[管理 Azure Cosmos DB 中的編製索引](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal)。

## <a name="document-processing-errors"></a>文件處理錯誤

### <a name="unprocessable-or-unsupported-documents"></a>無法處理或不支援的文件

Blob 索引子會[記錄明確支援哪些文件格式](search-howto-indexing-azure-blob-storage.md#SupportedFormats)。 有時候，Blob 儲存體容器會包含不支援的文件。 有時候則會包含有問題的文件。 您可以透過[變更設定選項](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)來避免您的索引子在這些文件上停止：

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

* 文件僅包含掃描的影像。 具有非文字內容的 PDF Blob (例如掃描的影像 (JPG)) 不會在標準 Blob 編製索引管線中產生結果。 如果您的影像內容包含文字元素，您可以使用[認知搜尋](cognitive-search-concept-image-scenarios.md)來尋找並解壓縮文字。
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
* [欄位](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings)對應或[AI 擴充](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)已變更檔，看起來與您所預期的不同。
* 請使用[查閱文件 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) 來尋找您的文件。
