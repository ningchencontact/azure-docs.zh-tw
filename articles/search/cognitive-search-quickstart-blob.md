---
title: 在 Azure 入口網站中建立認知技能集
titleSuffix: Azure Cognitive Search
description: 使用「匯入資料」精靈將認知技能新增至索引管線。 認知技能包括光學字元辨識 (OCR) 和自然語言處理。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 14d2751f55f3df26277b1210b9201eed61409c6b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792084"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立 Azure 認知搜尋的認知技能集

技能集是一項 AI 功能，可從大型的無差異文字或影像檔案中擷取資訊和結構，並使其可供編製索引和接受搜尋，以在 Azure 認知搜尋中進行全文檢索搜尋查詢。 

在本快速入門中，您將結合 Azure 雲端中的服務和資料以建立技能集。 一切都備妥之後，您即可在入口網站中執行**匯入資料**精靈，以將其全部提取。 其最終結果將是可搜尋的索引，其中填入在 AI 處理後建立、可在入口網站 ([搜尋總管](search-explorer.md)) 中查詢的資料。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-services-and-load-data"></a>建立服務並載入資料

本快速入門會使用 Azure 認知搜尋、Azure Blob 儲存體和 [Azure 認知服務](https://azure.microsoft.com/services/cognitive-services/)進行 AI 處理。 

由於工作負載很小，因此，從 Azure 認知搜尋叫用時，認知服務會在幕後連線以提供免費處理，每天最多 20 筆交易。 如果使用我們提供的範例資料，就可以略過建立或連結認知服務資源的步驟。

1. [下載範例資料](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)，其中有不同類型的小型檔案集。 將檔案解壓縮。

1. [建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)，或在您目前的訂用帳戶下方[尋找現有帳戶](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 

   選擇與 Azure 認知搜尋相同的區域。 如果您想要稍後在另一個逐步解說中試用知識存放區功能，請選擇 [StorageV2 (一般用途 V2)] 帳戶類型。 否則，請選擇任何類型。

1. 開啟 Blob 服務頁面，並建立容器。 您可以使用預設的公用存取層級。 

1. 在容器中按一下 [上傳]  ，以上傳您在第一個步驟中下載的範例檔案。 請注意，您有多種不同的內容類型，包括無法以原生格式全文檢索搜尋的影像和應用程式檔案。

   ![Azure Blob 儲存體中的來源檔案](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [建立 Azure 認知搜尋服務](search-create-service-portal.md)，或在相同訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. In many tutorials, especially those that use the REST API, you will need a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

現在您已準備就緒，可移至「匯入資料」精靈。

## <a name="run-the-import-data-wizard"></a>執行匯入資料精靈

在搜尋服務的 [概觀] 頁面中，按一下命令列上的 [匯入資料]  ，以四個步驟設定認知擴充。

  ![匯入資料命令](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>步驟 1：建立資料來源

1. 在 [連線到您的資料]  中，選擇 [Azure Blob 儲存體]  ，然後選取您建立的儲存體帳戶和容器。 指定資料來源的名稱，其餘部分則使用預設值。 

   ![Azure Blob 組態](./media/cognitive-search-quickstart-blob/blob-datasource.png)

1. 繼續進行下一頁。

### <a name="step-2-add-cognitive-skills"></a>步驟 2：新增認知技能

接著，新增認知技能以叫用自然語言處理。 範例資料包含 12 個檔案，因此認知服務20 筆交易的免費配額就足以供本快速入門使用。 由於我們不使用 OCR，因此在此程序中只會萃取和使用非影像檔案及計算其數目。

1. 在本快速入門中，我們將使用**免費**的認知服務資源。

   ![附加認知服務](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. 展開 [新增技能]  ，然後選取可執行自然語言處理的技能。 針對本快速入們，請選擇適用於人員、組織和位置的實體辨識。

   ![附加認知服務](media/cognitive-search-quickstart-blob/skillset.png)

1. 接受預設來源欄位：`content`。 您可能會覺得其涵蓋範圍不大，但 Azure Blob 的 `content` 欄位包含大部分的 Blob 文件 (例如，Word 文件或 PowerPoint Deck)，因此可說是適當的選項。

1. 繼續進行下一頁。

> [!NOTE]
> 自然語言處理技能會透過範例資料集內的文字內容來運作。 由於我們並未選取 OCR 選項，在範例資料集中找到的 JPEG 和 PNG 檔案將不會在此快速入門中處理。 

### <a name="step-3-configure-the-index"></a>步驟 3：設定索引

在 Azure 認知搜尋中，索引會包含可搜尋的內容，而**匯入資料**精靈通常可藉由資料來源的取樣為您建立結構描述。 在此步驟中，請檢閱產生的結構描述，並視需要修改任何設定。 以下是針對示範 Blob 資料集而建立的預設結構描述。

在本快速入門中，精靈會妥善地設定好合理的預設值： 

+ 預設名稱是以資料來源類型為基礎的 azureblob-index  。 

+ 預設欄位以原始來源資料欄位 (`content`)，以及認知技能所建立的輸出欄位 (`people`、`organizations` 和 `locations`) 為基礎。 從中繼資料與資料取樣可推斷預設資料類型。

+ 預設文件索引鍵是 *metadata_storage_path* (因欄位包含唯一值而選取)。

+ 這些欄位的預設屬性為 [可擷取]  和 [可搜尋]  。 「可搜尋」  表示可搜尋到某個欄位。 「可擷取」  表示它可以在結果中傳回。 精靈假設您希望這些欄位為可擷取並可搜尋，因為您是透過技能集來建立欄位。

  ![索引欄位](media/cognitive-search-quickstart-blob/index-fields.png)

請注意，依 `content` 欄位顯示的 [可擷取]  屬性上的刪除線和問號。 在大量文字的 Blob 文件中，`content` 欄位包含檔案主體，有可能遇到數千行。 如果您需要將檔案內容傳遞給用戶端程式碼，請確定 [可擷取]  保持已選取狀態。 否則，如果擷取的元素 (`people`、`organizations` 和 `locations`) 足以符合您的目的，請考慮清除 `content` 上的這個屬性。

將欄位標示為 [可擷取]  不表示該欄位「必須」  出現在搜尋結果中。 您可以使用 **$select** 查詢參數來指定要包含的欄位，精確地控制搜尋結果組合。 對於大量文字的欄位 (如`content`)， **$select** 參數可讓您將可管理的搜尋結果提供給您應用程式的人類使用者，同時確保用戶端程式碼可透過 [可擷取]  屬性存取其所需的所有資訊。
  
繼續進行下一頁。

### <a name="step-4-configure-the-indexer"></a>步驟 4：設定索引子

索引子是會驅動索引編製程序的高階資源。 它會指定資料來源名稱、目標索引和執行頻率。 **匯入資料**精靈會建立數個物件，且一定是您可以重複執行的索引子。

1. 在 [索引子]  頁面上，您可以接受預設名稱，並按一下 [一次]  排程選項立即加以執行。 

   ![索引子定義](media/cognitive-search-quickstart-blob/indexer-def.png)

1. 按一下 [提交]  以建立並同時執行索引子。

## <a name="monitor-status"></a>監視狀態

認知技能的索引編製需要比一般文字的索引編製更長的時間來完成。 若要監視進度，請移至 [概觀] 頁面，然後按一下頁面中央的 [索引子]  。

由於資料來源中是 JPG 和 PNG 影像檔案，且我們在此管線中省略了 OCR 技能，因此會發生警告。 您也可以找到截斷通知。 免費層上的擷取限制為 32,000 個字元。

  ![Azure 認知搜尋通知](./media/cognitive-search-quickstart-blob/indexer-notification.png)

編製索引和擴充需要一些時間，這就是為什麼會建議您在初期探索時使用較小的資料集。 

在 Azure 入口網站中，您也可以監視通知活動記錄中是否有可點選的 **Azure 認知搜尋通知**狀態連結。 執行作業可能需要幾分鐘的時間才能完成。

## <a name="query-in-search-explorer"></a>在搜尋總管中查詢

建立索引之後，您可以提交查詢以從索引中傳回文件。 在入口網站中，使用**搜尋總管**來執行查詢並檢視結果。 

1. 在搜尋服務儀表板頁面上，按一下命令列上的 [搜尋總管]  。

1. 選取頂端的 [變更索引]  以選取您建立的索引。

1. 輸入搜尋字串以查詢索引，例如 `search=Microsoft&searchFields=Organizations`。

結果會以 JSON 傳回，內容可能很詳細但也很難閱讀，尤其是源自於 Azure Blob 的大型文件更是如此。 如果您無法輕鬆地瀏覽結果，請使用 CTRL-F 來搜尋文件。 針對此查詢，您可以在 JSON 中搜尋特定字詞。 

CTRL-F 也可協助您判斷指定的結果集中有多少文件。 針對 Azure Blob，入口網站會選擇 "metadata_storage_path" 作為索引鍵，因為每個值對文件而言都是唯一的。 使用 CTRL-F 搜尋 "metadata_storage_path，即可取得文件計數。 

  ![搜尋總管範例](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>重要心得

您現在已建立第一個技能集，並了解使用您自己的資料為擴充的搜尋解決方案設計原型所需的重要概念。

我們希望您了解的一些重要概念包括 Azure 資料來源上的相依性。 技能集會繫結至索引子，而索引子則專屬於 Azure 與來源。 雖然本快速入門使用 Azure Blob 儲存體，但您也可使用其他 Azure 資料來源。 如需詳細資訊，請參閱 [Azure 認知搜尋中的索引子](search-indexer-overview.md)。

另一個重要概念是，技能是透過輸入欄位來運作。 在入口網站中，您必須為所有技能選擇單一來源欄位。 在程式碼中，輸入可以是其他欄位或上游技能的輸出。

輸出會導向至搜尋索引，且在編製索引期間建立的名稱/值配對會與索引中的個別欄位相對應。 就內部而言，入口網站會設定[註解](cognitive-search-concept-annotations-syntax.md)並定義[技能集](cognitive-search-defining-skillset.md)，以建立作業順序和一般流程。 這些步驟會隱藏在入口網站中，但是當您開始撰寫程式碼時，這些概念就會變得很重要。

最後，您已了解可以藉由查詢索引來驗證內容。 最終，Azure 認知搜尋所提供的就是可搜尋的索引，且您可以使用[簡單](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)或[完全展開的查詢語法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)加以查詢。 包含擴充欄位的索引都彼此類似。 無論您想要加入標準或[自訂分析器](search-analyzers.md)、[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[同義字](search-synonyms.md)、[多面向篩選](search-filters-facets.md)、地理搜尋，或任何其他 Azure 認知搜尋功能，全都沒有問題。

## <a name="clean-up"></a>清除

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源]  或 [資源群組]  連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。 

## <a name="next-steps"></a>後續步驟

您可以使用入口網站、.NET SDK 或 REST API 來建立技能集。 若要進一步精進知識，請使用 Postman 和更多範例資料來試用 REST API。

> [!div class="nextstepaction"]
> [教學課程：使用 AI 擴充將結構新增至「非結構化內容」](cognitive-search-tutorial-blob.md)

> [!Tip]
> 如果您想要重複此練習，或嘗試不同的 AI 擴充逐步解說，請刪除入口網站中的索引子。 刪除索引子，會將認知服務處理的免費每日交易計數器重設回零。