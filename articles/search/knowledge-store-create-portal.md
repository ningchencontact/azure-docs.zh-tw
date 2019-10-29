---
title: 在 Azure 入口網站中建立知識存放區
titleSuffix: Azure Cognitive Search
description: 使用「匯入資料」精靈建立用來保存擴充內容的知識存放區。 連線至知識存放區以進行其他應用程式的分析，或將擴充的內容傳送到下游程序。
author: lisaleib
manager: nitinme
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: d714e913d5e03233ed3ffcaaebca6eb989a56bd7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790041"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立 Azure 認知搜尋知識存放區

> [!Note]
> 知識存放區處於預覽狀態，不應用於生產環境。 Azure 入口網站和[搜尋 REST API 版本 2019-05-06-Preview](search-api-preview.md) 均提供此功能。 目前沒有 .NET SDK 支援。
>

知識存放區是 Azure 認知搜尋的一項功能，可保存認知技能管線的輸出，以進行後續的分析或其他下游處理。 

管線會接受影像和非結構化文字作為原始內容、透過認知服務 (例如影像和自然語言處理) 套用 AI，並建立擴充的內容 (新的結構和資訊) 作為輸出。 管線所建立的其中一個實體成品是[知識存放區](knowledge-store-concept-intro.md)，您可以透過工具存取加以存取，以分析和探索內容。

在本快速入門中，您將結合 Azure 雲端中的服務和資料以建立知識存放區。 一切都備妥之後，您即可在入口網站中執行**匯入資料**精靈，以將其全部提取。 最終結果是原始內容和 AI 產生的內容，而您可以在入口網站 ([儲存體總管](knowledge-store-view-storage-explorer.md)) 中加以檢視。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-services-and-load-data"></a>建立服務並載入資料

本快速入門會使用 Azure 認知搜尋、Azure Blob 儲存體和 [Azure 認知服務](https://azure.microsoft.com/services/cognitive-services/)進行 AI 處理。 

由於工作負載很小，因此，從 Azure 認知搜尋叫用時，認知服務會在幕後連線以提供免費處理，每天最多 20 筆交易。 如果使用我們提供的範例資料，就可以略過建立或連結認知服務資源的步驟。

1. [下載 HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)。 這項資料是儲存在 CSV 檔案中的飯店評論資料 (源自於 Kaggle.com)，其中包含 19 個關於單一飯店的客戶意見反應。 

1. [建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)，或在您目前的訂用帳戶下方[尋找現有帳戶](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 您會將 Azure 儲存體同時用於要匯入的原始內容，以及作為最終結果的知識存放區。

   此帳戶有兩個需求：

   + 選擇與 Azure 認知搜尋相同的區域。 
   
   + 選擇 StorageV2 (一般用途 V2) 帳戶類型。 

1. 開啟 Blob 服務頁面，並建立容器。  

1. 按一下 [上傳]  。

    ![上傳資料](media/knowledge-store-create-portal/upload-command-bar.png "上傳飯店評論")

1. 選取您在第一個步驟中下載的 **HotelReviews-Free.csv** 檔案。

    ![建立 Azure Blob 容器](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "建立 Azure Blob 容器")

1. 這項資源的作業即將完成，但在離開這些頁面之前，請使用左側導覽窗格上的連結開啟 [存取金鑰]  頁面。 取得連接字串以從 Blob 儲存體中擷取資料。 連接字串會如下列範例所示：`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. [建立 Azure 認知搜尋服務](search-create-service-portal.md)，或在相同訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。

現在您已準備就緒，可移至「匯入資料」精靈。

## <a name="run-the-import-data-wizard"></a>執行匯入資料精靈

在搜尋服務的 [概觀] 頁面中，按一下命令列上的 [匯入資料]  ，以四個步驟建立知識存放區。

  ![匯入資料命令](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>步驟 1：建立資料來源

1. 在 [連線到您的資料]  中，選擇 [Azure Blob 儲存體]  ，選取您建立的帳戶和容器。 
1. 針對 [名稱]  輸入 `hotel-reviews-ds`。
1. 針對 [剖析模式]  ，選取 [分隔的文字]  ，然後選取 [第一行包含標頭]  核取方塊。 請確定 [分隔符號字元]  是逗號 (,)。
1. 輸入您在上一個步驟中儲存的儲存體服務**連接字串**。
1. 針對 [容器名稱]  輸入 `hotel-reviews`。
1. 按一下 **下一步新增 AI 擴充 (選擇性)** 。

      ![建立資料來源物件](media/knowledge-store-create-portal/hotel-reviews-ds.png "建立資料來源物件")

1. 繼續進行下一頁。

### <a name="step-2-add-cognitive-skills"></a>步驟 2：新增認知技能

在此精靈步驟中，您將建立具有認知技能擴充的技能集。 我們在此範例中使用的技能會擷取關鍵片語，並偵測語言和情感。 在後續步驟中，這些擴充將「投射」到知識存放區中作為 Azure 資料表。

1. 展開 [連結認知服務]  。 預設會選取 [免費 (有限的擴充)]  。 您可以使用此資源，因為 HotelReviews-Free.csv 中的記錄筆數是 19，而此免費資源一天最多允許 20 筆交易。
1. 展開 [新增認知技能]  。
1. 針對 [技能集名稱]  輸入 `hotel-reviews-ss`。
1. 針對 [來源資料欄位]  ，選取 **reviews_text*。
1. 針對 [擴充細微性層級]  ，選取 [頁面 (5000 個字元區塊)]  。
1. 選取這些認知技能：
    + **擷取關鍵片語**
    + **偵測語言**
    + **偵測情感**

      ![建立技能集](media/knowledge-store-create-portal/hotel-reviews-ss.png "建立技能集")

1. 展開 [將擴充儲存到知識存放區]  。
1. 輸入您在上一個步驟中儲存的 [儲存體帳戶連接字串]  。
1. 選取下列 [Azure 資料表投影]  ：
    + **文件**
    + **頁面**
    + **關鍵片語**

    ![設定知識存放區](media/knowledge-store-create-portal/hotel-reviews-ks.png "設定知識存放區")

1. 繼續進行下一頁。

### <a name="step-3-configure-the-index"></a>步驟 3：設定索引

在此精靈步驟中，您會設定用於選擇性全文檢索搜尋查詢的索引。 此精靈會取樣您的資料來源，以推斷欄位和資料類型。 您只需要選取所需行為的屬性。 例如，[可擷取]  屬性會允許搜尋服務傳回欄位值，而 [可搜尋]  會啟用欄位的全文檢索搜尋。

1. 針對 [索引名稱]  輸入 `hotel-reviews-idx`。
1. 針對屬性，請進行下列選擇：
    + 針對所有欄位選取 [可取得]  。
    + 針對這些欄位選取 [可篩選]  和 [可 Facet]  ：情感  、語言  、關鍵片語 
    + 針對下列欄位選取 [可搜尋] ***：city*** 、name  、reviews_text  、language  、Keyphrases 

    您的索引應該會看起來如下圖。 因為此清單很長，所以並非所有欄位都會顯示在影像中。

    ![設定索引](media/knowledge-store-create-portal/hotel-reviews-idx.png "設定索引")

1. 繼續進行下一頁。

### <a name="step-4-configure-the-indexer"></a>步驟 4：設定索引子

在此步驟中，您將設定索引子，以便將資料來源、技能集，以及您在先前的精靈步驟中定義的索引整合在一起。

1. 針對 [名稱]  輸入 `hotel-reviews-idxr`。
1. 針對 [排程]  ，保留預設值 [一次]  。
1. 按一下 [提交]  以執行索引子。 資料擷取、編製索引、認知技能的應用全都在此步驟進行。

## <a name="monitor-status"></a>監視狀態

認知技能的索引編製需要比一般文字的索引編製更長的時間來完成。 精靈應該會在 [概觀] 頁面中開啟索引子清單，方便您追蹤進度。 若要自行瀏覽，請移至 [概觀] 頁面，然後按一下 [索引子]  。

在 Azure 入口網站中，您也可以監視通知活動記錄中是否有可點選的 **Azure 認知搜尋通知**狀態連結。 執行作業可能需要幾分鐘的時間才能完成。

## <a name="next-steps"></a>後續步驟

現在您已使用認知服務擴充資料，並將結果投射到知識存放區，接下來您可以使用儲存體總管或 Power BI 來探索擴充的資料集。

您可以在儲存體總管中檢視內容，或使用 Power BI 透過視覺效果取得深入解析。

> [!div class="nextstepaction"]
> [使用儲存體總管檢視](knowledge-store-view-storage-explorer.md)
> [使用 Power BI 連接](knowledge-store-connect-power-bi.md)

> [!Tip]
> 如果您想要重複此練習，或嘗試不同的 AI 擴充逐步解說，請刪除 hotel-reviews-idxr  索引子。 刪除索引子，會將認知服務處理的免費每日交易計數器重設回零。
