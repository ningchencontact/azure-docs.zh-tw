---
title: Azure 時間序列深入解析預覽中的資料儲存體和輸入 | Microsoft Docs
description: 了解 Azure 時間序列深入解析預覽中的資料儲存體和輸入。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: eba4c70a25cba2e456ed418a98b938f2029c4c1f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438358"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure 時間序列深入解析預覽中的資料儲存體和輸入

本文說明如何從 Azure 時間序列深入解析預覽中變更資料儲存體和輸入。 內容涵蓋基礎儲存體結構、檔案格式和時間序列識別碼屬性。 本文也會討論基礎輸入程序、輸送量和限制。

## <a name="data-storage"></a>資料儲存體

當您建立時間序列深入解析預覽隨用隨付 SKU 環境時，您會建立兩個資源：

* 時間序列深入解析環境。
* Azure 儲存體一般用途 V1 帳戶，資料將儲存於其中。

時間序列深入解析預覽使用 Azure Blob 儲存體搭配 Parquet 檔案類型。 時間序列解析管理所有資料作業，包括建立 Blob、編製索引，以及在 Azure 儲存體帳戶中分割資料。 您可以使用 Azure 儲存體帳戶來建立這些 Blob。

就和其他 Azure 儲存體 Blob 一樣，時間序列深入解析建立的 Blob 可讓您讀取並寫入，以支援各種不同的整合案例。

> [!TIP]
> 如果您讀取或寫入 Blob 太過頻繁，可能會對時間序列深入解析效能造成不良影響。

如需 Azure Blob 儲存體的概觀，請參閱[儲存體 Blob 簡介](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)。

如需 Parquet 檔案類型的詳細資訊，請參閱 [Azure 儲存體中支援的檔案類型](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format)。

## <a name="parquet-file-format"></a>Parquet 檔案格式

Parquet 是資料行導向的資料檔案格式，設計用來提供：

* 互通性
* 空間效率
* 查詢效率

時間序列深入解析選擇 Parquet 是因為它提供有效率的資料壓縮和編碼配置，以及可處理大量複雜資料的增強效能。

若要深入了解 Parquet 檔案格式，請參閱 [Parquet 文件](https://parquet.apache.org/documentation/latest/) \(英文\)。

## <a name="event-structure-in-parquet"></a>Parquet 中的事件結構

時間序列深入解析會以下列兩種格式建立並儲存 Blob 複本：

1. 第一個格式，依抵達時間分割的初始複本：

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * 依抵達時間分割之 Blob 的 Blob 建立時間。

1. 第二個格式，依時間序列識別碼的動態群組分割的重新分割複本：

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * 依時間序列識別碼分割之 Blob 的 Blob 內最小事件時間戳記。

> [!NOTE]
> * `<YYYY>` 對應至 4 位數年份表示法。
> * `<MM>` 對應至 2 位數月份表示法。
> * `<YYYYMMDDHHMMSSfff>` 對應至使用 4 位數年份 (`YYYY`)、2 位數月份 (`MM`)、2 位數天數 (`DD`)、2 位數小時 (`HH`)、2 位數分鐘 (`MM`)、2 位數秒鐘 (`SS`) 和 3 位數毫秒 (`fff`) 的時間戳記表示法。

時間序列深入解析事件會對應至 Parquet 檔案內容，如下所示：

* 每個事件都會對應至單一資料列。
* 內建的**時間戳記**資料行會有事件時間戳記。 時間戳記屬性絕不會是 Null。 如果事件來源中未指定時間戳記屬性，其預設值會是**事件來源加入佇列的時間**。 時間戳記採用 UTC。 
* 所有對應至資料行的其他屬性，都會以 `_string` (字串)、`_bool` (布林值)、`_datetime` (日期時間) 和 `_double` (雙精度浮點數) 結尾，取決於屬性類型。
* 這是第一版的檔案格式對應結構描述，我們稱為 **V=1**。 隨著這項功能發展，名稱將會遞增為 **V=2**、**V=3**，依此類推。

## <a name="partitions"></a>分割數

每個時間序列深入解析預覽環境都必須有時間序列識別碼屬性和時間戳記屬性，以作為唯一識別它的依據。 您的時間序列識別碼作為您資料的邏輯分割，並提供時間序列深入解析預覽環境自然界限，以跨實體分割區散發資料。 實體分割區的管理，是受 Azure 儲存體帳戶中的時間序列深入解析預覽所管理。

時間序列深入解析使用動態分割，透過卸除並重新建立分割區來最佳化儲存體與查詢效能。 時間序列深入解析預覽動態分割演算法會嘗試防止單一實體分割區讓資料有多個不同的邏輯分割區。 換句話說，分割演算法會將單一時間序列識別碼特定的所有資料以獨佔方式保留在 Parquet 檔案中，而不會與其他時間序列識別碼交錯。 動態分割演算法也會嘗試保留單一時間序列識別碼中的事件原始順序。

一開始，在輸入階段，資料會依時間戳記分割，如此一來指定時間範圍內的單一邏輯分割區就可分散到多個實體分割區。 單一實體分割區也可能包含多個或所有邏輯分割區。 由於 Blob 的大小限制，即使已最佳化分割，單一邏輯分割區也可能佔用多個實體分割區。

> [!NOTE]
> 根據預設，時間戳記的值是您設定之事件來源中的「已加入佇列時間」訊息。 

如果您正在上傳歷史資料或批次訊息，請將您要與資料一起儲存的值指派給時間戳記屬性，其會對應至適當的時間戳記。 時間戳記屬性有區分大小寫。 如需詳細資訊，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。

## <a name="physical-partitions"></a>實體分割區

實體分割區是區塊 Blob，其儲存於您的儲存體帳戶中。 Blob 的實際大小可能會有所不同，因為其大小取決於推送速率。 不過，我們預期 Blob 的大小約在 20 MB 至 50 MB。 此預期讓時間序列深入解析小組選取以 20 MB 作為最佳化查詢效能的大小。 此大小可能隨時間經過而改變，取決於檔案大小和資料輸入的速度。

> [!NOTE]
> * Blob 的大小設為 20 MB。
> * Azure Blob 偶爾會被卸除並重新建立以重新分割，來獲取更佳的效能。
> * 此外，相同的時間序列深入解析資料可能存在於兩個以上的 Blob 中。

## <a name="logical-partitions"></a>邏輯分割區

邏輯分割區是一種實體分割區內的分割區，當中儲存與某個單一分割區索引鍵值相關的所有資料。 時間序列深入解析預覽根據下列兩個屬性，以邏輯方式分割每個 Blob：

* **時間序列識別碼**：事件資料流和模型中所有時間序列深入解析資料的分割區索引鍵。
* **時間戳記**：初始輸入的時間。

時間序列深入解析預覽根據這兩個屬性提供高效能查詢。 這兩個屬性也提供快速傳遞時間序列深入解析資料最有效的方法。

務必選取適當的時間序列識別碼，因為它是不可變的屬性。 如需詳細資訊，請參閱[選擇時間序列識別碼](./time-series-insights-update-how-to-id.md)。

## <a name="your-azure-storage-account"></a>您的 Azure 儲存體帳戶

### <a name="storage"></a>儲存體

當您建立時間序列深入解析隨用隨付環境時，您會建立兩個資源：時間序列深入解析環境和 Azure 儲存體一般用途 V1 帳戶 (資料將儲存於其中)。 我們選擇 Azure 儲存體一般用途 V1 作為預設資源，是因為其互通性、價格和效能。 

時間序列深入解析在您的 Azure 儲存體帳戶中，每個事件最多會發佈兩個複本。 初始複本一定會被保留，以便您可以使用其他服務，快速地查詢它。 您可以輕鬆地使用 Spark、Hadoop 和其他熟悉的工具，透過原始 Parquet 檔案橫跨時間序列識別碼，因為這些引擎都支援基本檔案名稱篩選。 依年份和月份分組 Blob，是列出自訂作業特定時間範圍內 Blob 的實用方式。 

此外，時間序列深入解析會重新分割 Parquet 檔案，為時間序列深入解析 API 最佳化。 最近重新分割的檔案也會儲存。

在公開預覽期間，資料會無限期地儲存在您的 Azure 儲存體帳戶中。

### <a name="writing-and-editing-time-series-insights-blobs"></a>寫入和編輯時間序列深入解析 Blob

為了確保查詢效能和資料可用性，請勿編輯或刪除時間序列深入解析建立的任何 Blob。

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>從時間序列深入解析預覽存取和匯出資料

您可能想在時間序列深入解析預覽總管中存取儲存的資料，以便與其他服務搭配使用。 例如，您可能想使用資料以在 Power BI 中製作報表、使用 Azure Machine Learning Studio 執行機器學習，或在 Jupyter Notebooks 與記事本應用程式中搭配使用。

您可以透過三種一般方式存取資料：

* 透過時間序列深入解析預覽總管。
* 透過時間序列深入解析預覽 API。
* 直接透過 Azure 儲存體帳戶。

#### <a name="from-the-time-series-insights-preview-explorer"></a>透過時間序列深入解析預覽總管

您可以從時間序列深入解析預覽總管將資料會出為 CSV 檔案。 如需詳細資訊，請參閱 [Azure 時間序列深入解析預覽總管](./time-series-insights-update-explorer.md)。

#### <a name="from-the-time-series-insights-preview-apis"></a>透過時間序列深入解析預覽 API

您可在 `/getRecorded` 觸達 API 端點。 若要深入了解此 API，請參閱[時間序列查詢](./time-series-insights-update-tsq.md)。

#### <a name="from-an-azure-storage-account"></a>透過 Azure 儲存體帳戶

* 無論您使用何種帳戶來存取時間序列深入解析資料，都需要讀取權限。 如需詳細資訊，請參閱[管理儲存體帳戶資源的存取](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources)。

* 如需從 Azure Blob 儲存體直接讀取資料的詳細資訊，請參閱[從儲存體帳戶來回移動資料](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

* 若要從 Azure 儲存體帳戶匯出資料：

    * 請先確定您的帳戶符合匯出資料的必要需求。 如需詳細資訊，請參閱[儲存體匯入和會出需求](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements)。

    * 若要了解從 Azure 儲存體帳戶匯出資料的其他方式，請參閱[從 Blob 匯入和匯出資料](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs)。

### <a name="data-deletion"></a>刪除資料

請勿刪除 Blob，因為時間序列深入解析預覽其中會維護有關 Blob 的中繼資料。

## <a name="ingress"></a>輸入

### <a name="time-series-insights-ingress-policies"></a>時間序列深入解析輸入原則

時間序列深入解析預覽支援時間序列深入解析目前支援的相同事件來源和檔案類型。

支援的事件來源包括：

- Azure IoT 中樞
- Azure 事件中心
  
  > [!NOTE]
  > Azure 事件中樞執行個體支援 Kafka。

支援的檔案類型包括：

* JSON：如需有關我們可處理之支援的 JSON 樣貌詳細資訊，請參閱[如何塑形 JSON](./time-series-insights-send-events.md#json)。

### <a name="data-availability"></a>資料可用性

時間序列深入解析預覽使用 Blob 大小最佳化策略來編製資料索引。 資料經過編製索引後即可供查詢，其編製方式以資料輸入量與速度為基礎。

> [!IMPORTANT]
> * 時間序列深入解析正式運作 (GA) 版本將可在叫用事件資料來源的 60 秒內讓資料可供使用。 
> * 在預覽期間，可能需要較長的時間才能提供資料。 
> * 如果您遇到任何明顯的延遲，請務必與我們連絡。

### <a name="scale"></a>調整

時間序列深入解析預覽針對每個環境支援最多 6 Mbps 的初始輸入規模。 即將推出增強的規模支援。 我們計劃更新我們的文件以反映那些增強功能

## <a name="next-steps"></a>後續步驟

閱讀 [Azure 時間序列深入解析預覽儲存體和輸入](./time-series-insights-update-storage-ingress.md)。

閱讀新的[資料模型](./time-series-insights-update-tsm.md)。

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
