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
ms.openlocfilehash: cf07d19966c08a63b9aa50475622aa0a1e5e1600
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236533"
---
## <a name="business-disaster-recovery"></a>業務災害復原

本節說明 Azure Time Series Insights 可讓應用程式和服務即使在災害發生時執行的功能 (**商務災害復原**)。

### <a name="high-availability"></a>高可用性

作為 Azure 服務，時間序列深入解析提供某些**高可用性**功能在 Azure 區域層級使用備援。 比方說，Microsoft Azure 支援透過 Azure 的災害復原功能**跨區域可用性**功能。

透過 Azure （和也可用於任何的 Time Series Insights 執行個體） 提供額外的高可用性功能包括：

1. **容錯移轉**：Azure 提供[異地複寫和載入平衡](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)。
1. **還原資料**並**儲存體復原**:Azure 提供[保存和復原資料的數個選項](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)。
1. **Site Recovery**功能透過[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/)。

請務必啟用這些為您的裝置和使用者提供全域、 跨區域、 高可用性的 Azure 功能。

> [!NOTE]
> 如果 Azure 設定為啟用**跨區域可用性**，額外的跨區域可用性就不需要設定 Azure Time Series Insights 內。

### <a name="iot-and-event-hubs"></a>IoT 與事件中樞

某些 Azure IoT 服務也包含內建的商務災害復原功能：

1. [IoT 中樞高可用性災害復原](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)包括內部區域備援。
1. [事件中樞原則](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)。
1. [Azure 儲存體備援](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。

Time Series Insights 整合其他服務提供額外的災害復原的機會。 比方說，傳送至事件中樞的遙測資料可能會保存到 Azure Blob 儲存資料庫的備份。

### <a name="time-series-insights"></a>時間序列深入解析

有數種方式來保留時間序列深入解析資料、 應用程式，以及執行，即使它們中斷的服務。 您也可能會決定您的 Azure 時間序列環境的完整、 重複的備份複本，就需要：

1. 為特定 TSI**容錯移轉執行個體**資料重新導向和流量。
1. 進行稽核和資料保留。

一般情況下，複製 TSI 環境的最佳方式是建立備份的 Azure 區域中的第二個 TSI 環境。 事件也會從您的主要事件來源傳送到這個次要的環境。 請確定您使用第二個且專用的取用者群組，並遵循該來源的商務災害復原指導方針 （在上面提供）。

具體來說，若要建立重複的環境：

1. 在第二個區域中建立的環境 ([在 Azure 入口網站中建立新的 Time Series Insights 環境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started))。
1. 建立您事件來源的第二個專用取用者群組。
1. 該事件來源連接到新的環境，並確定指定的第二個、 專用的取用者群組。
1. 檢視時間序列深入解析[IoT 中樞](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)並[事件中樞](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)文件。

最後：

* 如果您的主要區域在災害事件期間受到影響，請將作業重新路由傳送到備份時間序列深入解析環境。
* 使用您的第二個區域備份和復原所有 TSI 遙測和查詢資料。

> [!IMPORTANT]
> * 請注意，在容錯移轉時可能會遇到延遲。
> * 容錯移轉也可能會暫時突然增加導致在訊息處理作業會重新進行路由。
> * 如需詳細資訊，請參閱[在時間序列深入解析中減少延遲](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)。