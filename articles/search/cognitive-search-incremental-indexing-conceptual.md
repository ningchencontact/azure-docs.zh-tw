---
title: 累加式編制索引（預覽）
titleSuffix: Azure Cognitive Search
description: 設定您的 AI 擴充管線，以將您的資料驅動至最終一致性，以處理任何技能、技能集、索引子或資料來源的更新。 這項功能目前為公開預覽狀態
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 76ab8784f04f3c67e4ea8062505931783048dea1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113596"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>什麼是 Azure 認知搜尋中的增量編制索引？

> [!IMPORTANT] 
> 累加式編制索引目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前沒有入口網站或 .NET SDK 支援。

增量索引是 Azure 認知搜尋的一項新功能，可將快取和狀態新增至認知技能集中的擴充內容，讓您控制在擴充管線中處理和重新處理個別步驟。 這不僅會保留您在處理中的金錢投資，還可讓系統更有效率。 快取結構和內容時，索引子可以判斷哪些技能已變更，並只執行已修改的技能，以及任何下游相依技能。 

透過累加式索引編製，目前的擴充管線版本只需執行最少量的工作，即可確保索引中所有文件的一致性。 對於您想要完整控制的案例，您可以使用更精細的控制項來覆寫預期的行為。 如需設定的詳細資訊，請參閱[設定累加式索引編制](search-howto-incremental-index.md)。

## <a name="indexer-cache"></a>索引子快取

累加式索引編製會將索引子快取新增至擴充管線。 索引子會快取文件萃取的結果，以及每份文件針對每項技能的輸出。 技能集有所更新時，將只會重新執行已變更或下游的技能。 更新的結果會寫入至快取，且索引和知識存放區中的文件會隨之更新。

快取實際上是儲存體帳戶。 搜尋服務中的所有索引可共用相同的儲存體帳戶，以進行索引子快取。 系統會為每個索引子指派一個唯一且不可變的快取識別碼。

### <a name="cache-configuration"></a>快取組態

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

第一次在現有的索引子上設定這個屬性時，您也會需要重設它，這會導致資料來源中的所有檔再次被處理。 累加式索引編製的目標是要讓索引中的文件與您的資料來源和目前的技能集版本趨於一致。 重設索引是達成此一致性的首要步驟，因為這樣會消除舊版技能集所擴充的任何文件。 索引子必須重設，而以一致的基準開始。

### <a name="cache-lifecycle"></a>快取生命週期

快取的生命週期由索引子所管理。 如果索引子上的 `cache` 屬性設定為 null 或連接字串已變更，則會刪除現有的快取。 快取生命週期也與索引子生命週期有關。 如果索引子遭到刪除，相關聯的快取也會一併刪除。

### <a name="indexer-cache-mode"></a>索引子快取模式

索引子快取可在資料僅寫入至快取的模式下運作，或將資料寫入至快取並用來重新擴充文件的模式下運作。  您可以將快取中的 `enableReprocessing` 屬性設定為 `false`，以暫止累加式擴充，且稍後將其設定為 `true` 以繼續累加式擴充，並達成最終的一致性。 當您想要讓為新文件編製索引的優先順序高於確保文件主體間的一致性時，此控制會特別有用。

## <a name="change-detection-override"></a>變更偵測覆寫

累加式索引編製可讓您更精確地控制擴充管線的各個層面。 此控制可讓您處理變更可能會產生非預期結果的情況。 例如，編輯技能集和更新自訂技能的 URL，會導致索引子讓該技能的快取結果失效。 如果您只是要將端點移至不同的 VM，或是要使用新的存取金鑰來重新部署您的技能，您就不需要重新處理任何現有的文件。

若要確保索引子只會擴充您明確需要的，技能集的更新可以選擇性地將 `disableCacheReprocessingChangeDetection` querystring 參數設定為 `true`。 設定後，此參數將可確保只會認可技能集的更新，且不會評估變更對現有主體的影響。

下列範例說明 querystring 使用方式。 它是要求的一部分，具有 & 分隔的索引鍵值組。 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>快取失效

相反的情況是，您要部署新版本的自訂技能，而擴充管線中的任何內容皆未變更，但您需要讓特定技能失效，且所有受影響的文件都需要重新處理，以反映更新模型的優點。 在這種情況下，您可以對技能集呼叫讓技能失效的作業。 重設技能 API 可接受 POST 要求，以及快取中應失效的技能輸出清單。 如需重設技能 API 的詳細資訊，請參閱[重設索引子（搜尋 REST API）](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)。

## <a name="bi-directional-change-detection"></a>雙向變更偵測

索引子不只會前向處理新文件，現在也能夠回溯讓先前處理的文件達成一致性。 使用這項新功能時，請務必了解擴充管線元件的變更如何產生索引子工作。 索引子會將工作排入佇列，以在識別出因快取內容而失效或不一致的變更時進行。

### <a name="invalidating-changes"></a>使變更失效

使變更失效是很罕見的作業，但對擴充管線的狀態會有重大影響。 失效變更是指會讓整個快取都不再有效的變更。 舉例來說，讓您的資料來源進行更新的變更，即為失效變更。 在您知道變更不應使快取不正確情況下（例如，輪替儲存體帳戶上的金鑰），`ignoreResetRequirement` querystring 參數應該設定為在特定資源的更新作業上 `true`，以確保作業為未被拒絕。

以下是會使快取失效的完整變更清單：

* 資料來源類型的變更
* 資料來源容器的變更
* 資料來源認證
* 資料來源變更偵測原則
* 資料來源刪除偵測原則
* 索引子欄位對應
* 索引子參數
    * 剖析模式
    * 排除的副檔名
    * 已編製索引的副檔名
    * 僅針對過大的文件編製儲存體中繼資料的索引
    * 分隔符號文字標頭
    * 分隔符號文字分隔符號
    * 文件根目錄
    * 影像動作 (影像擷取方式的變更)

### <a name="inconsistent-changes"></a>不一致變更

舉例來說，更新修改了技能的技能集，即為不一致變更。 修改可能會使快取的某部分不一致。 索引子會識別可再次達成一致性的工作。  

導致快取不一致的完整變更清單如下：

* 技能集中的技能具有不同的類型。 技能的 oData 類型已更新
* 技能的特定參數已更新，例如 URL、預設值或其他參數
* 技能輸出變更，即技能傳回了額外或不同的輸出
* 技能更新結果是不同的上階，也就是說技能鏈結已改變 技能輸入
* 如果為任何上游技能提供輸入的技能已更新，則此上游技能將會失效
* 更新知識存放區投射位置，導致文件重新投射
* 變更知識存放區投射，導致文件重新投射
* 索引子的輸出欄位對應已變更，導致文件重新投射至索引

## <a name="rest-api-reference-for-incremental-indexing"></a>增量索引的 REST API 參考

REST `api-version=2019-05-06-Preview` 提供用於增量編制索引的 Api，以及索引子、技能集和資料來源的新增專案。 參考檔目前並未包含這些新增專案。 下一節將說明 API 的變更。

### <a name="indexers"></a>索引子

[建立索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)和[更新索引子](https://docs.microsoft.com/rest/api/searchservice/update-indexer)現在會公開與快取相關的新屬性：

* `StorageAccountConnectionString`：將用來快取中繼結果之儲存體帳戶的連接字串。

* `CacheId`： `cacheId` 是 `annotationCache` 儲存體帳戶內的容器識別碼，將用來做為此索引子的快取。 此快取對此索引子而言將是唯一的，而且，如果刪除索引子並使用相同的名稱加以重新建立，則會重新產生 `cacheId`。 `cacheId` 無法設定，它一律會由服務產生。

* `EnableReprocessing`：設定為 [`false`] 時，預設為 [`true`]，檔會繼續寫入快取，但不會根據快取資料重新處理任何現有的檔。

某些索引子（經由[資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)）會透過查詢抓取資料。 若為抓取資料的查詢，索引子也會支援新的查詢字串參數：當您的更新動作不應使快取無效時，`ignoreResetRequirement` 應該設定為 `true`。

### <a name="skillsets"></a>技能集

技能集將不支援任何新的作業，但會支援新的 querystring 參數：當您不想根據目前的動作來更新任何現有的文件時，`disableCacheReprocessingChangeDetection` 應設定為 `true`。

### <a name="datasources"></a>資料來源

資料來源將不支援任何新的作業，但會支援新的 querystring 參數：當您的更新動作不應使快取失效時，`ignoreResetRequirement` 應設定為 `true`。

## <a name="best-practices"></a>最佳作法

使用累加式索引編製的建議方法是，藉由在新的索引子上設定快取屬性，來設定累加式索引編製，或重設現有的索引子並設定快取屬性。

請謹慎使用 `ignoreResetRequirement`，因為它可能會導致不容易偵測到的資料中發生非預期的不一致。

## <a name="takeaways"></a>重要摘要

累加式索引是一項功能強大的功能，可將資料來源的變更追蹤延伸至擴充管線的所有層面，包括資料來源、技能集的目前版本，以及索引子。 當您的技能、技能集或擴充改變時，擴充管線可確實完成最少量的工作，同時仍能讓您的文件達到最終的一致性。

## <a name="next-steps"></a>後續步驟

將快取新增至現有的索引子，或在定義新的索引子時新增快取，以開始進行累加式索引編製。

> [!div class="nextstepaction"]
> [設定累加式編制索引](search-howto-incremental-index.md)
