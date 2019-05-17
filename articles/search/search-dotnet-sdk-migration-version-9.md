---
title: 升級至 Azure 搜尋服務.NET SDK 版本 9-Azure 搜尋服務
description: 移轉至 Azure 搜尋服務.NET SDK 版本 9 的程式碼，從較舊版本。 了解新功能與必要的程式碼變更。
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: a59deed4ac0cec669ddc5e0335f7274586c702e8
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65541772"
---
# <a name="upgrade-to-the-azure-search-net-sdk-version-9"></a>升級至 Azure 搜尋服務.NET SDK 版本 9

如果您使用 7.0 預覽版或更舊版本[Azure 搜尋服務.NET SDK](https://aka.ms/search-sdk)，本文將協助您升級您的應用程式使用 9 版。

> [!NOTE]
> 如果您想要使用版本 8.0-preview 評估尚無法正式推出的功能，您也可以依照這篇文章中的指示，從舊版升級至 8.0-preview。

如需包括範例的 SDK 一般逐步解說，請參閱 [如何從 .NET 應用程式使用 Azure 搜尋服務](search-howto-dotnet-sdk.md)。

Azure 搜尋服務.NET SDK 第 9 版包含較舊版本的許多變更。 其中有些重大變更，但它們應該只需要相當小幅變更您的程式碼。 請參閱 [升級步驟](#UpgradeSteps) 以取得如何變更您的程式碼以使用新的 SDK 版本的指示。

> [!NOTE]
> 如果您使用版本 4.0 預覽或更舊版本，您就應該升級至版本 5，，然後再升級到版本 9。 請參閱[升級至 Azure 搜尋服務.NET SDK 第 5 版](search-dotnet-sdk-migration-version-5.md)如需相關指示。
>
> 您的「Azure 搜尋服務」執行個體支援數個 REST API 版本，包括最新版本。 當一個版本不再是最新版本時，您仍可繼續使用該版本，但建議您將程式碼移轉成使用最新版本。 使用 REST API 時，您必須每個要求中透過 api-version 參數指定 API 版本。 使用 .NET SDK 時，您使用的 SDK 版本會決定對應的 REST API 版本。 如果您使用的是舊版 SDK，則即使服務已升級成支援新版 API 版本，您仍可繼續執行該程式碼而無須變更。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>什麼是 9 版的新功能
Azure 搜尋服務.NET SDK 第 9 版為目標的最新的 Azure 搜尋服務 REST API，特別是 2019年-05-06 公開上市版本。 這可讓您從 .NET 應用程式中使用 Azure 搜尋服務的新功能，包括：

* [認知搜尋](cognitive-search-concept-intro.md)是 Azure 搜尋服務，用來擷取映像、 blob 和其他非結構化的資料來源-豐富的內容，讓它成為更可搜尋中的 Azure 搜尋服務索引中的文字中的人工智慧功能。
* 支援[複雜型別](search-howto-complex-data-types.md)可讓您建立 Azure 搜尋服務索引中幾乎任何巢狀的 JSON 結構的模型。
* [自動完成](search-autocomplete-tutorial.md)提供的替代項目**建議**API 來實作搜尋---輸入時的行為。 自動完成 「 完成 」 的單字或片語，在使用者目前輸入。
* [剖析模式的 JsonLines](search-howto-index-json-blobs.md)、 組件的 Azure Blob 編製索引，會建立每個會以新行字元分隔的 JSON 實體的一個搜尋服務文件。

### <a name="new-preview-features-in-version-80-preview"></a>在版本 8.0-preview 的新預覽功能
版本 8.0-preview Azure 搜尋服務.NET sdk 的目標 API 版本 2017年-11-11-Preview。 此版本包含所有相同功能的版本 9、 加上：

* [客戶管理的加密金鑰](search-security-manage-encryption-keys.md)服務端加密待用是新的預覽功能。 除了內建-待用加密由 Microsoft 管理，您可以套用額外的所在索引鍵的唯一擁有者的加密。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升級步驟
首先，更新 `Microsoft.Azure.Search` 的 NuGet 參考，方法是使用 NuGet 封裝管理員主控台，或是在 Visual Studio 中用滑鼠右鍵按一下您的專案參考並選取 [管理 NuGet 封裝]。

一旦 NuGet 下載新的封裝和其相依性，請重建您的專案。 根據您的程式碼結構情況，它可能會順利重新建置。 如果是這樣，代表您已準備就緒！

如果建置失敗，您必須修正每個組建錯誤。 請參閱[9 版中的重大變更](#ListOfChanges)詳細說明如何解決每個可能的建置錯誤。

您可能會看到與過時的方法或屬性相關的其他建置警告。 警告將會包含要使用哪些內容取代過時功能的指示。 例如，如果您的應用程式會使用`DataSourceType.DocumentDb`屬性，您應該取得警告，指出 「 此成員已被取代。 請改用 CosmosDb"。

在您修正所有建置錯誤或警告之後，就可以視需要對您的應用程式進行變更，以利用新的功能。 在 SDK 中的新功能的詳細[什麼是 9 版的新](#WhatsNew)。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>第 9 版中的重大變更

第 9 版可能需要程式碼變更，除了重新建置您的應用程式中有幾項重大變更。

> [!NOTE]
> 變更下方的清單並不詳盡。 某些變更可能不會導致建置錯誤，但就技術上而言重大，因為它們可能會中斷相依於舊版的 Azure 搜尋服務.NET SDK 組件的組件的二進位相容性。 這類變更不會列出如下。 請升級至版本 9，以避免任何二進位碼相容性問題時，重建您的應用程式。

### <a name="immutable-properties"></a>不可變的屬性

數個模型類別的公用屬性現在為不可變的。 如果您需要建立自訂的執行個體，這些類別進行測試，您可以使用新的參數化建構函式：

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>變更欄位

`Field`類別已變更，現在，它也可以代表複雜的欄位。

下列`bool`屬性現在是可為 null:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

這是因為這些屬性現在必須`null`在複雜的欄位。 如果您有讀取這些屬性的程式碼時，必須先將其準備好處理`null`。 請注意，所有其他屬性`Field`一直並繼續為可為 null，其中部分也將會是`null`在複雜的欄位，特別是下列情況下：

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

無參數建構函式`Field`發出`internal`。 從現在起，每個`Field`建構期間需要明確的名稱和資料類型。

### <a name="simplified-batch-and-results-types"></a>簡化的批次和結果型別

在版本 7.0-preview 及更早版本中，各種封裝群組的文件的類別已結構化成平行類別階層：

  -  `DocumentSearchResult` 和`DocumentSearchResult<T>`繼承自 `DocumentSearchResultBase`
  -  `DocumentSuggestResult` 和`DocumentSuggestResult<T>`繼承自 `DocumentSuggestResultBase`
  -  `IndexAction` 和`IndexAction<T>`繼承自 `IndexActionBase`
  -  `IndexBatch` 和`IndexBatch<T>`繼承自 `IndexBatchBase`
  -  `SearchResult` 和`SearchResult<T>`繼承自 `SearchResultBase`
  -  `SuggestResult` 和`SuggestResult<T>`繼承自 `SuggestResultBase`

衍生的型別，而泛型類型參數不是適用於在 「 動態型別 」 的情況下，而且假設的使用方式`Document`型別。

從版本 8.0-preview 開始，基底類別和衍生的類別的非泛型所有已移除。 動態類型的情況下，您可以使用`IndexBatch<Document>`， `DocumentSearchResult<Document>`，依此類推。
 
### <a name="removed-extensibleenum"></a>已移除的 ExtensibleEnum

`ExtensibleEnum`基底類別已移除。 所有從它衍生的類別為現在結構，例如`AnalyzerName`， `DataType`，和`DataSourceType`例如。 其`Create`方法也已經移除。 您可以只移除呼叫`Create`因為這些類型都會從字串隱含轉換。 如果這會導致編譯器錯誤，您可以明確叫用透過轉型轉換運算子，明確執行型別。 例如，您可以變更如下的程式碼：

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

變更為以下程式碼：

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

保留這些類型的選擇性值的屬性現在會明確輸入為可為 null 上，因此它們會繼續為選擇性。

### <a name="removed-facetresults-and-hithighlights"></a>已移除的 FacetResults 和 HitHighlights

`FacetResults`和`HitHighlights`類別已移除。 面向結果現在會輸入成`IDictionary<string, IList<FacetResult>>`並點擊反白顯示為`IDictionary<string, IList<string>>`。 解決建置錯誤，這項變更所引進的快速方法是新增`using`頂端移除的類型會使用每個檔案的別名。 例如：

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>將 synonymmap 新增 

`SynonymMap` 建構函式不再具有 `SynonymMapFormat` 的參數 `enum`。 這個 enum 只有一個值，因此是多餘的。 如果您看到因此發生的建置錯誤，只需移除對 `SynonymMapFormat` 參數的參考即可。

### <a name="miscellaneous-model-class-changes"></a>其他的模型類別變更

`AutocompleteMode`屬性`AutocompleteParameters`不再可為 null。 如果您已指派此屬性的程式碼`null`、 您可以直接將它移除和設為預設值，自動初始化的屬性。

參數的順序`IndexAction`建構函式已變更，現在這個建構函式會自動產生。 而不是使用建構函式，我們建議使用的 factory 方法`IndexAction.Upload`， `IndexAction.Merge`，依此類推。

### <a name="removed-preview-features"></a>已移除的預覽功能

如果您從版本 8.0 預覽版升級至 9 版，請注意，使用客戶管理金鑰的加密已經移除，因為這項功能仍處於預覽狀態。 具體而言，`EncryptionKey`的屬性`Index`和`SynonymMap`已移除。

如果您的應用程式具有硬式相依性，這項功能，您將無法升級至 Azure 搜尋服務.NET sdk 版本 9。 您可以繼續使用版本 8.0-preview。 但請記住，**我們不建議在實際執行應用程式中使用預覽 SDK**。 預覽功能僅供評估，可能會變更。

> [!NOTE]
> 如果您建立加密索引或同義字對應使用版本 8.0 預覽版的 sdk，您仍然可以使用它們，並修改使用 9 版的 sdk，而不會影響及其加密狀態及其定義。 不會將傳送的 SDK 版本 9`encryptionKey`屬性至 REST API 和 REST API 的結果不會變更資源的加密狀態。 

### <a name="behavioral-change-in-data-retrieval"></a>運作方式的變更資料擷取

如果您使用 「 動態具型別 」 `Search`， `Suggest`，或`Get`Api，傳回類型的執行個體`Document`，注意它們現在還原序列化至空的 JSON 陣列`object[]`而不是`string[]`。

## <a name="conclusion"></a>結論
如需更多有關使用 Azure 搜尋服務 .NET SDK 的詳細資料，請參閱 [.NET 做法](search-howto-dotnet-sdk.md)。

歡迎您提供 SDK 的意見反應。 如果您遇到問題，歡迎說明上尋求[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search)。 如果您發現錯誤，您可以在 [Azure .NET SDK GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/issues)中提出問題。 請務必在問題的標題前面加上「[Azure 搜尋服務]」前置詞。

感謝您使用 Azure 搜尋服務！
