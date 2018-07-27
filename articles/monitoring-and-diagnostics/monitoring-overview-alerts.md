---
title: Microsoft Azure 和 Azure 監視器中的傳統警示概觀
description: 警示可讓您監視 Azure 資源度量、事件或記錄檔，並在您所指定條件符合時收到通知。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: a0abcbdaa7e998413efb717be6e0addc5607ec5c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114006"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>什麼是 Microsoft Azure 中的傳統警示？

> [!NOTE]
> 本文說明如何建立舊版傳統計量警示。 Azure 監視器現已支援[新版的近乎即時計量警示和新的警示體驗](monitoring-overview-unified-alerts.md)。 
>

藉由使用警示，您可以對資料設定條件，並在最新的監視資料符合條件時收到通知。


## <a name="alerts-on-azure-monitor-data"></a>Azure 監視器資料的相關警示
可用的傳統警示類型有兩種：計量警示和活動記錄警示。

* **傳統計量警示**：當指定的計量值超出您指派的閾值時，就會觸發此警示。 當警示「已啟動」(超過閾值且符合警示條件) 時，會產生通知。 當警示「已解決」 (再次超出閾值但不再符合條件) 時，它也會產生警示。 

* **傳統活動記錄警示**：當符合您指派之篩選準則的活動記錄產生時，會觸發此資料流記錄警示。 這些警示只有「已啟動」這個狀態，因為警示引擎會直接將篩選準則套用至任何新的事件。 當新的服務健康狀態事件發生時，或當使用者或應用程式在您的訂用帳戶中執行諸如「刪除虛擬機器」作業時，這些警示可以通知您。

若要接收可透過 Azure 監視器取得的診斷記錄資料，請將資料路由傳送到 Log Analytics (先前稱為 Operations Management Suite)，並使用 Log Analytics 查詢警示。 Log Analytics 現在使用[新的警示方法](monitoring-overview-unified-alerts.md)。 

下圖摘要顯示 Azure 監視器中的資料來源，並建議您可以如何設定這些資料的警示。

![警示的說明](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Azure 監視器警示的分類法 (傳統)
Azure 使用下列詞彙來描述傳統警示及其功能：
* **警示**：符合時會啟動之準則 (一或多個規則或條件) 的定義。
* **啟動**：符合傳統警示所定義的準則時會發生的狀態。
* **已解決**：先前符合傳統警示所定義的準則，但之後已不再符合時會發生的狀態。
* **通知**：當傳統警示變成啟動時會採取的動作。
* **動作**：傳送給通知接收者的特定呼叫 (例如，電子郵件或張貼到 Webhook URL)。 通知通常可觸發多個動作。

## <a name="how-do-i-receive-notifications-from-an-azure-monitor-classic-alert"></a>如何接收來自 Azure 監視器傳統警示的通知？
在過去，Azure 的警示來自不同的服務，各自使用其專屬的內建通知方法。 

現在，Azure 監視器提供可重複使用的通知群組，稱為「動作群組」。 動作群組可指定一組通知接受者。 當參考動作群組的警示啟動時，所有接收者都會接收到該通知。 此功能可讓您在許多警示物件之間重複使用一個接收者群組 (例如您隨時待命的工程師清單)。 動作群組支援透過各種方法的通知。 這些方法可包括張貼到 Webhook URL、傳送電子郵件、SMS 訊息，以及數個其他動作。 如需詳細資訊，請參閱[在 Azure 入口網站中建立和管理動作群組](monitoring-action-groups.md)。 

舊版傳統活動記錄警示會使用動作群組。

不過，舊版計量警示不會使用動作群組。 您可以改為設定下列動作： 
* 將電子郵件通知傳送至服務管理員、共同管理員或您指定的其他電子郵件地址。
* 呼叫 Webhook，它可讓您啟動其他自動化動作。

Webhook 可讓您使用自動化和補救措施，例如，使用以下服務：
- Azure 自動化 Runbook
- Azure Functions
- Azure 邏輯應用程式
- 第三方服務

## <a name="next-steps"></a>後續步驟
使用以下文件來取得警示規則和如何設定它們的相關資訊：

* 深入了解[計量](monitoring-overview-metrics.md)
* [使用 Azure 入口網站設定傳統計量警示](insights-alerts-portal.md)
* [使用 PowerShell 設定傳統計量警示](insights-alerts-powershell.md)
* [使用 Azure CLI 設定傳統計量警示](insights-alerts-command-line-interface.md)
* [使用 Azure 監視器 REST API 設定傳統計量警示](https://msdn.microsoft.com/library/azure/dn931945.aspx) \(英文\)
* 深入了解[活動記錄](monitoring-overview-activity-logs.md)
* [使用 Azure 入口網站設定活動記錄警示](monitoring-activity-log-alerts.md)
* [使用 Resource Manager 設定活動記錄警示](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* 檢閱[活動記錄警示 Webhook 結構描述](monitoring-activity-log-alerts-webhook.md)
* 深入了解[動作群組](monitoring-action-groups.md)
* 設定[新版警示](monitor-alerts-unified-usage.md)
