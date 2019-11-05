---
title: 觀看具有儲存體總管的知識存放區
titleSuffix: Azure Cognitive Search
description: 使用 Azure 入口網站的儲存體總管來查看和分析 Azure 認知搜尋知識存放區。
manager: nitinme
author: lisaleib
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9ba11b69a51a5bc563764a7e75189bed67cb2ac6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484991"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>觀看具有儲存體總管的知識存放區

> [!Note]
> 知識存放區處於預覽狀態，不應用於生產環境。 入口網站和[搜尋 REST API 版本 2019-05-06-Preview](search-api-preview.md)提供這項功能。 目前沒有 .NET SDK 支援。
>
在本文中，您將瞭解如何使用 Azure 入口網站中的儲存體總管連接到知識存放區，並加以探索。

## <a name="prerequisites"></a>必要條件

+ 遵循在 Azure 入口網站中[建立知識存放區](knowledge-store-create-portal.md)中的步驟，建立本逐步解說中所使用的範例知識存放區。

+ 您也將需要用來建立知識存放區的 Azure 儲存體帳戶名稱，以及其從 Azure 入口網站的存取金鑰。

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>在儲存體總管中查看、編輯和查詢知識存放區

1. 在 Azure 入口網站中，開啟您用來建立知識存放區的[儲存體帳戶](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。

1. 在儲存體帳戶的左側流覽窗格中，按一下 [**儲存體總管**]。

1. 展開 [**資料表]** 清單，以顯示當您在飯店上執行 [匯**入資料**] wizard 審查範例資料時所建立的 Azure 資料表預測清單。

選取任何資料表來查看擴充的資料，包括關鍵字組情感分數、緯度和經度位置資料等。

   ![在儲存體總管中查看資料表](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "在儲存體總管中查看資料表")

若要變更任何資料表值的資料類型，或變更資料表中的個別值，請按一下 [**編輯**]。 當您變更一個資料表資料列中任何資料行的資料類型時，它將會套用至所有資料列。

   ![編輯儲存體總管中的資料表](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "編輯儲存體總管中的資料表")

若要執行查詢，請按一下命令列上的 [**查詢**]，然後輸入您的條件。  

   ![儲存體總管中的查詢資料表](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "儲存體總管中的查詢資料表")

## <a name="clean-up"></a>清除

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。

## <a name="next-steps"></a>後續步驟

將此知識存放區連接到 Power BI 以進行更深入的分析，或使用 REST API 和 Postman 來建立不同的知識存放區，以進行程式碼的繼續。

> [!div class="nextstepaction"]
> [與 Power BI 連接](knowledge-store-connect-power-bi.md)
> [在 REST 中建立知識存放區](knowledge-store-howto.md)
