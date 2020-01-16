---
title: 符合模式和特殊字元
titleSuffix: Azure Cognitive Search
description: 使用萬用字元和前置詞查詢，以符合 Azure 認知搜尋查詢要求中的整個或部分詞彙。 包含特殊字元的難以比對模式可以使用完整查詢語法和自訂分析器來加以解析。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ec1422d03cce78bdd8206f6687a78b63ddf989dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989614"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>符合模式和特殊字元（虛線）

對於包含特殊字元的查詢（`-, *, (, ), /, \, =`），或針對以較大詞彙中的部分詞彙為基礎的查詢模式，通常需要額外的設定步驟，以確保索引包含正確格式的預期內容。 

根據預設，電話號碼（例如 `+1 (425) 703-6214`）會標記為 `"1"`、`"425"`、`"703"`、`"6214"`。 如您所見，搜尋 `"3-62"`，包含虛線的部分詞彙將會失敗，因為該內容實際上不存在於索引中。 

當您需要搜尋部分字串或特殊字元時，您可以使用自訂分析器（在較簡單的 token 化規則下操作）來覆寫預設分析器，保留整個詞彙，當查詢字串包含某個詞彙或特殊的部分時，則為必要項長度. 回頭執行，此方法看起來像這樣：

+ 選擇預先定義的分析器，或定義會產生所需輸出的自訂分析器
+ 將分析器指派給欄位
+ 建立索引並測試

這篇文章會引導您完成這些工作。 這裡所述的方法在其他案例中很有用：萬用字元和正則運算式查詢也需要整個詞彙做為模式比對的基礎。 

> [!TIP]
> 評估 analyers 是一種反復的程式，需要經常重建索引。 您可以使用 Postman、用於[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)、[刪除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[載入檔](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)和[搜尋檔](https://docs.microsoft.com/rest/api/searchservice/search-documents)的 REST api，讓此步驟更容易。 針對載入檔，要求本文應該包含您想要測試的小型代表資料集（例如，具有電話號碼或產品代碼的欄位）。 使用這些 Api 在相同的 Postman 集合中，您可以快速地迴圈執行這些步驟。

## <a name="choosing-an-analyzer"></a>選擇分析器

選擇會產生整個詞彙標記的分析器時，下列分析器是常見的選擇：

| 分析器 | 行為 |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 整個欄位的內容會標記為單一詞彙。 |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 僅分隔空白字元。 包含破折號或其他字元的詞彙會被視為單一標記。 |
| [自訂分析器](index-add-custom-analyzers.md) | 使用建立自訂分析器可讓您指定 tokenizer 和權杖篩選器。 先前的分析器必須以相同的方式使用。 自訂分析器可讓您挑選要使用的 token 化工具和權杖篩選器。 <br><br>建議的組合是使用[較低案例標記篩選](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)的[關鍵字 tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) 。 預先定義的[關鍵字分析器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)本身並不會有任何大寫的文字，這可能會導致查詢失敗。 自訂分析器會提供一個機制，讓您新增小寫標記篩選器。 |

如果您使用 Web API 測試控管（例如 Postman），您可以加入[測試分析器 REST 呼叫](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)來檢查 token 化的輸出。 假設現有的索引和包含破折號或部分詞彙的欄位，您可以嘗試各種不同的分析器，以查看所發出的權杖。  

1. 檢查標準分析器，以查看預設如何將詞彙標記化。

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 評估回應，以瞭解如何在索引內標記文字。 請注意，每個詞彙的大小寫都是小寫並加以分解。

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. 修改要求以使用 `whitespace` 或 `keyword` 分析器：

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 現在回應是由大寫的單一 token 所組成，並保留以破折號做為字串的一部分。 如果您需要搜尋某個模式或部分詞彙，則查詢引擎現在具有尋找相符項的基礎。


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> 請注意，查詢剖析器在建立查詢樹狀結構時，通常會在搜尋運算式中以小寫的詞彙區分。 如果您使用的分析器不是小寫的文字輸入，而且您沒有收到預期的結果，這可能是原因。 解決方案是新增 lwower 案例權杖篩選器。

## <a name="analyzer-definitions"></a>分析器定義
 
無論您正在評估分析器或使用特定設定繼續進行，都必須在欄位定義上指定分析器，如果您不是使用內建的分析器，也可能設定分析器本身。 交換分析器時，您通常需要重建索引（drop、rebuild 和 reload）。 

### <a name="use-built-in-analyzers"></a>使用內建分析器

內建或預先定義的分析器可以在欄位定義的 `analyzer` 屬性上依名稱指定，不需要在索引中進行其他設定。 下列範例示範如何在欄位上設定 `whitespace` 分析器。

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```
如需所有可用內建分析器的詳細資訊，請參閱[預先定義的分析器清單](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)。 

### <a name="use-custom-analyzers"></a>使用自訂分析器

如果您使用的是[自訂分析器](index-add-custom-analyzers.md)，請在索引中，使用 tokenizer，tokenfilter 的使用者定義組合來定義它，並提供可能的設定。 接下來，在欄位定義上參考它，就如同您在內建的分析器一樣。

當目標是整個詞彙的 token 化時，建議使用包含**關鍵字 tokenizer**和**小寫標記篩選準則**的自訂分析器。

+ 關鍵字 tokenizer 會針對整個欄位內容建立單一 token。
+ 小寫標記篩選會將大寫字母轉換成小寫文字。 查詢剖析器通常會以小寫的形式輸入任何大寫文字。 小寫會 homogenizes 具有 token 化詞彙的輸入。

下列範例說明可提供關鍵字 tokenizer 和小寫標記篩選的自訂分析器。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
]

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> 系統會知道 `keyword_v2` tokenizer 和 `lowercase` token 篩選器，並使用其預設設定，這就是為什麼您可以依名稱參考它們，而不需要先定義它們。

## <a name="tips-and-best-practices"></a>祕訣和最佳作法

### <a name="tune-query-performance"></a>微調查詢效能

如果您執行建議的設定，其中包含 keyword_v2 tokenizer 和小寫的權杖篩選器，您可能會注意到查詢效能降低的原因，是因為您的索引中現有的權杖有額外的權杖篩選器處理。 

下列範例會新增[EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) ，以加快前置詞比對的速度。 會針對包含下列字元的2-25 個字元組合產生額外的權杖：（不只是 MS、MSF、MSFT、MSFT/、MSFT/S、MSFT/SQ、MSFT/SQL）。 您可以想像，額外的 token 化會產生較大的索引。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
]

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>使用不同的分析器進行索引編制和查詢處理

在編制索引期間和查詢執行期間，會呼叫分析器。 兩者都使用相同的分析器，但是您可以為每個工作負載設定自訂分析器。 分析器覆寫是在 `analyzers` 區段的[索引定義](https://docs.microsoft.com/rest/api/searchservice/create-index)中指定，然後在特定欄位上參考。 

只有在編制索引期間才需要自訂分析時，您可以套用自訂分析器，只針對查詢編制索引並繼續使用標準 Lucene 分析器（或其他分析器）。

若要指定角色特定分析，您可以在欄位上設定每一個的屬性，設定 `indexAnalyzer` 和 `searchAnalyzer`，而不是預設的 `analyzer` 屬性。

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>不同案例的重複欄位

另一個選項會利用每個欄位的分析器指派，針對不同的案例進行優化。 具體而言，您可能會定義 "featureCode" 和 "featureCodeRegex"，以支援第一個的一般全文檢索搜尋，以及第二個的先進模式比對。

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>後續步驟

本文說明分析器如何影響查詢問題，以及如何解決查詢問題。 在下一個步驟中，請仔細查看分析器對編制索引和查詢處理的影響。 特別是，請考慮使用「分析文字 API」來傳回已標記的輸出，讓您可以確切查看分析器為您的索引建立的內容。

+ [語言分析器](search-language-support.md)
+ [Azure 認知搜尋中的文字處理分析器](search-analyzers.md)
+ [分析文字 API （REST）](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [全文檢索搜尋的運作方式（查詢架構）](search-lucene-query-architecture.md)