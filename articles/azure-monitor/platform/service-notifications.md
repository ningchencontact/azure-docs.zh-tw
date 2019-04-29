---
title: 什麼是 Azure 服務健康情況通知？
description: 藉由服務健康情況通知，您可以檢視由 Microsoft Azure 發佈的服務健康情況訊息。
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: ab4aa2f37d5a883c83b8ee09b5de6551ebf13d2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883601"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>使用 Azure 入口網站檢視服務健康情況通知

Azure 會發佈服務健康情況通知，其中包含您訂用帳戶下資源的相關資訊。 這些通知是活動記錄事件的子類別，在活動記錄上也找得到。 根據不同類別，服務健康情況通知可以是告知性質或是可操作的性質。

服務健康情況通知有多種類別：  

- **需採取動作：** Azure 可能注意到您的帳戶中發生異常狀況，並協助您解決這個問題。 Azure 會傳送通知給您，其中會詳述您需要採取的動作，或 Azure 工程或支援的連絡方式。  
- **協助復原：** 事件已發生，且工程師確認您仍受到影響。 Azure 工程人員需要直接與您一起將服務還原為徹底健康的狀態。  
- **附帶事件 (Incident)︰** 影響服務的事件 (event) 目前正在影響您訂用帳戶下一個或多個資源。  
- **維護：** 可能會影響您訂用帳戶下一個或多個資源的計劃性維護活動。  
- **資訊：** 潛在的最佳化作業，可能有助於提升資源使用效率。 
- **安全性：** 緊急的安全性相關資訊，關係到 Azure 上執行的解決方案。

每個服務健康狀態通知皆會包含關於範圍以及資源所受影響的詳細資料。 詳細資料包括：

屬性名稱 | 描述
-------- | -----------
通道 | 下列其中一個值：**Admin** 或 **Operation**。
correlationId | 通常是字串格式的 GUID。 屬於相同動作的事件通常會共用同一個 correlationId。
eventDataId | 事件的唯一識別碼。
eventName | 事件的標題。
層級 | 事件的層級
resourceProviderName | 受影響資源的資源提供者名稱。
resourceType| 受影響資源的資源類型。
子狀態 | 通常是對應 REST 呼叫的 HTTP 狀態碼，但也可以包含其他描述子狀態的字串。 例如︰正常 (HTTP 狀態碼:200)、已建立 (HTTP 狀態碼:201)、已接受 (HTTP 狀態碼:202)、無內容 (HTTP 狀態碼:204)、錯誤的要求 (HTTP 狀態碼:400)、找不到 (HTTP 狀態碼:404)、衝突 (HTTP 狀態碼:409)、內部伺服器錯誤 (HTTP 狀態碼:500)、無法使用服務 (HTTP 狀態碼:503)，以及閘道逾時 (HTTP 狀態碼:504)。
eventTimestamp | Azure 服務處理要求 (與事件相對應) 而產生事件時的時間戳記。
submissionTimestamp | 當事件變成可供查詢時的時間戳記。
subscriptionId | 記錄此事件的 Azure 訂用帳戶。
status | 字串，描述作業的狀態。 以下為一些常用的值：**已啟動**、**進行中**、**成功**、**失敗**、**使用中**和**已解決**。
operationName | 作業的名稱。
category | 此屬性一律為 **ServiceHealth**。
resourceId | 受影響資源的資源識別碼。
Properties.title | 此通訊的當地語系化標題。 預設語言為英文。
Properties.communication | 與 HTML 標記通訊的詳細資料 (已當地語系化)。 預設語言為英文。
Properties.incidentType | 下列其中一個值：**ActionRequired**、**Informational**、**Incident**、**Maintenance** 或 **Security**。
Properties.trackingId | 與此事件 (event) 相關聯的附帶事件 (Incident)。 可用此屬性讓與附帶事件 (Incident) 有關的事件 (event) 相關聯。
Properties.impactedServices | 逸出的 JSON blob，描述受到附帶事件 (Incident) 影響的服務和區域。 屬性包含服務清單 (每一份都有 **ServiceName**) 和受影響區域清單 (每一份都有 **RegionName**)。
Properties.defaultLanguageTitle | 英文的通訊。
Properties.defaultLanguageContent | 英文的通訊，如 html 標記或純文字。
Properties.stage | **Incident** 和 **Security** 的可能值為 **Active**、**Resolved** 或 **RCA**。 針對 **ActionRequired** 或 **Informational**，唯一的值為 **Active**。 **Maintenance** 的可能值：**Active**、**Planned**、**InProgress**、**Canceled**、**Rescheduled**、**Resolved** 或 **Complete**。
Properties.communicationId | 與此事件相關聯的通訊。

### <a name="details-on-service-health-level-information"></a>服務健康情況層級資訊的詳細資料

**所需的動作** (properties.incidentType == ActionRequired)
- 資訊 - 避免影響現有服務所需的系統管理員動作
    
**維護** (properties.incidentType == Maintenance)
- 警告 - 緊急維護
- 資訊 - 標準計劃性維護

**資訊** (properties.incidentType == Information)
- 資訊 - 可能需要系統管理員採取動作，以避免影響現有服務

**安全性** (properties.incidentType == Security)
- 錯誤 - 跨多個區域存取多個服務的廣泛問題會影響廣泛的一組客戶。
- 警告 - 存取特定服務及/或特定區域的問題會影響一部分客戶。
- 資訊 - 影響管理作業和/或延遲的問題，不會影響服務可用性。

**服務問題** (properties.incidentType == Incident)
- 錯誤 - 跨多個區域存取多個服務的廣泛問題會影響廣泛的一組客戶。
- 警告 - 存取特定服務及/或特定區域的問題會影響一部分客戶。
- 資訊 - 影響管理作業和/或延遲的問題，不會影響服務可用性。


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>在 Azure 入口網站中檢視您的服務健康情況通知
1.  在 [Azure 入口網站](https://portal.azure.com)中，選取 [監視]。

    ![Azure 入口網站功能表的螢幕擷取畫面與選取的監視器](./media/service-notifications/home-monitor.png)

    Azure 監視器會將您所有的監視設定和資料結合成一個彙總檢視。 它會先開啟 [活動記錄檔]  區段。

3.  選取 [警示] 。

    ![監視器活動記錄的螢幕擷取畫面與選取的警示](./media/service-notifications/service-health-summary.png)
4. 選取 [+新增活動記錄警示] 並設定警示，以確保您會在未來收到服務通知。 如需詳細資訊，請參閱[建立服務通知的活動記錄警示](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。

## <a name="next-steps"></a>後續步驟
每當[發佈服務健康情況通知時，即接收警示通知](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。  
深入了解[活動記錄警示](../../azure-monitor/platform/activity-log-alerts.md)。

