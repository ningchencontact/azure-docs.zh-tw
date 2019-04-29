---
title: 快速入門：建立、 載入及查詢使用 PowerShell 和 REST API-Azure 搜尋服務索引
description: 建立、 載入和使用 PowerShell 的查詢索引 Invoke-restmethod 」 和 「 Azure 搜尋服務 REST API。
ms.date: 04/08/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 2deba4bf941d561fcef7c2dff804646732e7ce24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817149"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell-and-the-rest-api"></a>快速入門：建立使用 PowerShell 和 REST API 的 Azure 搜尋服務索引
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [入口網站](search-create-index-portal.md)
> 

本文將逐步引導您完成建立、 載入和查詢 Azure 搜尋服務的程序[index](search-what-is-an-index.md)使用 PowerShell 和[Azure 搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice/)。 索引定義和可搜尋的內容是在要求主體中提供，做為格式正確的 JSON 內容。

## <a name="prerequisites"></a>必要條件

本快速入門會使用下列服務和工具。 

[建立 Azure 搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。 

[PowerShell 5.1 或更新版本](https://github.com/PowerShell/PowerShell)，並使用[Invoke-restmethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod)循序和互動式步驟。

## <a name="get-a-key-and-url"></a>取得金鑰和 URL

REST 呼叫需要服務 URL 和每個要求的存取金鑰。 搜尋服務是同時建立，因此如果您將 Azure 搜尋服務新增至您的訂用帳戶，請遵循下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀] 頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

2. 在 [設定]  >  [金鑰] 中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

![取得 HTTP 端點和存取金鑰](media/search-fiddler/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="connect-to-azure-search"></a>連線到 Azure 搜尋服務

在 PowerShell 中，建立 **$headers**物件來儲存內容類型和 API 金鑰。 您只需要工作階段期間，一次設定此標頭，但您會將它新增至每個要求。 

```powershell
$headers = @{
   'api-key' = '<your-admin-api-key>'
   'Content-Type' = 'application/json' 
   'Accept' = 'application/json' }
```

建立 **$url**物件，指定服務的編製索引的集合。 `mydemo`服務名稱是作為預留位置。 以有效的搜尋服務中目前的訂用帳戶，在本範例裡加以取代。

```powershell
$url = "https://mydemo.search.windows.net/indexes?api-version=2017-11-11"
```

執行**Invoke-restmethod** GET 要求傳送至服務，並驗證連接。 新增**Convertto-json** ，讓您可以檢視傳送從服務傳回的回應。

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

如果服務是空的且沒有索引，結果會類似下列的範例。 否則，您會看到索引定義的 JSON 表示法。

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
    "value":  [

              ]
}
```

## <a name="1---create-an-index"></a>1 - 建立索引

除非您使用入口網站，索引上必須有服務之前，您可以載入資料。 此步驟中定義的索引，並將其推送至服務。 [建立索引 (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)使用於此步驟。

索引的必要項目包含一個名稱和欄位的集合。 Fields 集合定義的結構*文件*。 每個欄位都有名稱、 類型和屬性，以決定其使用方式 (例如，它是否全文檢索搜尋、 可篩選，或可在搜尋結果中擷取)。 索引，其中一個類型的欄位內`Edm.String`您必須指定為*金鑰*文件的身分識別。

這個索引名為"hotels"，並具有您在下方看到的欄位定義。 索引定義會指定[語言分析器](index-add-language-analyzers.md)如`description_fr`欄位，因為它用來儲存法文文字，後面的範例中，我們將新增。

此範例中貼上 PowerShell 來建立 **$body**物件，其中包含索引結構描述。

```powershell
$body = @"
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
"@
```

設定您的服務上的索引集合的 URI 和*旅館*索引。

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11"
```

執行命令並搭配 **$url**， **$headers**，並 **$body**服務上建立索引。 

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
```
結果看起來應該像這樣 （截斷為求簡單明瞭的前兩個欄位）：

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag":  "\"0x8D6A99E2DED96B0\"",
    "name":  "hotels",
    "defaultScoringProfile":  null,
    "fields":  [
                   {
                       "name":  "hotelId",
                       "type":  "Edm.String",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  false,
                       "facetable":  false,
                       "key":  true,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
                   {
                       "name":  "baseRate",
                       "type":  "Edm.Double",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  true,
                       "facetable":  true,
                       "key":  false,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
. . .
```

> [!Tip]
> 進行驗證，可能也檢查索引清單，在入口網站中，或重新執行用來驗證服務連線的命令，請參閱*旅館*索引集合中所列的索引。

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 載入文件

若要推送的文件，使用 HTTP POST 要求至您的索引 URL 端點。 這項工作的 REST api[新增、 更新或刪除文件](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)。

此範例中貼上 PowerShell 來建立 **$body**物件，其中包含您想要上傳的文件。 

此要求包含兩個完整和部分的一筆記錄。 部分記錄示範您可以上傳不完整的文件。 `@search.action`參數會指定如何完成編製索引。 有效值包括上傳、 合併、 mergeOrUpload 及刪除。 MergeOrUpload 行為建立新的文件，如 hotelId = 3，或更新的內容，如果已經存在。

```powershell
$body = @"
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        }
    ]
}
"@
```

若要設定端點*旅館*文件集合並包含在索引作業 （索引/hotels/docs/索引）。

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11"
```

執行命令並搭配 **$url**， **$headers**，並 **$body**載入 hotels 索引的文件。

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
```
結果看起來應該類似下列的範例。 您應該會看到狀態碼 201。 如需所有的狀態碼的說明，請參閱 < [（Azure 搜尋服務） 的 HTTP 狀態碼](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)。

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2017_11_11.IndexResult)",
    "value":  [
                  {
                      "key":  "1",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  },
                  {
                      "key":  "2",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  },
                  {
                      "key":  "3",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  }
              ]
}
```

## <a name="3---search-an-index"></a>3 - 搜尋索引

此步驟會說明如何使用索引來進行查詢[搜尋文件 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

端點設定為*旅館*文件集合，並新增**搜尋**包含查詢字串參數。 此字串是空的搜尋，並傳回所有文件的 unranked 的清單。

```powershell
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*'
```

執行命令，以傳送 **$url**至服務。

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

結果看起來應該類似下列的輸出。

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#docs(*)",
    "value":  [
                  {
                      "@search.score":  1.0,
                      "hotelId":  "1",
                      "baseRate":  199.0,
                      "description":  "Best hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Fancy Stay",
                      "category":  "Luxury",
                      "tags":  "pool view wifi concierge",
                      "parkingIncluded":  false,
                      "smokingAllowed":  false,
                      "lastRenovationDate":  "2010-06-27T00:00:00Z",
                      "rating":  5,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "2",
                      "baseRate":  79.99,
                      "description":  "Cheapest hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Roach Motel",
                      "category":  "Budget",
                      "tags":  "motel budget",
                      "parkingIncluded":  true,
                      "smokingAllowed":  true,
                      "lastRenovationDate":  "1982-04-28T00:00:00Z",
                      "rating":  1,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "3",
                      "baseRate":  129.99,
                      "description":  "Close to town hall and the river",
                      "description_fr":  null,
                      "hotelName":  null,
                      "category":  null,
                      "tags":  "",
                      "parkingIncluded":  null,
                      "smokingAllowed":  null,
                      "lastRenovationDate":  null,
                      "rating":  null,
                      "location":  null
                  }
              ]
}
```

請嘗試幾個其他查詢範例，以概略了語法。 您可以執行字串搜尋，逐字 $filter 查詢，結果集，而範圍內搜尋特定欄位，以及其他限制。

```powershell
# Query example 1
# Search the entire index for the term 'budget'
# Return only the `hotelName` field, "Roach hotel"
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=budget&$select=hotelName'

# Query example 2 
# Apply a filter to the index to find hotels cheaper than $150 per night
# Returns the `hotelId` and `description`. Two documents match.
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$filter=baseRate lt 150&$select=hotelId,description'

# Query example 3
# Search the entire index, order by a specific field (`lastRenovationDate`) in descending order
# Take the top two results, and show only `hotelName` and `lastRenovationDate`
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate'
```
## <a name="clean-up"></a>清除 

如果您不再需要您應該刪除索引。 一項免費服務僅限於三個索引。 您可能想要刪除您未主動使用，以便您可以逐步執行其他教學課程的任何索引。

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>後續步驟

請嘗試加入索引中的法文的描述。 下列範例包含法文字串，並示範其他搜尋動作。 若要建立或新增至現有的欄位使用 mergeOrUpload。 下列字串必須是 utf-8 編碼。

```json
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "1",
            "description_fr": "Meilleur hôtel en ville"
        },
        {
            "@search.action": "merge",
            "hotelId": "2",
            "description_fr": "Hôtel le moins cher en ville"
        }
    ]
}
```
