---
title: 在 Azure 入口網站中建立知識存放區 - Azure 搜尋服務
description: 使用 Azure 入口網站中的匯入資料精靈，建立 Azure 搜尋服務知識存放區，以便從認知搜尋管線保存擴充。
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 14996d0ac9ee4e086a5dccd9275ef694adca06ca
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963185"
---
# <a name="create-an-azure-search-knowledge-store-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure 搜尋服務知識存放區

> [!Note]
> 知識存放區處於預覽狀態，不應用於生產環境。 [Azure 搜尋服務 REST API 版本 2019-05-06-Preview](search-api-preview.md) 會提供此功能。 目前沒有 .NET SDK 支援。
>

知識存放區是 Azure 搜尋服務中的一項功能，可保存 AI 擴充管線的輸出，以便進行後續分析或其他下游處理。 AI 擴充管線可接受影像檔案或非結構化文字檔案，使用 Azure 搜尋服務編製其所引，從認知服務 (例如影像分析和自然語言處理) 套用 AI 擴充，然後將結果儲存至 Azure 儲存體中的知識存放區。 您可以接著使用 Power BI 或儲存體總管之類的工具來探索知識存放區。

在本文中，您會在 Azure 入口網站上使用 [匯入資料] 精靈，內嵌、編制索引，以及將 AI 擴充套用至一組旅館評論。 旅館評論會匯入 Azure Blog 儲存體中，而結果會儲存為 Azure 資料表儲存體中的知識存放區。

建立知識存放區之後，您可以了解如何使用儲存體總管或 Power BI 來存取此知識存放區。

## <a name="prerequisites"></a>必要條件

+ [建立 Azure 搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本教學課程的免費服務。

+ [建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)以儲存範例資料和知識存放區。 您的儲存體帳戶必須使用相同的位置 (例如，以 US-WE 作為您的 Azure 搜尋服務服務，且 帳戶種類  必須是 StorageV2 (一般用途 V2)  (預設) 或 儲存體 (一般用途 V1)  。

## <a name="load-the-data"></a>載入資料

將旅館評論 CSV 檔案載入 Azure Blob 儲存體中，以便 Azure 搜尋服務索引子存取該檔案，並透過 AI 擴充管線進行饋送。

### <a name="create-an-azure-blob-container-with-the-data"></a>建立含有此資料的 Azure Blob 容器

1. [下載儲存在 CSV 檔案 (HotelReviews_Free.csv) 中的旅館評論資料](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)。 此資料源自 Kaggle.com 並包含客戶對於旅館的意見反應。
1. [登入 Azure 入口網站](https://portal.azure.com)，然後瀏覽至您的 Azure 儲存體帳戶。
1. [建立 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) 若要執行這項操作，請在您儲存體帳戶的左側導覽列中，按一下 [Blob]  ，然後按一下命令列上的 [+ 容器]  。
1. 針對新的容器 [名稱]  輸入 `hotel-reviews`。
1. 選取任何 [公用存取層級]  。 我們使用預設值。
1. 按一下 [確定]  以建立 Azure Blob 容器。
1. 開啟新的 `hotels-review` 容器，按一下 [上傳]  ，然後選取您在第一個步驟中下載的 **HotelReviews-Free.csv** 檔案。

    ![上傳資料](media/knowledge-store-create-portal/upload-command-bar.png "上傳旅館評論")

1. 按一下 [上傳]  ，將 CSV 檔案匯入 Azure Blob 儲存體中。 新的容器隨即出現。

    ![建立 Azure Blob 容器](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "建立 Azure Blob 容器")

### <a name="get-the-azure-storage-account-connection-string"></a>取得 Azure 儲存體帳戶的連接字串

1. 在入口網站上，瀏覽至您的 Azure 儲存體帳戶。
1. 在服務的左側導覽中，按一下 [存取金鑰]  。
1. 在 [金鑰 1]  底下，複製並儲存*連接字串*。 字串的開頭為 `DefaultEndpointsProtocol=https`。 您的儲存體帳戶名稱和金鑰會內嵌在字串中。 將此字串保存在隨手可及的位置。 您在之後的步驟中將會用到它。

## <a name="create-and-run-ai-enrichments"></a>建立並執行 AI 擴充

使用匯入資料精靈來建立知識存放區。 您將建立資料來源、選擇擴充、設定知識存放區和索引，然後執行。

### <a name="start-the-import-data-wizard"></a>啟動匯入資料精靈

1. 在 Azure 入口網站上，[尋找您的搜尋服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

1. 在命令列上，按一下 [匯入資料]  以啟動匯入精靈。

### <a name="connect-to-your-data-import-data-wizard"></a>連線到您的資料 (匯入資料精靈)

在此精靈步驟中，您將從 Azure Blob 建立資料來源與您的旅館資料。

1. 在 [資料來源]  清單中，選取 [Azure Blob 儲存體]  。
1. 針對 [名稱]  輸入 `hotel-reviews-ds`。
1. 針對 [剖析模式]  ，選取 [分隔的文字]  ，然後選取 [第一行包含標頭]  核取方塊。 請確定 [分隔符號字元]  是逗號 (,)。
1. 輸入您在上一個步驟中儲存的儲存體服務**連接字串**。
1. 針對 [容器名稱]  輸入 `hotel-reviews`。
1. 按一下 **下一步新增認知搜尋 (選擇性)** 。

      ![建立資料來源物件](media/knowledge-store-create-portal/hotel-reviews-ds.png "建立資料來源物件")

## <a name="add-cognitive-search-import-data-wizard"></a>新增認知搜尋 (匯入資料精靈)

在此精靈步驟中，您將建立具有認知技能擴充的技能集。 我們在此範例中使用的技能會擷取關鍵片語，並偵測語言和情感。 這些擴充將「投射」到知識存放區中作為 Azure 資料表。

1. 展開 [連結認知服務]  。 預設會選取 [免費 (有限的擴充)]  。 您可以使用此資源，因為 HotelReviews-Free.csv 中的記錄筆數是 19，而此免費資源一天最多允許 20 筆交易。
1. 展開 [新增擴充]  。
1. 針對 [技能集名稱]  輸入 `hotel-reviews-ss`。
1. 針對 [來源資料欄位]  ，選取 **reviews_text*。
1. 針對 [擴充細微性層級]  ，選取 [頁面 (5000 個字元區塊)]  。
1. 選取這些認知技能：
    + **擷取關鍵片語**
    + **偵測語言**
    + **偵測情感**

      ![建立技能](media/knowledge-store-create-portal/hotel-reviews-ss.png "建立技能")

1. 展開 [將擴充儲存到知識存放區]  。
1. 輸入您在上一個步驟中儲存的 [儲存體帳戶連接字串]  。
1. 選取下列 [Azure 資料表投影]  ：
    + **文件**
    + **頁面**
    + **關鍵片語**

    ![設定知識存放區](media/knowledge-store-create-portal/hotel-reviews-ks.png "設定知識存放區")

1. 按一下 **下一步自訂目標索引**。

### <a name="import-data-import-data-wizard"></a>匯入資料 (匯入資料精靈)

在此精靈步驟中，您會設定用於選擇性全文檢索搜尋查詢的索引。 此精靈會取樣您的資料來源，以推斷欄位和資料類型。 您只需要選取所需行為的屬性。 例如，[可擷取]  屬性會允許搜尋服務傳回欄位值，而 [可搜尋]  會啟用欄位的全文檢索搜尋。

1. 針對 [索引名稱]  輸入 `hotel-reviews-idx`。
1. 針對屬性，請進行下列選擇：
    + 針對所有欄位選取 [可取得]  。
    + 針對這些欄位選取 [可篩選]  和 [可 Facet]  ：情感  、語言  、關鍵片語 
    + 針對下列欄位選取 [可搜尋] ***：city*** 、name  、reviews_text  、language  、Keyphrases 

    您的索引應該會看起來如下圖。 因為此清單很長，所以並非所有欄位都會顯示在影像中。

    ![設定索引](media/knowledge-store-create-portal/hotel-reviews-idx.png "設定索引")

1. 按一下 **下一步建立索引子**。

### <a name="create-an-indexer"></a>建立索引子

在此步驟中，您將設定索引子，以便將資料來源、技能集，以及您在先前的精靈步驟中定義的索引整合在一起。

1. 針對 [名稱]  輸入 `hotel-reviews-idxr`。
1. 針對 [排程]  ，保留預設值 [一次]  。
1. 按一下 [提交]  以執行索引子。 資料擷取、編製索引、認知技能的應用全都在此步驟進行。

### <a name="monitor-the-notifications-queue-for-status"></a>監視通知佇列中的狀態

1. 在 Azure 入口網站中，監視通知活動記錄中是否有可點選的 **Azure 搜尋服務通知**狀態連結。 執行作業可能需要幾分鐘的時間才能完成。

## <a name="next-steps"></a>後續步驟

您現在已使用認知服務擴充資料，並將結果投射到知識存放區，即可使用儲存體總管或 Power BI 來探索擴充的資料集。

若要了解如何使用儲存體總管來探索此知識存放區，請參閱下列逐步解說。

> [!div class="nextstepaction"]
> [使用儲存體總管檢視](knowledge-store-view-storage-explorer.md)

若要了解如何將此知識存放區連線到 Power BI，請參閱下列逐步解說。

> [!div class="nextstepaction"]
> [與 Power BI 連線](knowledge-store-connect-power-bi.md)

如果您想要重複此練習，或嘗試不同的 AI 擴充逐步解說，請刪除 hotel-reviews-idxr  索引子。 刪除索引子會將免費的每日交易計數器重設回零。
