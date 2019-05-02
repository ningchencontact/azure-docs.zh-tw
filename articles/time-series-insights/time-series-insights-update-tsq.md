---
title: Azure 時間序列深入解析預覽版資料查詢 | Microsoft Docs
description: Azure 時間序列深入解析預覽版資料查詢。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 0b1c43cb82d6dc2b7f0708dee8471f077befe349
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722167"
---
# <a name="data-querying"></a>資料查詢

Azure 時間序列深入解析預覽版允許透過公用介面 API，針對事件和儲存在環境中的中繼資料進行資料查詢。 這些 API 也會在[時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)中使用。

時間序列深入解析中提供了三個主要 API 類別：

* **環境 API**：啟用時間序列深入解析環境本身的查詢。 查詢範例是呼叫端可以存取的環境清單以及環境中繼資料。

* **時間序列模型查詢 (TSM-Q) API**：對儲存在時間序列模型之環境部分中的中繼資料啟用建立、讀取、更新和刪除作業。 範例包括執行個體、類型和階層。

* **時間序列查詢 (TSQ) API**：允許擷取從來源提供者記錄的事件資料。 這些 API 可以執行轉換、合併，以及對時間序列資料執行計算的作業。

[時間序列運算式 (TSX) 語言](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)是功能強大的第四個類別。 它會使用時間序列模型來實現進階計算的組合。

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure 時間序列深入解析預覽版核心 API

支援下列核心 API。

![tsq][1]

### <a name="environment-apis"></a>環境 API

下列環境 API 可以使用：

* [取得環境 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api)：傳回呼叫端有權存取的環境清單。
* [取得環境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api)：傳回事件時間戳記 `$ts` 上的事件計數分佈。 此 API 藉由傳回事件計數 (如果有的話) 來協助判斷時間戳記中是否存在任何事件。
* [取得事件結構描述 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api)：傳回指定搜尋範圍的事件結構描述中繼資料。 此 API 可協助擷取指定搜尋範圍的結構描述中可用的所有中繼資料和屬性。

### <a name="time-series-model-query-tsm-q-apis"></a>時間序列模型查詢 (TSM-Q) API

下列的時間序列模型查詢 API 可以使用：

* [模型設定 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)：啟用對環境的預設類型和模型名稱的 get 和 patch。
* [類型 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)：在時間序列類型及其相關聯的變數上啟用 CRUD。
* [階層 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)：在時間序列階層及其相關聯的欄位路徑上啟用 CRUD。
* [執行個體 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)：在時間序列執行個體及其相關聯的執行個體欄位上啟用 CRUD。

### <a name="time-series-query-tsq-apis"></a>時間序列查詢 (TSQ) API

下列時間序列查詢 API 可以使用：

* [取得事件 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api)：啟用從事件中查詢和擷取時間序列深入解析資料，因為它們是從來源提供者記錄在時間序列深入解析中。

* [取得數列 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api)：藉由使用線路上記錄的資料，可以從擷取的事件中查詢和擷取時間序列深入解析資料。 傳回的值是以模型或提供的內嵌中定義的變數為基礎。

    >[!NOTE]
    > 即使已在模型或提供的內嵌中指定彙總子句，也會忽略它。

  取得數列 API 會為每個間隔傳回每個變數的時間序列值。 時間序列值是時間序列深入解析針對來自查詢的輸出 JSON 所使用的格式。 傳回的值是以時間序列識別碼和一組所提供的變數為基礎。

* [彙總數列 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api)：藉由取樣和彙總記錄的資料，可以從擷取的事件中查詢和擷取時間序列深入解析資料。

  彙總數列 API 會為每個間隔傳回每個變數的時間序列值。 值是以時間序列識別碼和一組所提供的變數為基礎。 彙總數列 API 透過使用儲存在時間序列模型或提供的內嵌中的變量來彙總或取樣資料，以達到降低的目的。

  支援的彙總類型：`Min`、`Max`、`Sum`、`Count`、`Average`

## <a name="next-steps"></a>後續步驟

- 深入了解[儲存體與輸入](./time-series-insights-update-storage-ingress.md)預覽版 Azure 時間序列深入解析。

- 讀取時間序列深入解析預覽[資料模型化](./time-series-insights-update-tsm.md)文章。

- 探索[最佳做法，選擇 時間序列識別碼時](./time-series-insights-update-how-to-id.md)。

<!-- Images -->
[1]: media/v2-update-tsq/tsq.png
