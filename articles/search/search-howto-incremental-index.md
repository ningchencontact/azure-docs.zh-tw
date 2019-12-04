---
title: 新增累加式索引編制（預覽）
titleSuffix: Azure Cognitive Search
description: 啟用變更追蹤，並保留擴充內容的狀態，以在認知技能集中進行控制處理。 此功能目前為公開預覽狀態。
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 348bc2d92f636d1f3c3b50ea31334355da59a60f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790493"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中設定擴充檔的增量編制索引

> [!IMPORTANT] 
> 累加式編制索引目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前沒有入口網站或 .NET SDK 支援。

本文說明如何將狀態和快取新增至透過 Azure 認知搜尋擴充管線移動的擴充檔，讓您可以從任何支援的資料來源以累加方式編制檔的索引。 根據預設，技能集是無狀態的，而且變更其組合的任何部分，都需要完整重新執行索引子。 使用累加式編制索引時，索引子可以判斷管線的哪些部分已變更、重複使用現有的擴充做為未變更的部分，以及修改擴充，以瞭解進行變更的步驟。 快取的內容會放在 Azure 儲存體中。

如果您不熟悉如何設定索引子，請從[索引子總覽](search-indexer-overview.md)開始，然後繼續[技能集](cognitive-search-working-with-skillsets.md)以瞭解擴充管線。 如需重要概念的詳細背景，請參閱累加[式索引編制](cognitive-search-incremental-indexing-conceptual.md)。

## <a name="modify-an-existing-indexer"></a>修改現有的索引子

如果您有現有的索引子，請遵循下列步驟來啟用累加式索引編制。

### <a name="step-1-get-the-indexer"></a>步驟1：取得索引子

請從已定義必要資料來源和索引的有效現有索引子開始。 您的索引子應該是可執行檔。 使用 API 用戶端，建立[get 要求](https://docs.microsoft.com/rest/api/searchservice/get-indexer)以取得索引子的目前設定，而您想要在其中新增累加式索引。

```http
GET https://[service name].search.windows.net/indexers/[your indexer name]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-2-add-the-cache-property"></a>步驟2：新增快取屬性

< < < < < < < 標頭編輯 GET 要求的回應，以將 `cache` 屬性新增至索引子。 快取物件只需要單一屬性，`storageConnectionString` 這是儲存體帳戶的連接字串。 = = = = = = = 編輯 GET 要求的回應，以將 `cache` 屬性加入至索引子。 Cache 物件只需要單一屬性，而這就是 Azure 儲存體帳戶的連接字串。
>>>>>>> 3519a330aa86b6827d31403690529105825b1b16

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": {
        "configuration": {
            "enableAnnotationCache": true
        }
    }
}
```
#### <a name="enable-reporocessing"></a>啟用 reporocessing

您可以選擇性地在快取中設定 `enableReprocessing` 的布林值屬性，預設會將設定為 true。 `enableReprocessing` 旗標可讓您控制索引子的行為。 在您想要讓索引子排定將新檔新增至索引的情況下，您可以將旗標設定為 false。 一旦您的索引子與新檔攔截之後，將旗標翻轉為 true，然後讓索引子開始驅動現有的檔，使其成為最終一致性。 在 `enableReprocessing` 旗標設定為 false 的期間，索引子只會寫入快取，但不會根據已識別的擴充管線變更來處理任何現有的檔。

### <a name="step-3-reset-the-indexer"></a>步驟3：重設索引子

> [!NOTE]
> 重設索引子將導致資料來源中的所有檔都被重新處理，以便快取內容。 所有的認知擴充都會在所有檔上重新執行。
>

設定現有索引子的累加索引編制時，需要重設索引子，以確保所有檔都處於一致的狀態。 使用[REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)重設索引子。

```http
POST https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-4-save-the-updated-definition"></a>步驟4：儲存已更新的定義

使用 PUT 要求更新索引子定義，要求的主體應該包含更新的索引子定義。

```http
PUT https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
{
    "name" : "your indexer name",
    ...
    "cache": {
        "storageConnectionString": "[your storage connection string]",
        "enableReprocessing": true
    }
}
```

如果您現在在索引子上發出另一個 GET 要求，服務的回應將會在 cache 物件中包含 `cacheId` 屬性。 `cacheId` 是容器的名稱，其中將包含此索引子所處理之每份檔的所有快取結果和中繼狀態。

## <a name="enable-incremental-indexing-on-new-indexers"></a>在新的索引子上啟用累加式編制索引

若要為新的索引子設定增量索引，請在索引子定義裝載中包含 `cache` 屬性。 請確定您使用的是 `2019-05-06-Preview` 版本的 API。

## <a name="overriding-incremental-indexing"></a>覆寫增量索引

當設定時，累加式編制索引會追蹤您索引管線的變更，並驅動檔，使其在您的索引和預測之間具有最終一致性。 在某些情況下，您必須覆寫此行為，以確保索引子不會因更新索引管線而執行額外的工作。 例如，更新資料來源連接字串時，將需要在資料來源變更時，重設索引子並重新編制所有檔的索引。 但是，如果您只是使用新的金鑰來更新連接字串，您不會想要變更導致現有檔的任何更新。 相反地，您可能會想要讓索引子使快取失效並擴充檔，即使沒有對索引管線進行任何變更也是如此。 比方說，如果您要以新的模型重新部署自訂技能，而且希望技能在您所有的檔上執行，您可能會想要讓索引子失效。

### <a name="override-reset-requirement"></a>覆寫重設需求

對索引管線進行變更時，導致快取失效的任何變更都需要重新建立索引子。 如果您要對索引子管線進行變更，而不想讓變更追蹤使快取失效，您必須將 `ignoreResetRequirement` querystring 參數設定為 `true`，以在索引子或資料來源上進行作業。

### <a name="override-change-detection"></a>覆寫變更偵測

當您更新會導致檔標記為不一致的技能集時（例如，在重新部署技能時更新自訂技能 URL）時，請將 `disableCacheReprocessingChangeDetection` 查詢字串參數設定為 `true` 的技能集更新。

### <a name="force-change-detection"></a>強制變更偵測

具現化：當您想要讓索引管線辨識外部實體的變更（例如部署新版本的自訂技能）時，您必須編輯技能定義來更新技能集和「觸控」特定技能，特別是要強制執行的 URL。變更偵測並使該技能的快取失效。

## <a name="next-steps"></a>後續步驟

本文涵蓋包含技能集之索引子的增量索引編制。 若要進一步細分您的知識，請參閱一般重新編制索引的相關文章，適用于 Azure 認知搜尋中的所有索引編制案例。

+ [如何重建 Azure 認知搜尋索引](search-howto-reindex.md)。 
+ [如何在 Azure 認知搜尋中為大型資料集編制索引](search-howto-large-index.md)。 
