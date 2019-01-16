---
title: 在 Azure 入口網站中使用搜尋總管查詢索引 - Azure 搜尋服務
description: 如何在 Azure 搜尋服務中使用搜尋總管之類的 Azure 入口網站工具來查詢索引。 輸入搜尋字詞或完全符合條件的進階語法搜尋字串。
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2aa372d1f917608de753007cc75ab0d608cafbba
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188720"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>如何在 Azure 搜尋服務中使用搜尋總管來查詢索引 

本文說明如何在 Azure 入口網站中使用**搜尋總管**查詢現有的 Azure 搜尋服務索引。 您可以使用搜尋總管，在您的服務中對於任何現有的索引送出簡單或完整的 Lucene 查詢字串。

## <a name="start-search-explorer"></a>開啟 [搜尋總管]

1. 在 [Azure 入口網站](https://portal.azure.com)中，從儀表板開啟搜尋服務分頁，或在服務清單中[尋找服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

2. 在 [服務概觀] 頁面中，按一下 [搜尋總管]。

   ![入口網站中的搜尋總管命令](./media/search-explorer/search-explorer-cmd2.png "入口網站中的搜尋總管命令")

3. 選取要查詢的索引。

   ![選取要查詢的索引](./media/search-explorer/search-explorer-changeindex-se2.png "選取索引")

4. (選擇性) 設定 API 版本。 根據預設，會選取目前正式運作的 API 版本，但如果想要使用的語法是特定版本，則可以選擇預覽版或舊版 API。

5. 選取索引和 API 版本後，在搜尋列中輸入搜尋字詞或完整的查詢運算式，然後按一下 [搜尋] 來執行。

   ![輸入搜尋字詞，然後按一下 [搜尋]](./media/search-explorer/search-explorer-query-string-example.png "輸入搜尋字詞，然後按一下 [搜尋]")

在 [搜尋總管] 中進行搜尋的秘訣：

+ 結果會以詳細的 JSON 文件傳回，以便您可以完整地查看文檔構造和內容。 您可以使用範例中顯示的查詢運算式來限制要傳回哪些欄位。

+ 文件是由索引中標記為**可擷取**的所有欄位組成。 若要在入口網站中檢視索引屬性，請在 [搜尋概觀] 頁面上按一下 [索引] 清單中的 realestate-us-sample。

+ 自由格式查詢類似於您在商業 Web 瀏覽器中輸入的內容，適合用於測試使用者體驗。 比方說，假設內建的房地產 範例索引，您可以輸入 "Seattle apartments lake washington"，然後您可以使用 Ctrl-F 在搜尋結果中尋找字詞。 

+ 查詢和篩選條件運算式必須以 Azure 搜尋服務所支援的語法表達。 預設值是[簡單語法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)，但您可以選擇使用[完整 Lucene 功能](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)進行更強大的查詢。 [篩選條件運算式](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)是 OData 語法。

## <a name="basic-search-strings"></a>基本搜尋字串

下列範例假設內建的房地產範例索引。 如需有關建立這個索引的詳細資訊，請參閱[快速入門：Azure 入口網站中的匯入、索引和查詢](search-get-started-portal.md)。

### <a name="example-1---empty-search"></a>範例 1 - 空的搜尋

若要率先一睹您的內容，請執行空的搜尋，方法是按一下 [搜尋] 且不提供任何字詞。 空的搜尋作為第一個查詢很有用，因為它會傳回整個文件，以便您可以檢閱文件組合。 在空的搜尋上，沒有任何搜尋排名，而且會以任意順序傳回文件 (`"@search.score": 1` 為所有文件)。 根據預設，會傳回搜尋要求中的 50 份文件。

空的搜尋的對等語法是 `*` 或 `search=*`。

   ```Input
   search=*
   ```

   **結果**
   
   ![空的查詢範例](./media/search-explorer/search-explorer-example-empty.png "不合格或空的查詢範例")

### <a name="example-2---free-text-search"></a>範例 2 - 任意文字搜尋

自由格式查詢 (包含或不包含運算子) 可用於模擬從自訂應用程式傳送至 Azure 搜尋服務的使用者定義查詢。 請注意，當您提供查詢字詞或運算式時，搜尋排名可派上用場。 以下透過範例說明任意文字搜尋。

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **結果**

   您可以使用 Ctrl-F 來搜尋結果中感興趣的特定字詞。

   ![任意文字查詢範例](./media/search-explorer/search-explorer-example-freetext.png "任意文字查詢範例")

### <a name="example-3---count-of-matching-documents"></a>範例 3 - 相符文件的計數 

新增 **$count** 以取得索引中找到的相符項目數。 在空的搜尋中，計數會是索引中的文件總數。 在限定搜尋中，它是符合查詢輸入的文件數目。

   ```Input1
   $count=true
   ```
   **結果**

   ![文件範例的計數](./media/search-explorer/search-explorer-example-count.png "索引中相符文件的計數")

### <a name="example-4---restrict-fields-in-search-results"></a>範例 4 - 限制搜尋結果中的欄位

新增 **$select** 以將結果限制為明確具名欄位，以便 [搜尋總管] 中的輸出更容易讀取。 若要保留搜尋字串和 **$count=true**，請在引數前加上 **&** 首碼。 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **結果**

   ![限制欄位範例](./media/search-explorer/search-explorer-example-selectfield.png "限制搜尋結果中的欄位")

### <a name="example-5---return-next-batch-of-results"></a>範例 5 - 傳回下一個批次的結果

Azure 搜尋服務會根據搜尋排名傳回前 50 個相符項目。 若要取得下一組相符文件，請附加 **$top=100,&$skip=50** 以將結果集增加到 100 份文件 (預設值為 50，最大值為 1000)，略過前 50 個文件。 回想一下，您必須提供搜尋準則，例如查詢字詞或運算式，以取得高排序結果。 請注意，搜尋分數會降低您在搜尋結果中的深度。

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100,&$skip=50
   ```

   **結果**

   ![批次搜尋結果](./media/search-explorer/search-explorer-example-topskip.png "傳回下一個批次的搜尋結果")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>篩選條件運算式 (大於、小於、等於)

如果要指定精確的準則而不是任意文字搜尋，請使用 **$filter** 參數。 此範例會搜尋大於 3 的臥室：`search=seattle condo&$filter=beds gt 3&$count=true`

   ![篩選運算式](./media/search-explorer/search-explorer-example-filter.png "依準則篩選")

## <a name="order-by-expressions"></a>Order-by 運算式

新增 **$orderby** 以依照搜尋分數以外的其他欄位對結果進行排序。 可用來測試此輸出的範例運算式是 `search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc`

   ![Orderby 運算式](./media/search-explorer/search-explorer-example-ordery.png "變更排序次序")

**$filter** 和 **$orderby** 運算式都是 OData 建構。 如需詳細資訊，請參閱[篩選 OData 語法](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)。

## <a name="next-steps"></a>後續步驟

下列資源提供其他的查詢語法資訊和範例。

 + [簡單查詢語法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene 查詢語法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Lucene 查詢範例](search-query-lucene-examples.md) 
 + [OData 篩選條件運算式語法](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 