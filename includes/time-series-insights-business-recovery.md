---
title: 包含檔案
description: 包含檔案
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: e3e7044148e262e6977ae3be96f7cb61218114a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388756"
---
## <a name="business-disaster-recovery"></a>業務災害復原

本節說明 Azure Time Series Insights 可讓應用程式和服務即使在災害發生時執行的功能。 這個概念稱為商務災害復原。

### <a name="high-availability"></a>高可用性

作為 Azure 服務，Time Series Insights 會提供特定的高可用性功能，藉由在 Azure 區域層級使用備援。 比方說，Microsoft Azure 支援災害復原功能，透過 Azure 的跨區域的可用性功能。

透過 Azure，以及所提供的其他高可用性功能也可用於時間序列深入解析中的任何執行個體，包括：

* **容錯移轉**：Azure 提供[異地複寫與負載平衡](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)。
* **還原資料**並**儲存體復原**:Azure 提供[保存和復原資料的數個選項](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)。
* **站台復原**:功能可透過[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/)。

請務必啟用這些為您的裝置和使用者提供全域、 跨區域的高可用性的 Azure 功能。

> [!NOTE]
> 如果 Azure 設定為啟用跨區域可用性，額外的跨區域可用性就不需要設定 Azure Time Series Insights 內。

### <a name="iot-and-event-hubs"></a>IoT 與事件中樞

某些 Azure IoT 服務也包含內建的商務災害復原功能：

* [Azure IoT 中樞高可用性災害復原](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)，其中包含內部區域備援。
* [Azure 事件中樞原則](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)。
* [Azure 儲存體備援](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。

Time Series Insights 整合其他服務提供額外的災害復原的機會。 比方說，傳送至事件中樞的遙測資料可能會保存到 Azure Blob 儲存資料庫的備份。

### <a name="time-series-insights"></a>時間序列深入解析

有數種方式來保留時間序列深入解析資料、 應用程式，以及執行，即使它們中斷的服務。 您也可能會決定您的 Azure 時間序列環境的完整備份副本，並需要：

* 為資料重新導向到流量時間序列深入解析特定容錯移轉執行個體。
* 進行稽核和資料保留。

一般情況下，重複的 Time Series Insights 環境的最佳方式是建立備份的 Azure 區域中的第二個的 Time Series Insights 環境。 事件也會從您的主要事件來源傳送到這個次要的環境。 若要使用第二個專用取用者群組，並遵循該來源的商務災害復原指導方針，如先前所提供，請確定。

具體來說，若要建立重複的環境：

1. 在第二個區域中建立環境。 如需相關指示，請參閱 <<c0> [ 在 Azure 入口網站中建立新的 Time Series Insights 環境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)。
1. 建立您事件來源的第二個專用取用者群組。
1. 將該事件來源連線到新環境。 請務必指定第二個專用取用者群組。
1. 檢視時間序列深入解析[IoT 中樞](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)並[事件中樞](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)文件。

最後：

1. 如果您的主要區域在災害事件期間受到影響，請將作業重新路由傳送到備份時間序列深入解析環境。
1. 使用您的第二個區域備份和復原所有的 Time Series Insights 的遙測和查詢資料。

> [!IMPORTANT]
> * 請注意，在容錯移轉時可能會遇到延遲。
> * 容錯移轉也可能會暫時突然增加導致在訊息處理作業會重新進行路由。
> * 如需詳細資訊，請參閱[在時間序列深入解析中減少延遲](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)。
