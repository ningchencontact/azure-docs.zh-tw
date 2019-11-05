---
title: Azure 時間序列深入解析預覽中的資料儲存體和輸入 | Microsoft Docs
description: 了解 Azure 時間序列深入解析預覽中的資料儲存體和輸入。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: 0b61e194bdea5fd8272ffc0fc9e16a2d80d3cf60
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989697"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure 時間序列深入解析預覽中的資料儲存體和輸入

本文說明 Azure 時間序列深入解析 Preview 的資料儲存體和輸入的更新。 內容涵蓋基礎儲存體結構、檔案格式和時間序列識別碼屬性。 它也會討論基礎輸入程式、輸送量和限制。

## <a name="data-ingress"></a>資料輸入

在時間序列深入解析 Preview 中，資料輸入原則會決定資料的來源，以及資料應該有的格式。

[![時間序列模型總覽](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>輸入原則

時間序列深入解析 Preview 支援時間序列深入解析目前支援的相同事件來源：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中樞](../event-hubs/event-hubs-about.md)

時間序列深入解析 Preview 針對每個實例最多支援兩個事件來源。
  
Azure 時間序列深入解析支援透過 Azure IoT 中樞或 Azure 事件中樞提交的 JSON。 若要優化您的 IoT JSON 資料，請瞭解[如何塑造 json](./time-series-insights-send-events.md#supported-json-shapes)。

### <a name="data-storage"></a>資料儲存體

當您建立時間序列深入解析預覽版的隨用隨付 SKU 環境時，您會建立兩個 Azure 資源：

* 時間序列深入解析的預覽環境，可以選擇性地包含暖存放區功能。
* 適用于冷資料儲存的 Azure 儲存體一般用途 V1 blob 帳戶。

您的暖存放區中的資料只能透過[時間序列查詢](./time-series-insights-update-tsq.md)和[Azure 時間序列深入解析預覽瀏覽器](./time-series-insights-update-explorer.md)來使用。 

時間序列深入解析 Preview 會以[Parquet 檔案格式](#parquet-file-format-and-folder-structure)將您的冷存放區資料儲存至 Azure Blob 儲存體。 時間序列深入解析 Preview 會以獨佔方式管理此冷存放區資料，但可供您直接讀取為標準 Parquet 檔案。

> [!WARNING]
> 身為 Azure Blob 儲存體帳戶的擁有者，如果有冷存放區資料所在，您就可以完整存取帳戶中的所有資料。 此存取權包括 [寫入] 和 [刪除] 許可權。 請勿編輯或刪除時間序列深入解析預覽寫入的資料，因為這可能會導致資料遺失。

### <a name="data-availability"></a>資料可用性
時間序列深入解析預覽分割和索引資料，以獲得最佳查詢效能。 資料會在索引後變成可供查詢。 正在內嵌的資料量可能會影響此可用性。

> [!IMPORTANT]
> 公開上市（GA）版本的時間序列深入解析會在從事件來源讀取後的60秒內提供資料。 在預覽期間，您可能會經歷較長的時間，資料才會變成可用。 如果您遇到超過60秒的重大延遲，請洽詢我們。

### <a name="scale"></a>調整

根據預設，時間序列深入解析 Preview 支援每個環境每秒最多 1 mb （MB/s）的初始輸入規模。 如果您需要，可以使用最多 16 MB/秒的輸送量。 如果您需要增強的調整支援，請洽詢我們。

您可以取得事件來源的額外輸入和調整功能：

* [IoT 中心](../iot-hub/iot-hub-scaling.md)
* [事件中樞](../event-hubs/event-hubs-scalability.md)

## <a name="azure-storage"></a>Azure 儲存體

本節說明 Azure 時間序列深入解析 Preview 的相關 Azure 儲存體詳細資料。

如需 Azure Blob 儲存體的完整說明，請參閱[儲存體 blob 簡介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-storage-account"></a>您的儲存體帳戶

當您建立時間序列深入解析預覽版的隨用隨付環境時，系統會將 Azure 儲存體一般用途 V1 blob 帳戶建立為長期冷存放區。  

時間序列深入解析 Preview 會在您的 Azure 儲存體帳戶中最多發佈兩個事件的複本。 初始複本具有依內嵌時間排序的事件，而且一律會保留，因此您可以使用其他服務來存取它。 您可以使用 Spark、Hadoop 和其他熟悉的工具來處理原始的 Parquet 檔案。 

時間序列深入解析 Preview 會重新分割 Parquet 檔案，以針對時間序列深入解析查詢進行優化。 此資料的重新分割複本也會一併儲存。

在公開預覽期間，資料會無限期地儲存在您的 Azure 儲存體帳戶中。

### <a name="writing-and-editing-time-series-insights-blobs"></a>寫入和編輯時間序列深入解析 Blob

若要確保查詢效能和資料可用性，請勿編輯或刪除時間序列深入解析 Preview 所建立的任何 blob。

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>從時間序列深入解析預覽存取和匯出資料

您可能想要存取在時間序列深入解析預覽瀏覽器中查看的資料，以便與其他服務搭配使用。 例如，您可以使用您的資料在 Power BI 中建立報表，或使用 Azure Machine Learning Studio 將機器學習模型定型。 或者，您可以使用您的資料在 Jupyter 筆記本中轉換、視覺化和模型化。

您可以透過三種一般方式存取資料：

* 透過時間序列深入解析預覽總管。 您可以從 [explorer] 將資料匯出成 CSV 檔案。 如需詳細資訊，請參閱 [Azure 時間序列深入解析預覽總管](./time-series-insights-update-explorer.md)。
* 從時間序列深入解析預覽 API。 您可以在 `/getRecorded`連接 API 端點。 若要深入了解此 API，請參閱[時間序列查詢](./time-series-insights-update-tsq.md)。
* 直接透過 Azure 儲存體帳戶。 您需要您用來存取時間序列深入解析預覽資料的任何帳戶的讀取存取權。 如需詳細資訊，請參閱[管理儲存體帳戶資源的存取](../storage/blobs/storage-manage-access-to-resources.md)。

### <a name="data-deletion"></a>刪除資料

請勿刪除您的時間序列深入解析預覽檔案。 您應該只在時間序列深入解析 Preview 內管理相關資料。

## <a name="parquet-file-format-and-folder-structure"></a>Parquet 檔案格式和資料夾結構

Parquet 是開放原始碼的單欄式檔案格式，專為有效率的儲存和效能所設計。 基於這些理由，時間序列深入解析 Preview 會使用 Parquet。 它會依時間序列識別碼分割資料，以獲得大規模的查詢效能。  

如需 Parquet 檔案類型的詳細資訊，請參閱[Parquet](https://parquet.apache.org/documentation/latest/)檔。

時間序列深入解析預覽會儲存資料的複本，如下所示：

* 第一個，初始複本是透過內嵌時間來分割，並大致地以抵達的順序儲存資料。 資料位於 `PT=Time` 資料夾中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 第二個是以時間序列識別碼群組分割複本，並位於 `PT=TsId` 資料夾中：

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

在這兩種情況下，時間值都會對應到 blob 建立時間。 [`PT=Time`] 資料夾中的資料會保留下來。 [`PT=TsId`] 資料夾中的資料將會針對一段時間的查詢優化，而且不會保持靜態。

> [!NOTE]
> * `<YYYY>` 對應到四位數年份標記法。
> * `<MM>` 對應到兩位數的月份標記法。
> * `<YYYYMMDDHHMMSSfff>` 對應到具有四位數年份（`YYYY`）、兩位數月份（`MM`）、兩位數的日（`DD`）、兩位數的小時（`HH`）、兩位數的分鐘（`MM`）和三位數毫秒（`SS`）的時間戳記標記法（`fff`）。

時間序列深入解析預覽事件會對應至 Parquet 檔案內容，如下所示：

* 每個事件都會對應至單一資料列。
* 每個資料列都包含**時間**戳資料行與事件時間戳記。 時間戳記屬性絕不會是 null。 如果事件來源中未指定時間戳記屬性，它會預設為**事件排入佇列的時間**。 時間戳記一律為 UTC 格式。
* 每個資料列都包含建立時間序列深入解析環境時所定義的時間序列識別碼資料行。 屬性名稱包含 `_string` 尾碼。
* 以遙測資料傳送的所有其他屬性都會對應到以 `_string` （string）、`_bool` （布林）、`_datetime` （datetime）或 `_double` （double）為結尾的資料行名稱，視屬性類型而定。
* 這個對應配置適用于檔案格式的第一版，其參考為**V = 1**。 隨著這項功能的演進，名稱可能會遞增。

## <a name="next-steps"></a>後續步驟

- 閱讀[Azure 時間序列深入解析預覽儲存體和](./time-series-insights-update-storage-ingress.md)輸入。

- 深入瞭解新的[資料模型](./time-series-insights-update-tsm.md)。
