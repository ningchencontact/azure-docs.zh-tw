---
title: 累加式編制索引簡介（預覽）
titleSuffix: Azure Cognitive Search
description: 設定您的 AI 擴充管線，以將您的資料驅動至最終一致性，以處理任何技能、技能集、索引子或資料來源的更新。
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ea3bcfc25040f09b6871d85412ac64061ec2f9e8
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549119"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>什麼是 Azure 認知搜尋中的增量編制索引？

> [!Note]
> 累加式索引編制僅供預覽，不適用於生產環境使用。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前沒有入口網站或 .NET SDK 支援。
>

增量索引是 Azure 認知搜尋的一項新功能，可將快取和狀態新增至認知技能集中的擴充內容，讓您控制在擴充管線中處理和重新處理個別步驟。 這不僅會保留您在處理中的金錢投資，還可讓系統更有效率。 快取結構和內容時，索引子可以判斷哪些技能已變更，並只執行已修改的技能，以及任何下游相依技能。 

使用累加式編制索引時，目前版本的擴充管線會執行最少量的工作，以確保索引中所有檔的一致性。 對於您想要完整控制的案例，您可以使用更精細的控制項來覆寫預期的行為。 如需設定的詳細資訊，請參閱[設定累加式索引編制](search-howto-incremental-index.md)。

## <a name="indexer-cache"></a>索引子快取

累加式編制索引會將索引子快取新增至擴充管線。 索引子會快取檔破解的結果，以及每個檔的每項技能的輸出。 更新技能集時，只會重新執行已變更或下游的技能。 更新的結果會寫入至快取，而且會更新索引和知識存放區中的檔。

實際上，快取是儲存體帳戶。 搜尋服務中的所有索引可能會共用相同的儲存體帳戶供索引子快取使用。 每個索引子都會指派一個唯一且不可變的快取識別碼。

### <a name="cache-configuration"></a>快取設定

您將需要在索引子上設定 `cache` 屬性，以從累加式索引編制開始受益。 下列範例說明已啟用快取的索引子。 下列各節將說明這項設定的特定部分。

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
    "parameters":{}
}
```

第一次在現有的索引子上設定這個屬性時，您也會需要重設它，這會導致資料來源中的所有檔再次被處理。 累加式編制索引的目標是要讓索引中的檔與您的資料來源和技能集的目前版本一致。 重設索引是此一致性的第一個步驟，因為它會排除舊版技能集所擴充的任何檔。 索引子必須重設為以一致的基準開始。

### <a name="cache-lifecycle"></a>快取生命週期

快取的生命週期是由索引子管理。 如果索引子上的 `cache` 屬性設定為 null 或連接字串已變更，則會刪除現有的快取。 快取生命週期也會系結至索引子生命週期。 如果刪除索引子，也會一併刪除相關聯的快取。

### <a name="indexer-cache-mode"></a>索引子快取模式

索引子快取可以在僅將資料寫入快取中，或將資料寫入快取，並用來重新擴充檔的模式下運作。  您可以藉由將快取中的 [`enableReprocessing`] 屬性設定為 [`false`]，以暫時暫停累加擴充，並在稍後將其設定為 [`true`] 來繼續累加擴充並提高最終一致性。 當您想要設定索引新檔的優先順序，以確保檔主體之間的一致性時，這個控制項特別有用。

## <a name="change-detection-override"></a>變更偵測覆寫

累加式索引可讓您更精確地控制擴充管線的所有層面。 此控制項可讓您處理變更可能會產生非預期結果的情況。 例如，編輯技能集和更新自訂技能的 URL，會導致索引子讓該技能的快取結果失效。 如果您只是將端點移至不同的 VM，或使用新的存取金鑰來重新部署您的技能，您就不會想要重新處理任何現有的檔。

若要確保索引子只會擴充您明確需要的，技能集的更新可以選擇性地將 `disableCacheReprocessingChangeDetection` querystring 參數設定為 `true`。 設定時，此參數可確保只會認可技能集的更新，且不會評估變更對現有主體的影響。

下列範例說明 querystring 使用方式。 它是要求的一部分，具有 & 分隔的索引鍵值組。 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>快取失效

這種情況的相反，您可以在其中部署新版本的自訂技能，擴充管線中的任何內容都不會變更，但是您需要特定技能失效，而且所有受影響的檔都會重新處理，以反映更新模型的優點。 在這種情況下，您可以在技能集上呼叫不正確技能作業。 重設技能 API 會接受 POST 要求，以及快取中應該失效之技能輸出的清單。 如需重設技能 API 的詳細資訊，請參閱[重設索引子（搜尋 REST API）](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)。

## <a name="bi-directional-change-detection"></a>雙向變更偵測

索引子不只會向前移動和處理新檔，現在可以向後移動並驅動先前處理過的檔以保持一致性。 有了這項新功能，請務必瞭解擴充管線元件的變更如何導致索引子工作。 索引子會將工作排入佇列，以在識別出因快取內容而失效或不一致的變更時進行。

### <a name="invalidating-changes"></a>使變更失效

使變更失效是很罕見的，但對擴充管線的狀態有重大影響。 不正確變更是指整個快取不再有效的情況。 無效變更的範例之一，就是您的資料來源會在其中更新。 在您知道變更不應使快取不正確情況下（例如，輪替儲存體帳戶上的金鑰），`ignoreResetRequirement` querystring 參數應該設定為在特定資源的更新作業上 `true`，以確保作業為未被拒絕。

以下是會使您的快取失效的完整變更清單：

* 變更為您的資料來源類型
* 變更為資料來源容器
* 資料來源認證
* 資料來源變更偵測原則
* 資料來源刪除偵測原則
* 索引子欄位對應
* 索引子參數
    * 剖析模式
    * 排除的副檔名
    * 索引的副檔名
    * 僅針對超大格式的檔編制儲存體中繼資料的索引
    * 分隔的文字標頭
    * 分隔的文字分隔符號
    * 檔根目錄
    * 影像動作（對映射解壓縮方式的變更）

### <a name="inconsistent-changes"></a>不一致的變更

不一致變更的範例是修改技能的技能集更新。 修改可能會使快取的一部分不一致。 索引子會識別工作，讓專案再次一致。  

變更的完整清單會導致快取不一致：

* 技能集中的技能具有不同的類型。 技能的 odata 類型已更新
* 已更新的技能特定參數，例如 url、預設值或其他參數
* 技能輸出變更，技能會傳回額外或不同的輸出
* 產生的技能更新是不同的上階，技能鏈已改變，亦即 技能輸入
* 如果提供此技能輸入的技能已更新，則任何上游技能都會失效
* 知識存放區投射位置的更新，結果 reprojecting 檔
* 知識存放區投射的變更，結果 reprojecting 檔
* 在 reprojecting 檔中將索引子的輸出欄位對應變更為索引

## <a name="rest-api-reference-for-incremental-indexing"></a>增量索引的 REST API 參考

REST `api-version=2019-05-06-Preview` 提供用於增量編制索引的 Api，以及索引子、技能集和資料來源的新增專案。 參考檔目前並未包含這些新增專案。 下一節將說明 API 的變更。

### <a name="indexers"></a>索引子

[建立索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)和[更新索引子](https://docs.microsoft.com/rest/api/searchservice/update-indexer)現在會公開與快取相關的新屬性：

* `StorageAccountConnectionString`：將用來快取中繼結果之儲存體帳戶的連接字串。

* `CacheId`： `cacheId` 是 `annotationCache` 儲存體帳戶內的容器識別碼，將用來做為此索引子的快取。 此快取對此索引子而言是唯一的，而且如果刪除索引子並使用相同的名稱重新建立，則會重新產生 `cacheId`。 無法設定 `cacheId`，它一律會由服務產生。

* `EnableReprocessing`：設定為 [`false`] 時，預設為 [`true`]，檔會繼續寫入快取，但不會根據快取資料重新處理任何現有的檔。

某些索引子（經由[資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)）會透過查詢抓取資料。 若為抓取資料的查詢，索引子也會支援新的查詢字串參數：當您的更新動作不應使快取無效時，`ignoreResetRequirement` 應該設定為 `true`。

### <a name="skillsets"></a>技能集

技能集不支援任何新的作業，但會支援新的 querystring 參數：當您想要根據目前的動作來更新現有的檔時，`disableCacheReprocessingChangeDetection` 應該設定為 [`true`]。

### <a name="datasources"></a>資料來源

資料來源不支援任何新作業，但是會支援新的 querystring 參數：當您的更新動作不應使快取無效時，`ignoreResetRequirement` 應該設定為 `true`。

## <a name="best-practices"></a>最佳作法

使用累加索引編制的建議方法是，在新的索引子上設定快取屬性，或重設現有的索引子，並設定快取屬性，以設定增量編制索引。

請謹慎使用 `ignoreResetRequirement`，因為它可能會導致不容易偵測到的資料中發生非預期的不一致。

## <a name="takeaways"></a>重要摘要

累加式索引是一項功能強大的功能，可將資料來源的變更追蹤延伸至擴充管線的所有層面，包括資料來源、技能集的目前版本，以及索引子。 當您的技能、技能集或擴充演變時，擴充管線可確保最少的工作，同時仍能讓您的檔達到最終一致性。

## <a name="next-steps"></a>後續步驟

藉由將快取新增至現有的索引子，或在定義新的索引子時新增快取，以開始進行增量索引編制。

> [!div class="nextstepaction"]
> [設定累加式編制索引](search-howto-incremental-index.md)
