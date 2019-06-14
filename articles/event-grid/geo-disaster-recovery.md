---
title: Azure Event Grid 中的異地災害復原 |Microsoft Docs
description: 描述如何 Azure Event Grid 支援異地災害復原 (GeoDR) 自動。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66307313"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure Event Grid 中的伺服器端異地災害復原
Event Grid 現在會有自動地理災害復原 (GeoDR) 的不只適用於新的中繼資料，但所有現有的網域、 主題和事件訂用帳戶。 如果整個 Azure 區域故障時，Event Grid 已經具有所有您事件相關的基礎結構的中繼資料同步處理至配對的區域。 您的新事件，就會開始再次流動，而您不需要介入。 

嚴重損壞修復是使用兩個計量測量：

- [復原點目標 (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)： 分鐘或小時的資料可能會遺失。
- [復原時間目標 (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)： 分鐘數小時的服務可能會向下。

Event Grid 自動容錯移轉會有不同的 Rpo 和 Rto 的中繼資料 （事件訂閱等） 和資料 （事件）。 如果您需要不同的規格，從下列的項目時，您仍可實作您自己[容錯移轉使用本主題的用戶端健全狀況 api](custom-disaster-recovery.md)。

## <a name="recovery-point-objective-rpo"></a>復原點目標 (RPO)
- **中繼資料的 RPO**： 零分鐘。 每當建立 Event Grid 中的資源，立即會跨區域複寫。 在容錯移轉時，沒有中繼資料會遺失。
- **資料 RPO**:如果您的系統狀況良好並趕上區域性容錯移轉時的現有流量，則事件的 RPO 約 5 分鐘。

## <a name="recovery-time-objective-rto"></a>復原時間目標 (RTO)
- **中繼資料的 RTO**:雖然通常它是更快速地在 60 分鐘內，Event Grid 就會開始接受建立/更新/刪除對主題和訂用帳戶。
- **資料 RTO**:如同中繼資料，它通常會更快，不過在 60 分鐘內，Event Grid 會開始接受新的流量區域的容錯移轉之後。

> [!NOTE]
> Event Grid 上 GeoDR 的中繼資料的成本是： 0 美元。


## <a name="next-steps"></a>後續步驟
如果您想要實作您自己的用戶端容錯移轉邏輯，請參閱[# 建置您自己的災害復原在 Event Grid 自訂主題](custom-disaster-recovery.md)
