---
ms.topic: include
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 10/09/2019
ms.openlocfilehash: 61842a8f410d972c21614f625f7d4f36476cb239
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72259093"
---
## <a name="business-disaster-recovery"></a>業務災害復原

本節說明可讓應用程式和服務繼續執行的 Azure 時間序列深入解析功能，即使發生嚴重損壞（稱為「*商務*嚴重損壞修復」）也一樣。

### <a name="high-availability"></a>高可用性

身為 Azure 服務，時間序列深入解析使用 Azure 區域層級的冗余來提供特定的*高可用性*功能。 例如，Azure 支援透過 Azure 的*跨區域可用性*功能的嚴重損壞修復功能。

透過 Azure 提供的額外高可用性功能（也適用于任何時間序列深入解析實例）包括：

- **容錯移轉**：Azure 提供[異地複寫和負載平衡](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)。
- **資料還原**和**儲存體**復原：Azure 提供[數個選項來保留及復原資料](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)。
- **Site recovery**：Azure 透過[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/)提供 site recovery 功能。
- **Azure 備份**：[Azure 備份](https://docs.microsoft.com/azure/backup/backup-architecture)同時支援 Azure VM 的內部部署和雲端備份。

請確定您已啟用相關的 Azure 功能，為您的裝置和使用者提供全域、跨區域的高可用性。

> [!NOTE]
> 如果 Azure 設定為啟用跨區域可用性，Azure 時間序列深入解析不需要額外的跨區域可用性設定。

### <a name="iot-and-event-hubs"></a>IoT 和事件中樞

某些 Azure IoT 服務也包含內建的商務嚴重損壞修復功能：

- [IoT 中樞高可用性嚴重損壞修復](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)，包括內部區域冗余
- [事件中樞原則](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure 儲存體複寫](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

將時間序列深入解析與其他服務整合，可提供額外的嚴重損壞修復機會。 例如，傳送至事件中樞的遙測可能會保存到備份 Azure Blob 儲存體資料庫。

### <a name="time-series-insights"></a>時間序列深入解析

有數種方式可讓您的時間序列深入解析資料、應用程式和服務保持執行狀態，即使它們已中斷。 

不過，基於下列目的，您可能也會決定需要 Azure 時間序列環境的完整備份複本：

- 作為*容錯移轉實例*，專門用於時間序列深入解析將資料和流量重新導向至
- 保留資料和審核資訊

一般來說，複製時間序列深入解析環境的最佳方式，是在備份 Azure 區域中建立第二個時間序列深入解析環境。 事件也會從您的主要事件來源傳送到此次要環境。 請確定您使用的是第二個專用取用者群組。 遵循該來源的商務嚴重損壞修復指導方針，如先前所述。

若要建立重複的環境：

1. 在第二個區域中建立環境。 如需詳細資訊，請參閱[在 Azure 入口網站中建立新的時間序列深入解析環境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)。
1. 建立您事件來源的第二個專用取用者群組。
1. 將該事件來源連線到新環境。 請確定您指定第二個專用取用者群組。
1. 請參閱時間序列深入解析[IoT 中樞](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)和[事件中樞](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)檔。

如果發生事件：

1. 如果您的主要區域在災害事件期間受到影響，請將作業重新路由傳送到備份時間序列深入解析環境。
1. 使用您的第二個區域來備份和復原所有時間序列深入解析遙測和查詢資料。

> [!IMPORTANT]
> 如果發生容錯移轉：
> 
> * 可能也會發生延遲。
> * 當作業被重新路由時，可能會發生訊息處理的瞬間尖峰。
> 
> 如需詳細資訊，請參閱[在時間序列深入解析中減少延遲](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)。

