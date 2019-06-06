---
title: 規劃調整您的 Azure 時間序列深入解析環境規模 | Microsoft Docs
description: 本文說明如何遵循最佳作法，當您計劃的 Azure Time Series Insights 環境。 涵蓋的領域包括儲存體容量、 資料保留、 輸入容量、 監視和商務持續性和災害復原 (BCDR)。
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 9d9f9b7fe7aafa927f023e1d4e30e079a26f7fab
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431041"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>規劃 Azure 時間序列深入解析 GA 環境

本文說明如何規劃 Azure 時間序列深入解析公開上市 (GA) 環境根據預期的輸入速率和資料保留需求。

## <a name="video"></a>影片

**觀看這段影片以深入了解在 Azure 時間序列深入解析，以及如何規劃它的資料保留期**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>最佳作法

若要開始使用 Time Series Insights，最好是如果您知道您要推送的分鐘數和您要儲存資料的時間長度的資料量。  

如需這兩個時間序列深入解析 SKU 的容量和保留之詳細資訊，請參閱[時間序列深入解析定價](https://azure.microsoft.com/pricing/details/time-series-insights/)。

若要最規劃您的 Time Series Insights 環境的長期成功，請考慮下列屬性：

- <a href="#storage-capacity">儲存體容量</a>
- <a href="#data-retention">資料保留期限</a>
- <a href="#ingress-capacity">輸入容量</a>
- <a href="#shape-your-events">塑造您的事件</a>
- <a href="#ensure-that-you-have-reference-data">確保您已經找備妥的參考資料</a>

## <a name="storage-capacity"></a>儲存體容量

依預設，時間序列深入解析會保留資料是根據您佈建的儲存體數量 (單位&#215;的每個單位的儲存體數量) 和輸入。

## <a name="data-retention"></a>資料保留

您可以變更**資料保留時間**Time Series Insights 環境中的設定。 您可以啟用最多 400 天保留期。 

時間序列深入解析有兩種模式。 一種模式最佳化以確保您的環境具有最新的資料。 預設為開啟，這種模式。 

另一個模式最佳化以確保符合的保留期限制。 在第二個模式中，如果符合環境的整體儲存體容量的情況下，會暫停輸入。 

您可以調整保留，並在 Azure 入口網站中環境的 [設定] 頁面上的兩個模式之間切換。

您可以在時間序列深入解析環境中設定最大值為 400 天的資料保留期。

### <a name="configure-data-retention"></a>設定資料保留期

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取您的時間序列深入解析環境。

1. 在  **Time Series Insights 環境**窗格下方**設定**，選取**設定**。

1. 在  **（以天為單位） 的資料保留時間**方塊中，輸入介於 1 到 400 的值。

   [![設定保留期](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> 若要深入了解如何實作適當的資料保留原則，請參閱[如何設定保留期](./time-series-insights-how-to-configure-retention.md)。

## <a name="ingress-capacity"></a>輸入容量

將焦點放在規劃您的 Time Series Insights 環境的第二個區域是輸入容量。 輸入容量是每分鐘配置的衍生。

節流的觀點而言，封包大小為 32 KB 的輸入資料封包會被視為 32 個事件，每個 1KB 大小而定。 允許的事件大小上限為 32 KB。 大於 32 KB 的資料封包會被截斷。

下表摘要說明每個單位的每個 Time Series Insights SKU 輸入容量：

|SKU  |每個月的事件計數  |每個月的事件大小  |每分鐘的事件計數  |每分鐘的事件大小  |
|---------|---------|---------|---------|---------|
|S1     |   3,000 萬     |  30 GB     |  720    |  720 KB   |
|S2     |   3 億    |   300 GB   | 7,200   | 7,200 KB  |

您可以在單一環境中將 S1 或 S2 SKU 的容量增加至 10 個單位。 您無法從 S1 環境移轉至 S2。 您無法從 S2 環境移轉至 S1。

針對輸入容量，請先判斷您需要以每月為基礎的總輸入。 接下來，判斷您每分鐘需求為何。 

節流和延遲扮演角色的每分鐘的容量。 如果您的程式會持續 24 小時內的資料輸入的高峰，時間序列深入解析可以 「 趕上 」 兩次的速率在上表所列的輸入速率。

例如，如果您有單一 S1 SKU，您將資料輸入速率為 720 事件每分鐘，尖峰少於一小時的速率或較少的 1,440 事件的資料速率，沒有明顯的延遲中您的環境。 不過，如果您超過每分鐘超過一小時的 1,440 事件時，您可能會遇到資料視覺化並可藉由可供您的環境中的查詢延遲。

您可能不知道您要推送的事先多少資料。 在此情況下，您可以在其中找到資料遙測[Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)並[Azure 事件中樞](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/)在 Azure 入口網站訂用帳戶中。 遙測可協助您判斷如何佈建您的環境。 使用**計量**各自的事件來源，以檢視其遙測的 Azure 入口網站中的窗格。 如果您了解事件來源的計量，就可以更有效地規劃並佈建時間序列深入解析環境。

### <a name="calculate-ingress-requirements"></a>計算輸入需求

若要計算您輸入的需求：

- 確認您的輸入容量高於您平均每分鐘費率，而且您的環境夠大，無法處理您預期的輸入相當於兩次您容量少於一小時。

- 如果輸入高峰的上一次的時間超過 1 小時，使用高峰率作為平均值。 佈建容量來處理高峰率的環境。

### <a name="mitigate-throttling-and-latency"></a>減少節流和延遲

如需關於如何避免節流和延遲的資訊，請參閱[減少節流和延遲](time-series-insights-environment-mitigate-latency.md)。

## <a name="shape-your-events"></a>塑形您的事件

請務必確定您傳送事件至 Time Series Insights 支援您要佈建的環境的大小的方式。 （相反地，您可以將對應的環境，以 Time Series Insights 讀取的事件數目和每個事件的大小。）務必也認為要進行配量和篩選所使用之屬性的相關資料的查詢時。

> [!TIP]
> 檢閱 「 JSON 形式 」 中的文件[將事件傳送](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events)。

## <a name="ensure-that-you-have-reference-data"></a>請確定您已參考資料

A*參考資料集*是項目來擴展您事件來源中的事件集合。 Time Series Insights 輸入引擎會聯結參考資料集從事件來源與對應的資料列的每個事件。 增強的事件就可供查詢。 聯結根據**主索引鍵**定義在參考資料集中的資料行。

> [!NOTE]
> 參考資料不會回溯加入。 只有目前和未來的輸入資料會比對，並且聯結至參考資料集，它已設定，並上傳之後。 如果您打算將大量的歷程記錄資料傳送至時間序列深入解析和不先上傳或建立 Time Series Insights 參考資料，您可能必須取消復原您的工作 (提示： 不有趣)。  

若要深入了解如何建立、 上傳及管理 Time Series Insights 參考資料，請參閱我們[參考資料集的文件](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>後續步驟

- 從建立開始[在 Azure 入口網站中新的 Time Series Insights 環境](time-series-insights-get-started.md)。

- 了解如何[新增事件中樞的事件來源](time-series-insights-how-to-add-an-event-source-eventhub.md)至時間序列深入解析。

- 了解如何[設定 IoT 中樞事件來源](time-series-insights-how-to-add-an-event-source-iothub.md)。
