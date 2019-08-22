---
title: 使用 Power BI 連線到知識存放區 - Azure 搜尋服務
description: 在 Azure 入口網站中使用匯入資料精靈建立知識存放區，然後與 Power BI 連線以進行分析和探索。
author: heidisteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: heidist
ms.openlocfilehash: 1c7f297092760f2a92f524347a3c1a5e353d0965
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69635558"
---
# <a name="create-an-azure-search-knowledge-store-and-connect-using-power-bi"></a>建立 Azure 搜尋服務的知識存放區，並使用 Power BI 進行連線

> [!Note]
> 知識存放區處於預覽狀態，不應用於生產環境。 [Azure 搜尋服務 REST API 版本 2019-05-06-Preview](search-api-preview.md) 會提供此功能。 目前沒有 .NET SDK 支援。
>

知識存放區是 Azure 搜尋服務中的一項功能，可保存 AI 擴充管線的輸出，以便進行後續的分析或其他下游處理。 AI 擴充管線可接受所支援資料來源中的影像檔案或非結構化文字檔，然後將其傳送至 Azure 搜尋服務的編製索引功能，接著，從認知服務套用 AI 擴充 (例如影像分析和自然語言處理)，並將結果儲存至 Azure 儲存體中的知識存放區。 在知識存放區中，您可以連結 Power BI 或儲存體總管之類的工具來探索結果。

在本文中，您可以在入口網站中建立知識存放區，然後使用 Power BI Desktop 中的 Power Query 進行連線和探索。 

本逐步解說使用包含客戶評論和評等的資料集、來自認知服務的情感評分，並使用 Power Query 來查詢您的文件。 這些資料來自 Kaggle.com 上的飯店評論資料。 

## <a name="prerequisites"></a>必要條件

+ [下載飯店評論的 CSV 檔案 (HotelReviews_Free.csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)。 此資料集包含關於飯店的客戶意見反應記錄。

+ [Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ [Azure 搜尋服務](search-create-service-portal.md)。 您可以使用免費服務來進行此逐步解說。 

+ [Azure 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。 請確定帳戶是「一般用途」，也就是作為預設值的 StorageV2 (一般用途 V2)  或儲存體 (一般用途 V1)  。 選擇與「Azure 搜尋服務」相同的區域。
 
## <a name="prepare-data"></a>準備資料 

將 .csv 檔案載入至 Azure Blob 儲存體，讓 Azure 搜尋服務索引子可以存取該檔案。

1. [登入 Azure 入口網站](https://portal.azure.com)瀏覽至您的 Azure 儲存體帳戶、按一下 [Blob]  ，然後按一下 [+ 容器]  。

1. [建立 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)以容納範例資料： 

   1. 將容器命名為 `hotel-reviews`。 
   
   1. 將 [公用存取層級] 設定為任何有效值。 我們使用預設值。

1. 建立容器之後，請加以開啟，然後選取命令列的 [上傳]  。

1. 流覽至包含 **HotelReviews-Free.csv** 的資料夾並選取該檔案，然後按一下 [上傳]  。

   ![上傳 .csv 檔案](media/knowledge-store-howto-powerbi/hotel-reviews-blob-container.png "上傳 .csv 檔案")

1. 當您位於 Azure 儲存體中時，請取得連接字串和容器名稱。  建立資料來源物件時會需要這兩個字串：

   1. 在 [概觀] 頁面中，按一下 [存取金鑰]  並複製「連接字串」  。 它會以 `DefaultEndpointsProtocol=https;` 作為開頭，並以 `EndpointSuffix=core.windows.net` 作為結尾。 您的帳戶名稱和金鑰會介於它們之間。 

   1. 容器名稱應該是 `hotel-reviews` 或您所指派的任何名稱。 

## <a name="create-and-run-ai-enrichments"></a>建立並執行 AI 擴充

使用匯入資料精靈來建立知識存放區。 您將匯入資料集、選擇擴充資料、設定知識存放區和索引，然後執行。

1. 在 Azure 入口網站上[尋找您的搜尋服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

1. 按一下命令列上的 [匯入資料]  。

1. 在精靈的第一頁中建立資料來源物件。

   - 選取 **Azure Blob 儲存體**。

   - 提供資料來源的名稱，例如 hotel-reviews-ds  。

   - 由於資料是 .csv 檔案，請選取 [分隔文字]  作為剖析模式，然後選取 [第一行包含標頭]  ，並確定分隔符號為逗號。

   - 您可以在入口網站的 [存取金鑰]  底下取得 Azure 儲存體帳戶的連接字串。

   - 也可以從入口網站的 Azure 儲存體 Blob 清單中取得容器名稱。

      ![建立資料來源物件](media/knowledge-store-howto-powerbi/hotel-reviews-ds.png "建立資料來源物件")

1. 在精靈的第二頁中新增擴充及設定知識存放區。

   - 在 [連結認知服務]  中，您可以使用每日最多允許 20 筆交易的免費資源。 HotelReviews-Free.csv 中的記錄數目小於 20 筆。

   - 在 [新增擴充]  中，將技能命名為 hotel-reviews-ss  、選擇 [reviews_text]  欄位、選擇 [頁面 (5000 個字元區塊)]  作為粒度層級，然後選取三個認知技能：[擷取關鍵片語]  、[偵測語言]  、[偵測情感]  。

      ![建立技能](media/knowledge-store-howto-powerbi/hotel-reviews-ss.png "建立技能")

   - 在 [將擴充資料儲存至知識存放區]  中，提供一般用途 Azure 儲存體帳戶的連接字串。 如果您在本區段中選取 [Azure 資料表專案]  選項，知識存放區就會建立在 Azure 資料表儲存體中。

      ![設定知識存放區](media/knowledge-store-howto-powerbi/hotel-reviews-ks.png "設定知識存放區")
   
   按一下 **[下一步自訂目標索引]** ，以繼續進行下一個步驟。

1. 在 Azure 搜尋服務中設定用於選擇性全文檢索搜尋查詢的索引。 雖然本逐步解說的重點是連線到 Azure 資料表儲存體的 Power BI，但是**匯入資料**精靈也可以在 Azure 搜尋服務中，建立用於全文檢索搜尋的索引。 

   此精靈會對您的資料來源進行取樣，以推斷欄位和資料類型，因此，您只需要選取必要的屬性，就能完成所需的行為。 例如，[可擷取]  表示可以從服務中擷取欄位內容，而 [可搜尋]  則可在選取的欄位上進行全文檢索搜尋。

   - 提供索引的名稱，例如 hotel-reviews-idx  。
   - 將所有欄位設定為 [可擷取]  。
   - 將 city  、name  、reviews_text  、language  、keyphrases  設定為 [可搜尋]  。
   - 將 sentiment  、language  、keyphrases  設定為 [可篩選]  和 [可使用 Facet]  。 
   
    您的索引應該會看起來如下圖。

     ![設定索引](media/knowledge-store-howto-powerbi/hotel-reviews-idx.png "設定索引")

   按一下 **[下一步建立索引子]** 以繼續進行下一個步驟。

1. 藉由提供索引子名稱和執行頻率來設定索引子。 此逐步解說會使用 hotel-reviews-idxr  作為索引子名稱，並使用預設的 [立即]  排程來立即執行索引子。

   索引子執行會啟動所有先前的設定。 擷取、處理和內嵌都會在此步驟中進行。

1. 在入口網站的通知佇列中監視索引編製。 執行需要幾分鐘才能完成。

## <a name="connect-with-power-bi"></a>使用 Power BI 進行連接

1. 啟動 Power BI Desktop 並選取 [取得資料]  。

1. 在 [取得資料] **中，依序選取 [Azure]** **和 [Azure 資料表儲存體]** 。 按一下 [ **連接**]。

1. 在 [帳戶名稱] 或 [URL] 中，貼上您的 Azure 儲存體帳戶名稱 (系統會為您解析完整的 URL)。

1. 輸入帳戶金鑰。

1. 選取 [Document]、[KeyPhrases] 和 [Pages]。 這些項目是您在知識存放區設定中選取同名專案時，匯入資料精靈建立的資料表。 按一下 [載入]  。

1. 按一下 [編輯查詢]  命令來開啟 Power Query。

   ![開啟 Power Query](media/knowledge-store-howto-powerbi/powerbi-edit-queries.png "開啟 Power Query")

1. 針對 Document：

   - 移除 Azure 資料表儲存體所建立的 PartitionKey、RowKey 和 Timestamp 資料行。 知識存放區提供此分析中使用的關聯性。

   - 展開內容資料行。

     ![編輯資料表](media/knowledge-store-howto-powerbi/powerbi-edit-table.png "編輯資料表")

1. 選取所有欄位，然後取消選取以 "metadata" 開頭的欄位。

1. 在每個資料行上使用 ABC-123 圖示來更正下列資料行的資料類型：

   - 日期資料行應使用**日期時間**
   - [緯度]  應使用**十進位數字**
   - [經度]  應使用**十進位數字**

1. 針對 KeyPhrases 重複上述步驟，移除 PartitionKey 和其他資料行、展開內容資料行，然後將 [SentimentScore]  設定為**十進位數**。

1. 再次針對 Pages 重複這些步驟，移除 PartitionKey 和其他資料行，並展開內容資料行。 此資料表無須修改資料類型。

1. 按一下 [Power Query] 命令列最左邊的 [關閉並套用]  。

1. 確認 Power BI 可辨識知識存放區在您資料中建立的關聯性。 按一下左側導覽窗格中的 [關聯性] 圖格。 三個資料表都應該有關聯。 編輯關聯性，並確定 [交叉篩選方向] 已設定為 [雙向]，這可確保所有視覺效果都會在套用篩選時重新整理。

   ![驗證關聯性](media/knowledge-store-howto-powerbi/powerbi-relationships.png "驗證關聯性")

## <a name="try-with-larger-data-sets"></a>嘗試使用大型資料集

我們刻意將資料集保持在較小型的狀態，以避免在示範逐步解說時產生費用。 如需更真實的體驗，您可以建立及連結計費的認知服務資源，以針對情感分析器、關鍵片語擷取和語言偵測器技能啟用更多的交易。

在 Azure Blob 儲存體中建立新的容器，並將每個 CSV 檔案上傳到其本身的容器。 在匯入資料精靈的資料來源建立步驟中，指定這些容器的其中一個。

| 說明 | 連結 |
|-------------|------|
| 免費層   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| 小型 (500 筆記錄) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| 中型 (6000 筆記錄)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| 大型 (完整資料集 35000 筆記錄) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). 請注意，處理極大型資料集的成本很高。 此項目的成本大約是 $1000 美元。|

若要使用大型資料集，請在精靈的擴充步驟中，將計費的[認知服務](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)資源 (建立在 S0  階層上的資源) 附加到 Azure 搜尋服務所在的區域。 

  ![建立認知服務資源](media/knowledge-store-howto-powerbi/create-cognitive-service.png "建立認知服務資源")

## <a name="next-steps"></a>後續步驟

如果您想要重複此練習，或執行另一個 AI 擴充逐步解說，請刪除您剛才建立的 hotel-reviews-idx  索引子。 刪除索引子會將免費的每日交易計數器重設回零。 

如需示範知識存放區的更多逐步解說，請繼續進行下一篇文章，其中會說明如何使用 REST API 和 Postman 建立知識存放區。

> [!div class="nextstepaction"]
> [開始使用知識存放區](knowledge-store-howto.md)
