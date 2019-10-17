---
title: 將全文檢索搜尋新增至 Azure Blob 儲存體
titleSuffix: Azure Search
description: 在 Azure 搜尋服務中建立全文檢索搜尋索引時，將內容解壓縮並將結構新增至 Azure blob。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 24886d8b8a7b679f6474789748a002c8d045a746
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331285"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>使用 Azure 搜尋服務將全文檢索搜尋新增至 Azure blob 資料

搜尋 Azure Blob 儲存體中儲存的各種內容類型可能是難以解決的問題。 不過，只要按幾下滑鼠，就可以使用[Azure 搜尋服務](search-what-is-azure-search.md)來編制索引並搜尋 blob 的內容。 Azure 搜尋服務具有內建的整合，可透過[*blob 索引子*](search-howto-indexing-azure-blob-storage.md)從 blob 儲存體編制索引，以新增資料來源感知功能來編制索引。

## <a name="supported-content-types"></a>支援的內容類型

藉由在容器上執行 Blob 索引子，您可以使用單一查詢從下列內容類型中提取文字和中繼資料：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

（選擇性）您可以使用*技能集*的形式附加[AI 擴充](search-blob-ai-integration.md)，以從 blob 建立新的資訊和結構，包括影像檔案的文字標記法。 新增 AI 擴充會展開內容類型清單，以包含 JPEG 和 PNG。 它新增了影像處理技能（例如[光學字元辨識（OCR））](cognitive-search-skill-ocr.md)和[視覺功能](cognitive-search-skill-image-analysis.md)的識別，讓您可以為每個影像中找到的視覺內容編制索引。

## <a name="search-through-your-blob-metadata"></a>搜尋您的 Blob 中繼資料
若要簡化任何內容類型的 Blob 排序，常見案例是為每個 Blob 的自訂中繼資料和系統屬性編製索引。 如此一來，所有 blob 的資訊都會編制索引，不論檔案類型為何（儲存在 Azure 搜尋服務的索引中）。 您可以使用新的索引，在所有 Blob 儲存體內容中繼續排序、篩選和 facet。

### <a name="indexing-json-blobs"></a>編制 JSON blob 的索引
可以將 Azure 搜尋服務設定為擷取在包含 JSON 的 Blob 中找到的結構化內容。 Azure 搜尋服務可以讀取 JSON Blob，並將結構化內容剖析成 Azure 搜尋服務文件的適當欄位。 Azure 搜尋服務也會採用包含 JSON 物件陣列的 Blob，並將每個元素對應至不同的 Azure 搜尋服務文件。 您可以設定剖析模式，以影響索引子所建立之 JSON 物件的類型。

## <a name="how-to-get-started"></a>如何開始進行

您可以直接在儲存體帳戶入口網站頁面中啟動。 按一下 [新增 Azure 搜尋服務] 會啟動一個流程，您可以在其中選取現有的 Azure 搜尋服務或建立新的服務。 如果您建立新的服務，您將會跳脫儲存體帳戶的入口網站經驗。 您可以瀏覽回到儲存體入口網站頁面，然後重新選取 [新增 Azure 搜尋服務] 選項，便可在其中選取現有的服務。 

![](./media/search-blob-storage-integration/blob-blade.png)

如果您在相同的訂用帳戶中已經有現有的搜尋服務，按一下 [**新增] Azure 搜尋服務**會開啟 [匯入資料] 嚮導，讓您可以立即逐步執行編制索引、擴充和索引定義。

您也可以[建立 Azure 搜尋服務服務](search-create-index-portal.md)，並定義從 blob 容器提取內容的 blob 索引子。

下列快速入門和教學課程會使用 Blob 資料：

+ [使用 REST Api 為半結構化 blob 編制索引](search-semi-structured-data.md)
+ [在入口網站中建立 AI 擴充管線](cognitive-search-quickstart-blob.md)
+ [在中建立 AI 擴充管線C#](cognitive-search-tutorial-blob-dotnet.md)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定 blob 索引子](search-howto-indexing-azure-blob-storage.md) 
