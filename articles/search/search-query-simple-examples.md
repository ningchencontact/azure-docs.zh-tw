---
title: Azure 搜尋服務的簡單查詢範例 | Microsoft Docs
description: 全文檢索搜尋、篩選搜尋、地理搜尋、多面向搜尋，和其他用來查詢 Azure 搜尋服務索引的查詢字串適用的簡單查詢範例。
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: heidist
ms.openlocfilehash: e4bb72eb8ad6f15b0e5bc14e0e07556e76d0477b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368596"
---
# <a name="simple-syntax-query-examples-for-building-queries-in-azure-search"></a>在 Azure 搜尋服務中建置查詢的簡單語法查詢範例

[簡單查詢語法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)會叫用預設查詢剖析器，以對 Azure 搜尋服務索引執行全文檢索搜尋查詢。 簡單查詢分析器速度快，可處理 Azure 搜尋服務中的常見案例，包括全文檢索搜尋、篩選搜尋、多面向搜尋和地理搜尋。 本文中的範例示範在使用簡單語法時可用的查詢作業，請逐步執行這些範例。

替代的查詢語法是[完整 Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)，可支援如模糊和萬用字元搜尋等較複雜的查詢結構，而其處理時間可能會比較久。 如需詳細資訊和示範完整語法的範例，請參閱 [Lucene 語法查詢範例](search-query-lucene-examples.md)。

## <a name="formulate-requests-in-postman"></a>以 Postman 編寫要求

下列範例會根據 [紐約市 OpenData](https://nycopendata.socrata.com/) 計劃所提供的資料集，利用由可用工作組成的 NYC 工作搜尋索引。 這項資料不應視為目前的或已完成。 此索引屬於 Microsoft 所提供的沙箱服務，這表示您不需要 Azure 訂用帳戶或 Azure 搜尋服務，即可嘗試執行這些查詢。

您的需要是 Postman，或可對 GET 發出 HTTP 要求的對等工具。 如需詳細資訊，請參閱[使用 REST 用戶端測試](search-fiddler.md)。

### <a name="set-the-request-header"></a>設定要求標頭

1. 在要求標頭中，將 **Content-Type** 設定為 `application/json`。

2. 新增 **api-key**，並將其設定為此字串：`252044BE3886FE4A8E3BAA4F595114BB`。 這是裝載 NYC 工作索引的沙箱搜尋服務所適用的查詢金鑰。

在指定要求標頭後，您可以將其重複用於本文中的所有查詢，只要替換掉 **search=** 字串即可。 

  ![Postman 要求標頭](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>設定要求 URL

要求是與包含 Azure 搜尋服務端點和搜尋字串的 URL 成對的 GET 命令。

  ![Postman 要求標頭](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL 組合具有下列元素：

+ **`https://azs-playground.search.windows.net/`** 是由 Azure 搜尋服務開發小組維護的沙箱搜尋服務。 
+ **`indexes/nycjobs/`** 是該服務的索引集合中包含的 NYC 工作索引。 要求上必須同時有服務名稱和索引。
+ **`docs`** 是包含所有可搜尋內容的文件集合。 要求標頭中提供的查詢 API 金鑰僅適用於以文件集合為目標的讀取作業。
+ **`api-version=2017-11-11`** 會設定 API 版本，這是每個要求的必要參數。
+ **`search=*`** 是查詢字串，在初始查詢中設為 Null，會傳回前 50 個結果 (根據預設)。

## <a name="send-your-first-query"></a>傳送第一個查詢

在驗證步驟中，將下列要求貼到 GET 中，然後按一下 [傳送]。 結果會以詳細 JSON 文件的形式傳回。 您可以將此 URL 複製並貼到下方的第一個範例中。

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=*
  ```

查詢字串 **`search=*`** 是未指定的搜尋，等同於 Null 或空的搜尋。 其功用並不高，卻是最方便執行的搜尋。

(選擇性) 您可以將 **`$count=true`** 新增至 URL，以傳回符合搜尋準則的文件計數。 在空的搜尋字串上，這是索引中的所有文件 (在 NYC 工作的案例中為 2802)。

## <a name="how-to-invoke-simple-query-parsing"></a>如何叫用簡單查詢剖析

針對互動式查詢，您不需要指定任何項目：簡單是預設值。 在程式碼中，如果您先前叫用 **queryType=full** 而使用完整查詢語法，您可以透過 **queryType=simple** 重設為預設值。

## <a name="example-1-field-scoped-query"></a>範例 1：欄位範圍查詢

第一個查詢並不會隨語法而不同 (此查詢同時適用於簡單和完整語法)，但我們首先舉出此例，為的是要介紹可產生易讀 JSON 回應的基準查詢概念。 為求簡潔，查詢僅以 *business_title* 欄位為目標，且指定僅傳回公司職稱。 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

**searchFields** 參數會將搜尋限制在商務標題欄位。 **select** 參數會決定要包含在結果集中的欄位。

此查詢的回應會如下列螢幕擷取畫面所示。

  ![Postman 範例回應](media/search-query-lucene-examples/postman-sample-results.png)

您可能已經注意到，即使未指定搜尋分數，仍會傳回每個文件的搜尋分數。 這是因為搜尋分數是中繼資料，具有可指出結果排名順序的值。 沒有排名時，分數一律為 1，這是因為搜尋不是全文檢索搜尋，或是沒有可套用的準則。 Null 搜尋沒有任何準則，且資料列會以任意順序傳回。 隨著搜尋準則取用更多定義時，您會發現搜尋分數逐漸具有其實質意義。

## <a name="example-2-look-up-by-id"></a>範例 2︰依識別碼查閱

此範例有點不規則，但在評估搜尋行為時，您可能想要查看某個文件的完整內容，以了解結果為何會包含或排除該文件。 若要傳回整份文件，請使用[查閱作業](https://docs.microsoft.com/rest/api/searchservice/lookup-document)傳入文件識別碼。

所有文件都有唯一識別碼。 若要嘗試使用查閱查詢的語法，請先傳回文件識別碼清單，以尋找您要使用的文件。 NYC 工作的識別碼會儲存在 `id` 欄位中。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
 ```

下一個範例是根據 `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5" (出現在上述回應中的最上方) 傳回特定文件的查閱查詢。 下列查詢會傳回整份文件，而不只是選取的欄位。 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
 ```

## <a name="example-3-search-precision"></a>範例 3︰搜尋精準度

字詞查詢是個別評估的單一字詞 (可能有許多個)。 片語查詢會以引號括住，並以逐字字串的形式評估。 比對的精確度由運算子和 searchMode 所控制。

範例 1︰**`&search=fire`** 傳回 150 個結果，其中，所有相符項目皆在文件中的某處包含 word 一字。

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

範例 2︰**`&search=fire department`** 傳回 2002 個結果。 系統會針對包含 fire 或 department 的文件傳回相符項目。

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

範例 3︰**`&search="fire department"`** 傳回 82 個結果。 以引號括住字串時會對這兩個字詞進行逐字搜尋，並從索引中包含此組合字詞的權杖化字詞尋找相符項目。 這解釋了為何 **`search=+fire +department`** 這樣的搜尋並不相同。 這兩個字詞都必須存在，但兩者的掃描會個別執行。 

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-4-booleans-with-searchmode"></a>範例 4︰使用 searchMode 的布林值

簡單語法支援字元格式 (`+, -, |`) 的布林運算子。 SearchMode 參數會指出精確度與召回率之間的取捨；`searchMode=any` 會以召回率優先 (符合任何準則即可將文件納入結果集內)，而 `searchMode=all` 則以精確度優先 (必須符合所有準則)。 預設值為 `searchMode=any`，這在以多個運算子堆疊出查詢而取得範圍較廣 (而非範圍較窄) 的結果時，可能會令人混淆。 在使用 NOT 更是如此，因為其結果會包含所有「不含」特定字詞的文件。

使用預設 searchMode (any) 會傳回 2800 份文件：包含複合字詞 "fire department" 的文件，以及所有不含 "Metrotech Center" 一詞的文件。

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```
將 searchMode 變更為 `all` 時，會對準則強制執行累加效果，而傳回較小的結果集 (21 份文件)，由包含完整片語 "fire department"、但工作地點不是 Metrotech Center 的文件所組成。

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```


## <a name="example-5-structuring-results"></a>範例 5︰建構結果

有數個參數會控制要在搜尋結果中包含的欄位、每個批次中傳回的文件數目，以及排序次序。 此範例將從前述幾個範例延伸，使用 **$select** 陳述式和逐字搜尋準則將結果限定於特定欄位，而傳回 82 個相符項目 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
接續先前的範例，您可以依職稱排序。 之所以可進行此排序，是因為 civil_service_title 在索引中是*可排序的*。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

分頁結果會使用 **$top** 參數來實作，在此範例中會傳回前 5 份文件：

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

若要取得後續的 5 份文件，請略過第一個批次：

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>後續步驟
嘗試在您的程式碼中指定查詢。 下列連結說明如何使用預設的簡單語法設定 .NET 和 REST API 的搜尋查詢。

* [使用 .NET SDK 查詢 Azure 搜尋服務索引](search-query-dotnet.md)
* [使用 REST API 查詢 Azure 搜尋服務索引](search-query-rest-api.md)

您可以在下列連結中找到其他語法參考、查詢架構和範例：

+ [建置進階查詢的 Lucene 語法查詢範例](search-query-lucene-examples.md)
+ [全文檢索搜尋如何在 Azure 搜尋服務中運作](search-lucene-query-architecture.md)
+ [簡單查詢語法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [完整 Lucene 查詢](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
