---
title: "什麼是 Azure 服務健全狀況通知？ | Microsoft Docs"
description: "服務健全狀況通知可讓您檢視發行的 Microsoft Azure 服務健全狀況訊息。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: 198aa74622c0f938aabe6540e2321e16aa9beb21
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>使用 Azure 入口網站來檢視服務健全狀況通知

服務健全狀況通知由 Azure 中，發行，並包含您的訂用帳戶下對資源的相關資訊。 這些通知是一種活動的子類別記錄事件，也可以在活動記錄檔中找到。 服務健全狀況通知可以是參考或可採取的動作，視類別而定。

有各種類別的服務健全狀況通知：  

- **所需的動作：** Azure 可能會注意到尋常會發生在您的帳戶，並協助您解決這個問題。 Azure 會傳送通知，請詳細描述您需要採取的動作或連絡 Azure 工程或支援人員。  
- **協助復原：**已發生事件，而且工程師已經確認還是有影響。 Azure 工程人員需要直接與您一起將服務還原為徹底健康的狀態。  
- **事件：**事件影響服務的目前會影響一個或多個訂用帳戶中的資源。  
- **維護：**可能會影響一個或多個訂用帳戶下對資源的計劃性的維護活動。  
- **資訊：**潛在的最佳化，以幫助提升資源使用。 
- **安全性：**緊急與安全性相關的資訊，關於您在 Azure 執行的方案。

每個服務健康狀態通知皆會包含關於範圍以及資源所受影響的詳細資料。 詳細資料包括：

屬性名稱 | 說明
-------- | -----------
通道 | 下列值之一： **Admin**或**作業**。
correlationId | 通常是字串格式的 GUID。 事件屬於相同的動作通常會共用相同的相互關聯識別碼。
eventDataId | 事件的唯一識別碼。
eventName | 事件的標題。
層級 | 事件層級。 下列值之一：**重大**，**錯誤**，**警告**，**參考**，或**Verbose**.
resourceProviderName | 受影響資源的資源提供者名稱。
resourceType| 受影響資源的資源類型。
子狀態 | 通常對應的其餘部分的 HTTP 狀態碼呼叫，但是也可以包含其他描述子狀態的字串。 例如: [確定] (HTTP 狀態碼： 200)，建立 (HTTP 狀態碼： 201)、 接受 (HTTP 狀態碼： 202)、 沒有內容 (HTTP 狀態碼： 204)，不正確的要求 (HTTP 狀態碼： 400)，找不到 (HTTP 狀態碼： 404)，衝突 (HTTP 狀態碼： 409)，內部伺服器錯誤 (HTTP 狀態碼： 500)，則 Service 無法使用 (HTTP 狀態碼： 503)，以及閘道逾時 (HTTP 狀態碼： 504)。
eventTimestamp | 產生事件的處理要求對應到事件的 Azure 服務時的時間戳記。
submissionTimestamp | 當事件變成可供查詢時的時間戳記。
subscriptionId | 記錄此事件的 Azure 訂用帳戶。
status | 字串，描述作業的狀態。 某些常見的值：**已啟動**，**進行中**， **Succeeded**，**失敗**， **Active**，和**解析**。
operationName | 作業的名稱。
category | 此屬性一律為**ServiceHealth**。
ResourceId | 受影響資源的資源識別碼。
Properties.title | 此通訊的當地語系化標題。 預設語言為英文。
Properties.communication | 與 HTML 標記通訊的詳細資料 (已當地語系化)。 預設語言為英文。
Properties.incidentType | 下列值之一： **AssistedRecovery**， **ActionRequired**，**資訊**，**事件**， **維護**，或**安全性**。
Properties.trackingId | 與此事件相關聯的事件。 可用此屬性讓與附帶事件 (Incident) 有關的事件 (event) 相關聯。
Properties.impactedServices | 描述的服務和此事件影響的區域逸出的 JSON blob。 屬性包含一份服務，每一個都有**ServiceName**，以及一份受影響的區域中，每一個都有**區域名稱**。
Properties.defaultLanguageTitle | 以英文通訊。
Properties.defaultLanguageContent | 英文做為 HTML 標記或純文字中的通訊。
Properties.stage | 可能值**AssistedRecovery**， **ActionRequired**，**資訊**，**事件**，和**安全性**是**Active**或**已解決**。 如**維護**，它們是： **Active**，**已計劃**， **InProgress**， **Canceled**， **重新排程**，**解析**，或**完成**。
Properties.communicationId | 與此事件相關聯的通訊。


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>在 Azure 入口網站中檢視您的服務健全狀況通知
1.  在 [Azure 入口網站](https://portal.azure.com)中，選取 [監視]。

    ![螢幕擷取畫面的 Azure 入口網站 功能表與選取的監視器](./media/monitoring-service-notifications/home-monitor.png)

    Azure 監視器會將您所有的監視設定和資料結合成一個彙總檢視。 它會先開啟 [活動記錄檔]  區段。

3.  選取 [警示] 。

    ![螢幕擷取畫面的監視活動的記錄檔，與選取的警示](./media/monitoring-service-notifications/service-health-summary.png)
4. 選取**+ 新增活動記錄檔警示**，並設定警示，以確保您會收到通知的未來服務通知。 如需詳細資訊，請參閱[服務通知上建立活動記錄檔警示](monitoring-activity-log-alerts-on-service-notifications.md)。

## <a name="next-steps"></a>後續步驟
接收[警示通知，只要服務健康情況通知](monitoring-activity-log-alerts-on-service-notifications.md)張貼。  
深入了解[活動記錄警示](monitoring-activity-log-alerts.md)。
