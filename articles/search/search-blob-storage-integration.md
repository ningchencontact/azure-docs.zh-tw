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
ms.openlocfilehash: 1aec65ab08cd1c0711e51a222a8e674ef56ef508
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312192"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>使用 Azure 搜尋服務將全文檢索搜尋新增至 Azure blob 資料

搜尋 Azure Blob 儲存體中儲存的各種內容類型可能是難以解決的問題。 不過，只要按幾下滑鼠，就可以使用[Azure 搜尋服務](search-what-is-azure-search.md)來編制索引並搜尋 blob 的內容。 Azure 搜尋服務具有內建的整合，可透過[*blob 索引子*](search-howto-indexing-azure-blob-storage.md)從 blob 儲存體編制索引，以新增資料來源感知功能來編制索引。

## <a name="supported-content-types"></a>支援的內容類型

藉由在容器上執行 Blob 索引子，您可以使用單一查詢從下列內容類型中提取文字和中繼資料：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

（選擇性）您可以使用*技能集*的形式附加[AI 擴充](search-blob-ai-integration.md)，以從 blob 建立新的資訊和結構，包括影像檔案的文字標記法。 新增 AI 擴充會展開內容類型清單，以包含 JPEG 和 PNG。 它新增了影像處理技能（例如[光學字元辨識（OCR））](cognitive-search-skill-ocr.md)和[視覺功能](cognitive-search-skill-image-analysis.md)的識別，讓您可以為每個影像中找到的視覺內容編制索引。

## <a name="search-through-your-blob-metadata"></a>搜尋您的 Blob 中繼資料
若要簡化任何內容類型的 Blob 排序，常見案例是為每個 Blob 的自訂中繼資料和系統屬性編製索引。 如此一來，不論是什麼文件類型，所有 Blob 的資訊都會編製成索引。 然後便可以跨所有 Blob 儲存體的內容，進行排序、篩選、facet。

[深入了解如何編製 Blob 中繼資料的索引。](https://aka.ms/azsblobmetadataindexing)

## <a name="index-and-search-through-json-blobs"></a>檢索和搜尋 JSON Blob
可以將 Azure 搜尋服務設定為擷取在包含 JSON 的 Blob 中找到的結構化內容。 Azure 搜尋服務可以讀取 JSON Blob，並將結構化內容剖析成 Azure 搜尋服務文件的適當欄位。 Azure 搜尋服務也會採用包含 JSON 物件陣列的 Blob，並將每個元素對應至不同的 Azure 搜尋服務文件。

JSON 剖析目前無法透過入口網站設定。 [深入了解 Azure 搜尋服務中的 JSON 剖析。](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>快速入門
您可以直接從 Blob 儲存體入口網站頁面將 Azure 搜尋服務新增至 Blob。

![](./media/search-blob-storage-integration/blob-blade.png)

按一下 [新增 Azure 搜尋服務] 會啟動一個流程，您可以在其中選取現有的 Azure 搜尋服務或建立新的服務。 如果您建立新的服務，您將會跳脫儲存體帳戶的入口網站經驗。 您可以瀏覽回到儲存體入口網站頁面，然後重新選取 [新增 Azure 搜尋服務] 選項，便可在其中選取現有的服務。

## <a name="next-steps"></a>後續步驟
閱讀完整的[文件](https://aka.ms/azsblobindexer)以深入了解 Azure 搜尋服務 Blob 索引子。
