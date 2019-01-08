---
title: 教學課程：搜尋 Azure Blob 儲存體中的非結構化資料
description: 教學課程：使用 Azure 搜尋服務來搜尋 Blob 儲存體中的非結構化資料。
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 12/13/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: 42c67d73ee776488fbe932676f61cb7166c2984b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599827"
---
# <a name="tutorial-search-unstructured-data-in-cloud-storage"></a>教學課程：搜尋雲端儲存體中的非結構化資料

在本教學課程中，您會了解如何使用 [Azure 搜尋服務](../../search/search-what-is-azure-search.md)，運用 Azure Blob 儲存體中儲存的資料來搜尋非結構化資料。 非結構化資料是未依預先定義方式組織或沒有資料模型的資料。 .txt 檔案即為其中一例。

本教學課程會要求您擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立資源群組
> * 建立儲存體帳戶
> * 建立容器
> * 將資料上傳至容器
> * 透過入口網站建立搜尋服務
> * 將搜尋服務連線至儲存體帳戶
> * 建立資料來源
> * 設定索引
> * 建立索引子
> * 使用搜尋服務來搜尋容器

## <a name="prerequisites"></a>必要條件

每個儲存體帳戶都必須屬於 Azure 資源群組。 資源群組是用來群組 Azure 服務的邏輯容器。 當您建立儲存體帳戶時，可以選擇建立新的資源群組，或使用現有的資源群組。 本教學課程會建立新的資源群組。

登入 [Azure 入口網站](http://portal.azure.com)。

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

系統已為您備妥一個範例資料集，以便您在本教學課程中使用。 下載 [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)，並將它解壓縮至它本身的資料夾。

這個範例包含取自 [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) 的文字檔。 本教學課程會使用它們作為可供以「Azure 搜尋服務」服務搜尋的範例文字檔。

## <a name="create-a-container"></a>建立容器

容器類似資料夾，用來儲存 Blob。

在本教學課程中，您會使用單一容器來儲存取自 clinicaltrials.gov 的文字檔。

1. 移至 Azure 入口網站中您的儲存體帳戶。

2. 選取 [Blob 服務] 下方的 [瀏覽 Blob]。

3. 新增容器。

4. 將容器命名為 **data**，然後針對公用存取層級選取 [容器]。

5. 選取 [確定] 以建立容器。

  ![非結構化搜尋](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>上傳範例資料

您現在已經有容器，可以將範例資料上傳至其中。

1. 選取容器，然後選取 [上傳]。

2. 選取 [檔案] 欄位旁邊的藍色資料夾圖示，然後瀏覽至您解壓縮範例資料的本機資料夾。

3. 選取所有解壓縮的檔案，然後選取 [開啟]。

4. 選取 [上傳] 以開始上傳程序。

  ![非結構化搜尋](media/storage-unstructured-search/upload.png)

上傳程序可能需要一些時間。

完成之後，請返回您的 data 容器來確認文字檔已上傳。

  ![非結構化搜尋](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>建立搜尋服務

「Azure 搜尋服務」是搜尋即服務雲端解決方案，可為開發人員提供 API 和工具來新增資料搜尋體驗。

針對本教學課程，您會使用搜尋服務來搜尋從 clinicaltrials.gov 取得的文字檔。

1. 移至 Azure 入口網站中您的儲存體帳戶。

2. 向下捲動，然後選取 [Blob 服務] 下方的 [新增 Azure 搜尋服務]。

3. 在 [匯入資料] 下方，選取 [挑選您的服務]。

4. 選取 [按一下這裡建立新搜尋服務]。

5. 在 [新增搜尋服務] 的 [URL] 欄位中，輸入您搜尋服務的唯一名稱。

6. 在 [資源群組] 下方，選取 [使用現有的]，然後選擇您稍早建立的資源群組。

7. 針對 [定價層]，選取 [免費] 層，然後按一下 [選取]。

8. 選取 [建立] 以建立搜尋服務。

  ![非結構化搜尋](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>將您的搜尋服務連接到您的容器

您現在已有搜尋服務，可以將它附加到您的 Blob 儲存體。 本節逐步解說完成選擇資料來源、建立索引以及建立索引子的程序。

1. 移至您的儲存體帳戶。

2. 選取 [Blob 服務] 下方的 [新增 Azure 搜尋服務]。

3. 選取 [匯入資料] 內的 [搜尋服務]，然後按一下您在上一節中建立的搜尋服務。 這會開啟 [新增資料來源]。

### <a name="create-a-data-source"></a>建立資料來源

  資料來源指定要編製索引的資料以及如何存取資料。 同一個搜尋服務可以多次使用資料來源。

1. 輸入資料來源的名稱。 在 [要擷取的資料] 下方，選取 [內容與中繼資料]。 資料來源指定要對 Blob 的哪些部分編製索引。

2. 因為您使用的 Blob 是文字檔，所以請將 [剖析模式] 設定為 [文字]。

  ![非結構化搜尋](media/storage-unstructured-search/datasources.png)

3. 選取 [儲存體容器]，列出可用的儲存體帳戶。

4. 選取儲存體帳戶，然後選取您先前建立的容器。

  ![非結構化搜尋](media/storage-unstructured-search/datacontainer.png)

5. 按一下 [選取] 以返回 [新增資料來源]，然後選取 [確定] 以繼續進行操作。

### <a name="configure-the-index"></a>設定索引

  索引是可搜尋資料來源中的欄位集合。 您需在這些欄位上設定參數，以便讓搜尋服務知道應該以哪些方式搜尋資料。

1. 在 [匯入資料] 中，選取 [自訂目標索引]。

2. 在 [索引名稱] 欄位中，輸入索引名稱。

3. 選取 **metadata_storage_name** 下方 [可擷取] 屬性的核取方塊。

  ![非結構化搜尋](media/storage-unstructured-search/valuestoselect.png)

4. 選取 [確定] 以顯示 [建立索引子]。

索引的參數以及提供這些參數的屬性十分重要。 這些參數會指定要儲存「什麼」資料，而屬性則指定「如何」儲存該資料。

[欄位名稱] 資料行包含這些參數。 下表提供可用屬性和其描述的清單。

#### <a name="field-attributes"></a>欄位屬性

| 屬性 | 說明 |
| --- | --- |
| *金鑰* |字串，提供每一份文件的唯一識別碼，用於查閱文件。 每個索引必須有一個索引鍵。 只有一個欄位可以做為索引鍵，而且其類型必須設定為 Edm.String。 |
| *Retrievable* |指定搜尋結果中是否可傳回某欄位。 |
| *Filterable* |允許欄位用於篩選查詢。 |
| *Sortable* |允許查詢使用此欄位排序搜尋結果。 |
| *Facetable* |允許欄位用於使用者自我引導篩選的多面向導覽結構中。 通常，欄位若包含您可以用來將多份文件群組在一起的重複值 (例如，在單一品牌或服務類別下的多份文件)，最適合以 Facet 形式運作。 |
| *Searchable* |將欄位標記為可供全文檢索。 |

### <a name="create-an-indexer"></a>建立索引子

  索引子會以搜尋索引連接資料來源，並提供排程以對資料進行編製索引。

1. 在 [名稱] 欄位中輸入名稱，然後選取 [確定]。

  ![非結構化搜尋](media/storage-unstructured-search/exindexer.png)

2. 您會返回 [匯入資料]。 請選取 [確定] 以完成連線程序。

您現在已成功將 Blob 連接至您的搜尋服務。 入口網站需要幾分鐘的時間，才會顯示已填入索引。 不過，搜尋服務會立即開始編製索引，讓您可以立即開始搜尋。

## <a name="search-your-text-files"></a>搜尋文字檔

若要搜尋您的檔案，請在您新建之搜尋服務的索引內開啟搜尋總管。

下列步驟示範在何處尋找搜尋總管，並提供一些範例查詢：

1. 移至所有資源，然後尋找新建立的搜尋服務。

  ![非結構化搜尋](media/storage-unstructured-search/exampleurl.png)

2. 選取您的索引以將它開啟。

  ![非結構化搜尋](media/storage-unstructured-search/overview.png)

3. 選取 [搜尋總管] 以開啟搜尋總管，您可以在其中對資料進行即時查詢。

  ![非結構化搜尋](media/storage-unstructured-search/indexespane.png)

4. 查詢字串欄位為空時，請選取 [搜尋]。 空的查詢會傳回 Blob 中的「所有」資料。

  ![非結構化搜尋](media/storage-unstructured-search/emptySearch.png)

### <a name="perform-a-full-text-search"></a>執行全文檢索搜尋

在 [查詢字串] 欄位中輸入 **Myopia**，然後選取 [搜尋]。 此步驟會起始檔案內容的搜尋，然後傳回包含"Myopia" 一字的內容子集。

  ![非結構化搜尋](media/storage-unstructured-search/secondMyopia.png)

### <a name="perform-a-system-properties-search"></a>執行系統屬性搜尋

除了全文檢索搜尋之外，您也可以使用 `$select` 參數，建立依系統屬性進行搜尋的查詢。

請將 `$select=metadata_storage_name` 輸入到查詢字串中，然後按 **Enter** 鍵。 這會導致僅傳回該特定欄位。

查詢字串將會直接修改 URL，因此不允許有空格。 若要搜尋多個欄位，請使用逗號，例如：`$select=metadata_storage_name,metadata_storage_path`

定義索引時，`$select` 參數只能與標示為可擷取的欄位搭配使用。

  ![非結構化搜尋](media/storage-unstructured-search/metadatasearchunstructured.png)

您現在已完成本教學課程，並且具有一組可搜尋的未結構化資料。

## <a name="clean-up-resources"></a>清除資源

若要移除您已建立的資源，最簡單的方式是刪除資源群組。 移除資源群組時也會刪除群組內包含的所有資源。 在下列範例中，移除資源群組時也會移除儲存體帳戶和資源群組本身。

1. 在 Azure 入口網站中，移至您訂用帳戶中的資源群組清單。
2. 選取您想要刪除的資源群組。
3. 選取 [刪除資源群組] 按鈕，然後在刪除欄位中輸入資源群組的名稱。
4. 選取 [刪除] 。

## <a name="next-steps"></a>後續步驟

依循下列連結，以深入了解如何使用「Azure 搜尋服務」來為文件編製索引：

> [!div class="nextstepaction"]
> [使用 Azure 搜尋服務在 Azure Blob 儲存體中對文件編製索引](../../search/search-howto-indexing-azure-blob-storage.md)
