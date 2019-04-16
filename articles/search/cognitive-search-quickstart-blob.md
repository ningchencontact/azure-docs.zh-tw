---
title: 快速入門：在 Azure 入口網站中建置採用 AI 技術的索引 - Azure 搜尋服務
description: 使用Azure 入口網站和範例資料，在 Azure 搜尋服務的索引編製入口網站中使用資料擷取、自然語言和影像處理技能。
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 04/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 161d3ff3e00f7e9e979527533f6b8ac365c41490
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265010"
---
# <a name="quickstart-create-an-ai-indexing-pipeline-using-cognitive-skills-and-sample-data"></a>快速入門：使用認知技能和範例資料建立 AI 索引管線

Azure 搜尋服務會與[認知服務](https://azure.microsoft.com/services/cognitive-services/)整合，藉此將內容料擷取、自然語言處理 (NLP) 和影像處理技能新增至 Azure 搜尋服務索引管線，讓無法搜尋或非結構化的內容變得更便於搜尋。 

許多認知服務資源，例如 [OCR](cognitive-search-skill-ocr.md)、[語言偵測](cognitive-search-skill-language-detection.md)和[實體辨識](cognitive-search-skill-entity-recognition.md)等等，都可連結到編製索引程序。 認知服務的 AI 演算法用來尋找來源資料中的模式、特徵和特性，傳回結構和文字內容，這些資訊可用於以 Azure 搜尋服務為基礎的全文檢索搜尋解決方案。

在本快速入門中，您會先在 [Azure 入口網站中](https://portal.azure.com)建立擴充管線，然後再撰寫單一行程式碼：

> [!div class="checklist"]
> * 從使用 Azure blob 儲存體中的範例資料開始
> * 設定[**匯入資料**](search-import-data-portal.md)精靈來進行認知編製索引和進行擴充 
> * 執行精靈 (會偵測人員、位置和組織的實體技能)
> * 使用[**搜尋總管**](search-explorer.md)來查詢擴充的資料

本快速入門雖然在免費服務上執行，但可用的交易數目限制為每日 20 份文件。 如果您想要在同一天中多次執行本快速入門，請使用較小的檔案集，如此才能在限制內完成多次執行。

> [!NOTE]
> 當您藉由增加處理次數、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須連結可計費的認知服務資源。 在認知服務中呼叫 API，以及在 Azure 搜尋服務的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務隨用隨附價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價會依預覽定價收費，如 [Azure 搜尋服務定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)所述。 [深入](cognitive-search-attach-cognitive-services.md)了解。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

[建立 Azure 搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。

[認知服務](https://azure.microsoft.com/services/cognitive-services/)會提供 AI。 本快速入門包含指定管線時新增這些內嵌資源的步驟。 您不需要事先設定帳戶。

需使用 Azure 服務來提供索引管線的輸入。 您可以使用任何由 [Azure 搜尋服務索引子](search-indexer-overview.md)支援的資料來源，除了 Azure 表格儲存體以外，其不支援 AI 索引編製。 本快速入門會使用 [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)作為來源資料檔案的容器。 

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>設定 Azure Blob 服務並載入範例資料

1. [下載範例資料](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)，其中有不同類型的小型檔案集。 

1. [註冊 Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)、建立儲存體帳戶、開啟 Blob 服務頁面，以及建立容器。  在 Azure 搜尋服務的所在區域中建立儲存體帳戶。

1. 在您建立的 容器中，按一下 [上傳] 以上傳您在上一步中下載的範例檔案。

   ![Azure Blob 儲存體中的來源檔案](./media/cognitive-search-quickstart-blob/sample-data.png)

## <a name="create-the-enrichment-pipeline"></a>建立擴充管線

返回 Azure 搜尋服務儀表板頁面，按一下命令列上的 [匯入資料]，以四個步驟來設定認知擴充。

  ![匯入資料命令](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>步驟 1：建立資料來源

在 [連線到您的資料] 中，選擇 [Azure Blob 儲存體]，選取您建立的帳戶和容器。 指定資料來源的名稱，其餘部分則使用預設值。 

  ![Azure Blob 組態](./media/cognitive-search-quickstart-blob/blob-datasource.png)

繼續進行下一頁。

  ![認知搜尋的下一頁按鈕](media/cognitive-search-quickstart-blob/next-button-add-cog-search.png)

### <a name="step-2-add-cognitive-skills"></a>步驟 2：新增認知技能

接著，將擴充步驟新增至管線。 如果您沒有認知服務資源，可以註冊免費版，其可提供您每天 20 筆交易。 範例資料包含 14 個檔案，因此您一旦執行此精靈，就幾乎用盡每日配置。

1. 展開 [附加認知服務] 以檢視認知服務 API 的資源處理選項。 針對本教學課程的目的，您可以使用**免費**資源。

   ![附加認知服務](media/cognitive-search-quickstart-blob/cog-search-attach.png)

2. 展開 [新增擴充] 然後選取可執行自然語言處理的技能。 針對本快速入們，請選擇適用於人員、組織和位置的實體辨識。

   ![附加認知服務](media/cognitive-search-quickstart-blob/skillset.png)

   入口網站提供 OCR 處理和文字分析的內建技能。 在入口網站中，技能集會透過單一來源欄位來運作。 這聽起來像是小目標，但 Azure Blob 的 `content` 欄位包含大部分的 Blob 文件 (例如，Word 文件或 PowerPoint Deck)。 因此，此欄位是理想的輸入，因為其中有 Blob 的所有內容。

3. 繼續進行下一頁。

   ![下一頁：自訂索引](media/cognitive-search-quickstart-blob/next-button-customize-index.png)

> [!NOTE]
> 自然語言處理技能會透過範例資料集內的文字內容來運作。 由於我們並未選取 OCR 選項，在範例資料集中找到的 JPEG 和 PNG 檔案將不會在此快速入門中處理。 

### <a name="step-3-configure-the-index"></a>步驟 3：設定索引

此精靈通常可以推斷預設索引。 在此步驟中，您可以檢視所產生的索引結構描述，也可能會修改任何設定。 以下是針對示範 Blob 資料集所建立的預設索引。

在本快速入門中，精靈會妥善地設定好合理的預設值： 

+ 預設名稱是以資料來源類型為基礎的 azureblob-index。 

+ 預設欄位是以原始來源資料欄位 (`content`)，以及認知管線所建立的輸出欄位 (`people`、`organizations` 和 `locations`) 為基礎。 從中繼資料與資料取樣可推斷預設資料類型。

+ 預設索引鍵是 *metadata_storage_path* (此欄位包含唯一值)。

+ 這些欄位的預設屬性為 [可擷取] 和 [可搜尋]。 「可搜尋」表示可搜尋到某個欄位。 「可擷取」表示它可以在結果中傳回。 精靈假設您希望這些欄位為可擷取並可搜尋，因為您是透過技能集來建立欄位。

  ![索引欄位](media/cognitive-search-quickstart-blob/index-fields.png)

請注意，依 `content` 欄位顯示的 [可擷取] 屬性上的刪除線和問號。 在大量文字的 Blob 文件中，`content` 欄位包含檔案主體，有可能遇到數千行。 如果您需要將檔案內容傳遞給用戶端程式碼，請確定 [可擷取] 保持已選取狀態。 否則，如果擷取的元素 (`people`、`organizations` 和 `locations`) 足以符合您的目的，請考慮清除 `content` 上的這個屬性。

將欄位標示為 [可擷取] 不表示該欄位「必須」出現在搜尋結果中。 您可以使用 **$select** 查詢參數來指定要包含的欄位，精確地控制搜尋結果組合。 對於大量文字的欄位 (如`content`)，**$select** 參數可讓您將可管理的搜尋結果提供給您應用程式的人類使用者，同時確保用戶端程式碼可透過 [可擷取] 屬性存取其所需的所有資訊。
  
繼續進行下一頁。

  ![下一頁：建立索引子](media/cognitive-search-quickstart-blob/next-button-create-indexer.png)

### <a name="step-4-configure-the-indexer"></a>步驟 4：設定索引子

索引子是會驅動索引編製程序的高階資源。 它會指定資料來源名稱、目標索引和執行頻率。 **匯入資料**精靈最終結果一定是您可以重複執行的索引子。

在 [索引子] 頁面上，您可以接受預設名稱，並使用 [執行一次] 排程選項來立即執行它。 

  ![索引子定義](media/cognitive-search-quickstart-blob/indexer-def.png)

按一下 [提交] 以建立並同時執行索引子。

## <a name="monitor-indexing"></a>監視編製索引

擴充步驟需要比一般文字索引更長的時間來完成。 精靈應該會在 [概觀] 頁面中開啟索引子清單，方便您追蹤進度。 若要自行瀏覽，請移至 [概觀] 頁面，然後按一下 [索引子]。

因為 JPG 和 PNG 檔是影像檔案，而我們在此管線中省略了 OCR 技能，因此會發生警告。 您也可以找到截斷通知。 Azure 搜尋服務在免費層的擷取限制為 32,000 個字元。

  ![Azure 搜尋服務通知](./media/cognitive-search-quickstart-blob/indexer-notification.png)

編製索引和擴充需要一些時間，這就是為什麼會建議您在初期探索時使用較小的資料集。 

## <a name="query-in-search-explorer"></a>在搜尋總管中查詢

建立索引之後，您可以提交查詢以從索引中傳回文件。 在入口網站中，使用**搜尋總管**來執行查詢並檢視結果。 

1. 在搜尋服務儀表板頁面上，按一下命令列上的 [搜尋總管]。

1. 選取頂端的 [變更索引] 以選取您建立的索引。

1. 輸入搜尋字串以查詢索引，例如 `search=Microsoft&searchFields=organizations`。

結果會以 JSON 傳回，內容可能很詳細但也很難閱讀，尤其是源自於 Azure Blob 的大型文件更是如此。 如果您無法輕鬆地瀏覽結果，請使用 CTRL-F 來搜尋文件。 針對此查詢，您可以在 JSON 中搜尋特定字詞。 

CTRL-F 也可協助您判斷指定的結果集中有多少文件。 針對 Azure Blob，入口網站會選擇 "metadata_storage_path" 作為索引鍵，因為每個值對文件而言都是唯一的。 使用 CTRL-F 搜尋 "metadata_storage_path，即可取得文件計數。 

  ![搜尋總管範例](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>重要心得

您現在已完成第一個擴充的認知索引編製練習。 本快速入門的目的是要介紹重要概念，並透過精靈引導您完成步驟，讓您可以使用自己的資料，快速完成認知搜尋解決方案的原型。

我們希望您了解的一些重要概念包括 Azure 資料來源上的相依性。 認知搜尋擴充會受到索引子的約束，而索引子專屬於 Azure 與來源。 雖然本快速入門使用 Azure Blob 儲存體，但您也可使用其他 Azure 資料來源。 如需詳細資訊，請參閱 [Azure 搜尋服務中的索引子](search-indexer-overview.md)。

另一個重要概念是，技能是透過輸入欄位來運作。 在入口網站中，您必須為所有技能選擇單一來源欄位。 在程式碼中，輸入可以是其他欄位或上游技能的輸出。

 技能的輸入會對應到索引中的輸出欄位。 就內部而言，入口網站會設定[註解](cognitive-search-concept-annotations-syntax.md)並定義[技能集](cognitive-search-defining-skillset.md)，以建立作業順序和一般流程。 這些步驟會隱藏在入口網站中，但是當您開始撰寫程式碼時，這些概念就會變得很重要。

最後，您已了解可透過查詢索引來檢視結果。 結果就是 Azure 搜尋服務提供的功能是可搜尋的索引，您可以使用[簡單](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)或[完全展開的查詢語法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)來進行查詢。 包含擴充欄位的索引都彼此類似。 如果您想要加入標準或[自訂分析器](search-analyzers.md)、[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[同義字](search-synonyms.md)、[多面向篩選](search-filters-facets.md)、地理搜尋，或任何其他 Azure 搜尋功能，您完全可以這麼做。

## <a name="clean-up-resources"></a>清除資源

如果您的探索已結束，最快速的清除方式是刪除包含 Azure 搜尋服務和 Azure Blob 服務的資源群組。  

如果您將這兩項服務放在相同群組中，現在刪除資源群組表示永久刪除其中所有內容，包括服務與您為此練習建立的任何已儲存內容。 在入口網站中，資源群組名稱位在每個服務的 [概觀] 頁面上。

## <a name="next-steps"></a>後續步驟

根據您佈建認知服務資源的方式，您可以使用不同技能和來源資料欄位重新執行精靈，來實驗索引編製和擴充。 若要重複步驟，請刪除索引和索引子，然後以新的選項組合來重新建立索引子。

+ 在 [概觀] > [索引] 中，選取您建立的索引，然後按一下 [刪除]。

+ 在 [概觀] 中，連按兩下 [索引子] 磚。 找到您建立的索引子，並將它刪除。

或者，重複使用您建立的範例資料和服務，然後在下一個教學課程中，了解如何以程式設計方式執行相同工作。 

> [!div class="nextstepaction"]
> [教學課程：了解認知搜尋 REST API](cognitive-search-tutorial-blob.md)
