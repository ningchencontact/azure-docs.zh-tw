---
ms.topic: include
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: 8a3c630b54ff95a9b1200e2421c787a514a0aa52
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431028"
---
## <a name="business-disaster-recovery"></a>業務災害復原

本節說明 Azure Time Series Insights 可讓應用程式和服務執行，即使在災害發生時的功能 (稱為*商務災害復原*)。

### <a name="high-availability"></a>高可用性

作為 Azure 服務，時間序列深入解析提供某些*高可用性*藉由在 Azure 區域層級使用備援功能。 例如，Azure 都支援透過 Azure 的災害復原功能*跨區域可用性*功能。

透過 Azure （和也可用於任何的 Time Series Insights 執行個體） 提供的其他高可用性功能包括：

- **容錯移轉**：Azure 提供[異地複寫與負載平衡](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)。
- **還原資料**並**儲存體復原**:Azure 提供[保存和復原資料的數個選項](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)。
- **站台復原**:Azure 提供的站台復原功能，透過[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/)。

請確定您啟用相關的 Azure 功能，來為您的裝置和使用者提供全域、 跨區域的高可用性。

> [!NOTE]
> 如果啟用跨區域的可用性設定 Azure，Azure Time Series Insights 被不需要任何額外的跨區域可用性設定。

### <a name="iot-and-event-hubs"></a>IoT 與事件中樞

某些 Azure IoT 服務也包含內建的商務災害復原功能：

- [IoT 中樞高可用性災害復原](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)，包括內部區域備援
- [事件中樞原則](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure 儲存體複寫](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Time Series Insights 整合其他服務提供額外的災害復原的機會。 比方說，傳送至事件中樞的遙測資料可能會保存到 Azure Blob 儲存資料庫的備份。

### <a name="time-series-insights"></a>時間序列深入解析

有數種方式來保留您的 Time Series Insights 資料、 應用程式和服務執行，即使它們中斷。 

不過，您可能判斷您的 Azure 時間序列環境的完整備份副本也是必要的用於下列用途：

- 作為*容錯移轉執行個體*專為資料重新導向和流量傳送到的時間序列深入解析
- 若要保留的資料和稽核資訊

一般情況下，重複的 Time Series Insights 環境的最佳方式是建立備份的 Azure 區域中的第二個的 Time Series Insights 環境。 事件也會從您的主要事件來源傳送到這個次要的環境。 請確定您使用第二個、 專用的取用者群組。 如先前所述，請遵循該來源的商務災害復原指導方針。

若要建立重複的環境：

1. 在第二個區域中建立環境。 如需詳細資訊，請參閱 <<c0> [ 在 Azure 入口網站中建立新的 Time Series Insights 環境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)。
1. 建立您事件來源的第二個專用取用者群組。
1. 將該事件來源連線到新環境。 請確定您指定的第二個、 專用的取用者群組。
1. 檢視時間序列深入解析[IoT 中樞](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)並[事件中樞](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)文件。

如果發生事件：

1. 如果您的主要區域在災害事件期間受到影響，請將作業重新路由傳送到備份時間序列深入解析環境。
1. 使用您的第二個區域備份和復原所有的 Time Series Insights 的遙測和查詢資料。

> [!IMPORTANT]
> 如果發生容錯移轉：
> 
> * 也可能會發生延遲。
> * 訊息處理暫時突然增加可能會發生，因為作業會重新進行路由。
> 
> 如需詳細資訊，請參閱[在時間序列深入解析中減少延遲](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)。

