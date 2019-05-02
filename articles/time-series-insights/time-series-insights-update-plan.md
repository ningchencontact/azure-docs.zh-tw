---
title: 規劃您的 Azure 時間序列深入解析預覽環境| Microsoft Docs
description: 規劃您的 Azure 時間序列深入解析預覽版環境。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 0472f53d11ec4c990fcf6face633444fe66ba937
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702352"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>規劃您的 Azure 時間序列深入解析預覽版環境

本文描述規劃及快速開始使用 Azure 時間序列深入解析預覽版的最佳做法。

## <a name="best-practices-for-planning-and-preparation"></a>規劃和準備的最佳做法

若要開始使用時間序列深入解析，建議您先了解：

* 佈建時間序列深入解析預覽版環境之後能取得什麼。
* 您的時間序列識別碼和時間戳記屬性為何。
* 新的時間序列模型是什麼，以及如何建置您自己的。
* 如何有效率地以 JSON 格式傳送事件。 
* 時間序列深入解析商務災害復原選項。

時間序列深入解析採用預付型商業模式。 如需費用和容量的詳細資訊，請參閱[時間序列深入解析定價](https://azure.microsoft.com/pricing/details/time-series-insights/)。

## <a name="the-time-series-insights-preview-environment"></a>時間序列深入解析預覽版環境

當您佈建時間序列深入解析預覽版環境時，您會建立兩個 Azure 資源：

* 時間序列深入解析預覽版環境
* Azure 儲存體一般用途 V1 帳戶

若要開始使用，您需要三個額外項目：
 
- [時間序列模型](./time-series-insights-update-tsm.md) 
- [連線到時間序列深入解析的事件來源](./time-series-insights-how-to-add-an-event-source-iothub.md) 
- [流入事件來源的事件](./time-series-insights-send-events.md)，這些事件必須已經對應至該模型，且為有效的 JSON 格式 

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>設定您的時間序列識別碼和時間戳記屬性

若要建立新的時間序列深入解析環境，請選取一個時間序列識別碼。 此動作會當作您資料的邏輯分割。 如上述事項，請務必備妥您的時間序列識別碼。

> [!IMPORTANT]
> 時間序列識別碼「不可變」且「事後無法變更」。 在最終選取前及第一次使用前，請確認每個識別碼。

您可以選取最多三 (3) 個索引鍵來唯一區分您的資源。 如需詳細資訊，請參閱[選擇時間序列識別碼的最佳做法](./time-series-insights-update-how-to-id.md)和[儲存體與輸入](./time-series-insights-update-storage-ingress.md)。

時間戳記屬性也很重要。 您可以在新增事件來源時指定此屬性。 每個事件來源都有選擇性的時間戳記屬性，可用來依時間追蹤事件來源。 時間戳記值會區分大小寫，而且格式必須設定為每個事件來源的個別規格。

> [!TIP]
> 請確認您事件來源的格式設定和剖析需求。

保留空白時，系統會使用事件來源內的事件加入佇列時間，作為事件時間戳記。 如果您傳送歷史資料或批次事件，自訂「時間戳記屬性」比預設的「事件加入佇列時間」更有用。 如需詳細資訊，請參閱[如何在 IoT 中樞新增事件來源](./time-series-insights-how-to-add-an-event-source-iothub.md)。 

## <a name="understand-the-time-series-model"></a>了解時間序列模型

您現在可以設定您時間序列深入解析環境的時間序列模型。 使用新模型很容易就能尋找及分析 IoT 資料。 它能用來鑑藏、維護及擴充時間序列資料，並協助準備取用者可用的資料集。 該模型使用**時間序列識別碼**，它對應到的執行個體與唯一資源相關聯，且該資源有變數 (稱為類型) 和階層。 了解新的[時間序列模型](./time-series-insights-update-tsm.md)。

模型是動態的，所以隨時可以建置。 若要快速開始使用，請先建置並上傳模型，再將資料推送至時間序列深入解析中。 若要建置您的模型，請參閱[使用時間序列模型](./time-series-insights-update-how-to-tsm.md)。

對許多客戶而言，時間序列模型對應至現有的資產模型或 ERP 系統。 如果您沒有現有的模型，我們[提供](https://github.com/Microsoft/tsiclient)的預先建置使用者體驗可快速啟動並執行。 若要了解模型能如何協助您，請檢視[範例示範環境](https://insights.timeseries.azure.com/preview/demo)。 

## <a name="shape-your-events"></a>塑形您的事件

您可以確認您傳送事件到時間序列深入解析的方式。 在理想情況下，您的事件已妥善且有效率地反正規化。

良好的經驗法則：

* 將中繼資料儲存在您的時間序列模型中
* 時間序列模型、執行個體欄位和事件只包括必要資訊，例如：
  * 時間序列識別碼
  * Timestamp

如需詳細資訊，請參閱[塑形事件](./time-series-insights-send-events.md#json)。

## <a name="business-disaster-recovery"></a>業務災害復原

時間序列深入解析是高可用性服務，使用 Azure 區域層級的備援。 使用這些固有的功能不需要設定。 Microsoft Azure 平台也包含各種功能，以協助您建置具有災害復原功能或跨區域可用性的解決方案。 若要為裝置或使用者提供全域、跨區域的高可用性，可善用這類 Azure 災害復原功能。 

如需 Azure 中的商務持續性和災害復原 (BCDR) 內建功能，請參閱 [Azure 商務持續性技術指導方針](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance)。 如需使 Azure 應用程式達到高可用性和災害復原能力的策略相關架構指導方針，請參閱[為 Azure 設計復原應用程式](https://docs.microsoft.com/azure/architecture/resiliency/index)。

> [!NOTE]
> 時間序列深入解析沒有內建的 BCDR。 根據預設，Azure 儲存體、Azure IoT 中樞和 Azure 事件中樞已內建復原。

若要深入了解，請參閱：

* [Azure 儲存體複寫](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [IoT 中樞高可用性和災害復原](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)
* [Azure 事件中樞地理災害復原](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)

如果需要 BCDR，您仍可以實作復原策略。 在備份 Azure 區域中建立第二個時間序列深入解析環境。 將事件從您的主要事件來源，傳送至這個次要環境。 使用第二個專用取用者群組和該事件來源的 BCDR 指導方針。

請遵循這些步驟來建立並使用次要時間序列深入解析環境。

1. 在第二個區域中建立環境。 如需詳細資訊，請參閱[時間序列深入解析環境](./time-series-insights-get-started.md)。
1. 建立您事件來源的第二個專用取用者群組。 將該事件來源連線到新環境。 請務必指定第二個專用取用者群組。 若要深入了解，請參閱 [IoT 中樞文件](./time-series-insights-how-to-add-an-event-source-iothub.md)或[事件中樞文件](./time-series-insights-data-access.md)。
1. 如果您的主要區域在災害事件期間受到影響，請將作業重新路由傳送到備份時間序列深入解析環境。

> [!IMPORTANT]
> * 請注意，在容錯移轉時可能會遇到延遲。
> * 因為作業會重新路由傳送，所以容錯移轉也可能會造成訊息處理短暫地增加。
> * 如需詳細資訊，請參閱[在時間序列深入解析中減少延遲](./time-series-insights-environment-mitigate-latency.md)。

## <a name="next-steps"></a>後續步驟

- 深入了解[儲存體與輸入](./time-series-insights-update-storage-ingress.md)時間序列深入解析預覽版。

- 深入了解[資料模型化](./time-series-insights-update-tsm.md)時間序列深入解析預覽版。