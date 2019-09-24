---
title: 使用 Power BI 連線到知識存放區 - Azure 搜尋服務
description: 連接 Azure 搜尋服務知識存放區與 Power BI 以進行分析和探索。
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: fa6187dc270b18f513b5ee4046d0a6c085f9ae12
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963225"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>使用 Power BI 連線到知識存放區

> [!Note]
> 知識存放區處於預覽狀態，不應用於生產環境。 [Azure 搜尋服務 REST API 版本 2019-05-06-Preview](search-api-preview.md) 會提供此功能。 目前沒有 .NET SDK 支援。
>
在本文中，您將了解如何使用 Power BI Desktop 應用程式中的 Power Query 來連接及探索知識存放區。 若要建立本逐步解說中使用的知識存放區範例，請參閱[在 Azure 入口網站中建立知識存放區](knowledge-store-create-portal.md)。

## <a name="prerequisites"></a>必要條件

+ 遵循[在 Azure 入口網站中建立知識存放區](knowledge-store-create-portal.md)中的步驟，建立本逐步解說中所使用的知識存放區範例。 您也需要用來建立知識存放區的 Azure 儲存體帳戶名稱，以及其來自 Azure 入口網站的存取金鑰。

+ [安裝 Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="connect-with-power-bi"></a>使用 Power BI 進行連接

1. 啟動 Power BI Desktop 並按一下 [取得資料]  。

1. 在 [取得資料]  中，依序選取 [Azure]  和 [Azure 資料表儲存體]  。

1. 按一下 [ **連接**]。

1. 針對 [帳戶名稱或 URL]  ，輸入您的 Azure 儲存體帳戶名稱 (系統會為您建立完整的 URL)。

1. 如果出現提示，請輸入儲存體帳戶金鑰。

1. 選取 hotelReviewsSsDocument  、hotelReviewsSsKeyPhrases  和 hotelReviewsSsPages  表格。 這些資料表是旅館評論資料的 Azure 資料表預測，包含建立知識存放區時所選取的認知服務擴充。

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

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Search to use larger data sets. 

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
