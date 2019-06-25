---
title: 可使用索引子用於自動編製索引的欄位對應 - Azure 搜尋服務
description: 設定 Azure 搜尋服務索引子欄位對應，以處理欄位名稱和資料表示法的差異。
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147800"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>欄位對應和使用 Azure 搜尋服務索引子的轉換

在使用 Azure 搜尋服務索引子時，您有時候會發現輸入的資料不完全符合目標索引的結構描述。 在這些情況下，您可以使用**欄位對應**重新編製索引的程序期間調整您的資料。

欄位對應在某些情況下很有用︰

* 您的資料來源有一個名為欄位`_id`，但 Azure 搜尋服務不允許以底線開頭的欄位名稱。 欄位對應可讓您有效地重新命名欄位。
* 您想要填入索引，從相同的資料來源資料中的數個欄位。 比方說，您可能想要將不同的分析器套用到這些欄位。
* 您想要填入索引欄位從一個以上的資料來源的資料和資料來源各使用不同的欄位名稱。
* 您必須以 Base64 格式編碼或解碼資料。 欄位對應支援數個 **對應函式**，包括 Base64 編碼和解碼的函式。

> [!NOTE]
> Azure 搜尋服務索引子的欄位對應功能提供簡單的方式，將資料欄位對應至索引的欄位，以進行資料轉換的幾個選項。 更複雜的資料可能需要重新調整成容易索引的表單前置處理。
>
> Microsoft Azure Data Factory 是功能強大雲端式解決方案，來匯入和轉換資料。 您也可以撰寫程式碼來轉換來源資料編製索引之前。 程式碼範例，請參閱[關聯式資料模型](search-example-adventureworks-modeling.md)並[模型多層次 facet](search-example-adventureworks-multilevel-faceting.md)。
>

## <a name="set-up-field-mappings"></a>設定欄位對應

欄位對應包含三個部分︰

1. `sourceFieldName`，表示資料來源中的欄位。 這是必要屬性。
2. 選擇性的 `targetFieldName`，表示搜尋索引中的欄位。 如果省略，則會使用資料來源中的相同名稱。
3. 選擇性的 `mappingFunction`，可以使用數個預先定義的函式之一轉換您的資料。 函式的完整清單 [如下](#mappingFunctions)。

欄位對應會新增至`fieldMappings`陣列索引子定義。

## <a name="map-fields-using-the-rest-api"></a>使用 REST API 的對應欄位

建立新的索引子使用時，您可以新增欄位對應[建立索引子](https://docs.microsoft.com/rest/api/searchservice/create-Indexer)API 要求。 您可以管理現有的索引子使用的欄位對應[更新索引子](https://docs.microsoft.com/rest/api/searchservice/update-indexer)API 要求。

例如，以下是如何將來源欄位對應至目標欄位具有不同的名稱：

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

來源欄位，可參考多個欄位對應。 下列範例示範 「 分岔 」 欄位中，將相同的來源欄位複製到兩個不同的索引欄位的方法：

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure 搜尋服務會使用不區分大小寫的比較，來解析欄位對應的欄位和函式名稱。 這很方便 (大小寫不需要完全正確)，但這表示資料來源或索引不能有只以大小寫區分的欄位。  
>
>

## <a name="map-fields-using-the-net-sdk"></a>使用.NET SDK 的欄位對應

您定義在.NET SDK 中使用的欄位對應[FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping)類別，其中包含的屬性`SourceFieldName`並`TargetFieldName`，以及選用`MappingFunction`參考。

您可以指定欄位對應，當建構索引子，或稍後藉由直接設定`Indexer.FieldMappings`屬性。

下列C#範例會建構索引子時，將設定欄位對應。

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>欄位對應函式

欄位對應函式轉換欄位的內容，然後再儲存在索引中。 目前支援下列的對應函式：

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>進行 base64encode 處理函式

執行輸入字串的安全 URL  Base64 編碼。 假設輸入以 UTF-8 編碼。

#### <a name="example---document-key-lookup"></a>範例-文件索引鍵查閱

Azure 搜尋服務文件索引鍵顯示只有安全的 URL 字元 (因為客戶必須能夠處理文件使用，所以[查閱 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) )。 如果您的金鑰的來源欄位包含不安全 URL 字元，您可以使用`base64Encode`將在建立索引時的函式。

當您在搜尋時擷取已編碼的金鑰時，然後您可以使用`base64Decode`函式收到的原始索引鍵值，並使用它來擷取來源文件。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

如果您未包含參數屬性，您的對應函式，則預設值為`{"useHttpServerUtilityUrlTokenEncode" : true}`。

Azure 搜尋服務支援兩種不同的 Base64 編碼。 您應該使用相同的參數時編碼和解碼的相同欄位。 如需詳細資訊，請參閱 < [base64 編碼選項](#base64details)決定要使用哪些參數。

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode 函式

執行輸入字串的 Base64 解碼。 輸入會假設*URL 安全*Base64 編碼的字串。

#### <a name="example---decode-blob-metadata-or-urls"></a>範例-解碼 blob 中繼資料或 Url

您的來源資料可能會包含 Base64 編碼的字串，例如 blob 中繼資料字串或 web Url，您想要讓使用者可搜尋以純文字。 您可以使用`base64Decode`編碼的資料轉換回成一般字串時填入搜尋索引的函式。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

如果您未包含參數的屬性，則預設值為`{"useHttpServerUtilityUrlTokenEncode" : true}`。

Azure 搜尋服務支援兩種不同的 Base64 編碼。 您應該使用相同的參數時編碼和解碼的相同欄位。 如需詳細資訊，請參閱 < [base64 編碼選項](#base64details)決定要使用哪些參數。

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64 編碼選項

Azure 搜尋服務支援兩種不同的 Base64 編碼：**HttpServerUtility URL 權杖**，並**無填補的 URL 安全 Base64 編碼**。 稍後要解碼為編製索引期間的 base64 編碼的字串使用相同的編碼選項，否則結果不會符合原始。

如果`useHttpServerUtilityUrlTokenEncode`或是`useHttpServerUtilityUrlTokenDecode`分別編碼和解碼的參數會設定為`true`，然後`base64Encode`行為就像[HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)和`base64Decode`行為類似[HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx)。

如果您不想要使用完整的.NET Framework （也就您使用.NET Core 或另一個架構） 若要產生的索引鍵值，以模擬 Azure 搜尋服務行為，則您應該設定`useHttpServerUtilityUrlTokenEncode`並`useHttpServerUtilityUrlTokenDecode`至`false`。 根據您所使用的程式庫，base64 編碼和解碼函式可能不同於 Azure 搜尋服務所使用的項目。

下表比較 `00>00?00` 字串的不同 base64 編碼。 若要判斷您的 base64 函式需要的額外處理 (如果有的話)，將您的程式庫編碼函式套用在 `00>00?00` 字串，然後比較輸出與預期的輸出 `MDA-MDA_MDA`。

| 編碼 | Base64 編碼的輸出 | 程式庫編碼之後的額外處理 | 程式庫解碼之前的額外處理 |
| --- | --- | --- | --- |
| Base64 加填補 | `MDA+MDA/MDA=` | 使用 URL 安全的字元，並移除填補 | 使用標準 base64 字元，並加上填補 |
| Base64 無填補 | `MDA+MDA/MDA` | 使用 URL 安全的字元 | 使用標準 base64 字元 |
| URL 安全的 base64 填補加填補 | `MDA-MDA_MDA=` | 移除填補 | 加上填補 |
| URL 安全的 base64 無填補 | `MDA-MDA_MDA` | None | None |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition 函式

使用指定的分隔符號分割字串欄位，並在分割結果的指定位置挑選權杖。

此函式會使用下列參數：

* `delimiter`︰分割輸入字串時，用為分隔符號的字串。
* `position`：分割輸入字串後，要挑選的權杖以零為基底位置的整數。

例如，如果輸入是 `Jane Doe`，而 `delimiter` 是 `" "` (空格) 且 `position` 為 0，則結果是 `Jane`；如果 `position` 為 1，則結果是 `Doe`。 如果位置參考不存在的權杖，會傳回錯誤。

#### <a name="example---extract-a-name"></a>範例-擷取的名稱

資料來源包含 `PersonName` 欄位，而您想要將它編製為 `FirstName` 和 `LastName` 兩個不同欄位的索引。 您可以使用這個函式來分割以空格字元作為分隔符號的輸入。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection 函式

將格式化為字串 JSON 陣列的字串，轉換為可用來填入索引中 `Collection(Edm.String)` 欄位的字串陣列。

例如，輸入字串是 `["red", "white", "blue"]`，則 `Collection(Edm.String)` 類型的目標欄位會填入 `red`、`white`、`blue` 三個值。 若為無法剖析為 JSON 字串陣列的輸入值，會傳回錯誤。

#### <a name="example---populate-collection-from-relational-data"></a>範例-從關聯式資料的集合中填入

Azure SQL Database 沒有自然對應至內建資料型別`Collection(Edm.String)`Azure 搜尋服務中的欄位。 若要填入字串集合欄位，您可以預先處理您的來源資料以 JSON 字串陣列，然後使用`jsonArrayToStringCollection`對應函式。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

將關聯式資料轉換成索引集合欄位的詳細範例，請參閱[模型關聯式資料](search-example-adventureworks-modeling.md)。
