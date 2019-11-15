---
title: 使用 Power BI 連線至知識存放區 (預覽)
titleSuffix: Azure Cognitive Search
description: 使用 Power BI 連接 Azure 認知搜尋知識存放區 (預覽)，以進行分析和探索。
author: lisaleib
manager: nitinme
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 7b12f0f14003389d36e2df5bcffe7828c135cf2b
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715484"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>使用 Power BI 連線到知識存放區

> [!IMPORTANT] 
> 知識存放區目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供預覽功能。 目前的入口網站支援有限，而且沒有 .NET SDK 支援。

在本文中，了解如何使用 Power BI Desktop 應用程式中的 Power Query 來連線及探索知識存放區。 您可以透過範本更快地開始使用，或從頭開始建立自訂儀表板。

+ 遵循[在 Azure 入口網站中建立知識存放區](knowledge-store-create-portal.md)或[使用 REST 建立 Azure 認知搜尋知識存放區](knowledge-store-create-rest.md)中的步驟，建立本逐步解說中所使用的知識存放區範例。 您也需要用來建立知識存放區的 Azure 儲存體帳戶名稱，以及其來自 Azure 入口網站的存取金鑰。

+ [安裝 Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>範例 Power BI 範本 - 僅限 Azure 入口網站

如果您[使用 Azure 入口網站建立知識存放區](knowledge-store-create-portal.md)，您可以使用 [Azure 認知搜尋 Power BI 範本範例](https://github.com/Azure-Samples/cognitive-search-templates)來檢視和實驗 Power BI 視覺效果。 當您逐步執行 [匯入資料]  精靈時，此範本也可供下載。

範例範本會自動執行本文其餘部分所述的設定步驟。 不過，如果您使用 REST API 建立知識存放區，請略過此範本並使用本文中的其餘章節，將知識存放區連線到 Power BI。 從[與 Power BI 連線](#connect-with-power-bi)開始著手。

範例範本包含數個視覺效果，例如 WordCloud 和 Network Navigator。 範本中的某些視覺效果 (例如位置對應和實體圖表檢視器) 不會顯示[在 Azure 入口網站中建立知識存放區](knowledge-store-create-portal.md)中所建立範例知識存放區的資料。 這是因為已使用 [匯入資料]  精靈中僅提供的 AI 擴充子集。

![範例 Azure 認知搜尋 Power BI 範本](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "範例 Power BI 範本")

## <a name="connect-with-power-bi"></a>使用 Power BI 進行連接

1. 啟動 Power BI Desktop 並按一下 [取得資料]  。

1. 在 [取得資料]  中，依序選取 [Azure]  和 [Azure 資料表儲存體]  。

1. 按一下 [ **連接**]。

1. 針對 [帳戶名稱或 URL]  ，輸入您的 Azure 儲存體帳戶名稱 (系統會為您建立完整的 URL)。

1. 如果出現提示，請輸入儲存體帳戶金鑰。

1. 選取 hotelReviewsSsDocument  、hotelReviewsSsKeyPhrases  和 hotelReviewsSsPages  表格。 這些資料表是旅館評論資料的 Azure 資料表預測，包含建立知識存放區時所選取的 AI 擴充。

1. 按一下 [載入]  。

1. 在頂端的功能區中，按一下 [編輯查詢]  以開啟 [Power Query 編輯器]  。

   ![開啟 Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "開啟 Power Query")

1. 選取 hotelReviewsSsDocument  ，然後移除 PartitionKey  、RowKey  和 Timestamp  資料行。 

   ![編輯資料表](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "編輯資料表")

1. 按一下資料表右上方具有反向箭號的圖示，以展開 [內容]  。 當資料行清單出現時，選取所有資料行，然後取消選取以 'metadata ' 開頭的資料行。 按一下 [確定]  以顯示選取的資料行。

   ![編輯資料表](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "展開內容")

1. 按一下資料行左上方的 ABC-123 圖示，變更下列資料行的資料類型。

   + 針對 content.latitude  和 Content.longitude  ，選取 [十進位數字]  。
   + 針對 Content.reviews_date  和 Content.reviews_dateAdded  ，選取 [日期/時間]  。

   ![變更資料類型](media/knowledge-store-connect-power-bi/powerbi-change-type.png "變更資料類型")

1. 選取 hotelReviewsSsPages  ，然後重複步驟 9 和 10 以刪除資料行並展開 [內容]  。
1. 將 Content.SentimentScore  的資料類型變更為 [十進位數字]  。
1. 選取 hotelReviewsSsKeyPhrases  ，然後重複步驟 9 和 10 以刪除資料行並展開 [內容]  。 此資料表無須修改資料類型。

1. 按一下命令列上的 [關閉並套用]  。

1. 按一下左側導覽窗格中的 [模型] 圖格，然後驗證 Power BI 是否顯示全部三個資料表之間的關聯性。

   ![驗證關聯性](media/knowledge-store-connect-power-bi/powerbi-relationships.png "驗證關聯性")

1. 按兩下每個關聯性，並確定 [交叉篩選方向]  設定為 [兩者]  。  這可讓您的視覺效果在套用篩選條件後重新整理。

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>清除

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源]  或 [資源群組]  連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。

## <a name="next-steps"></a>後續步驟

若要了解如何使用儲存體總管來探索此知識存放區，請參閱下列文章。

> [!div class="nextstepaction"]
> [使用儲存體總管檢視](knowledge-store-view-storage-explorer.md)

若要了解如何使用 REST API 和 Postman 建立知識存放區，請參閱下列文章。  

> [!div class="nextstepaction"]
> [在 REST 中建立知識存放區](knowledge-store-howto.md)
