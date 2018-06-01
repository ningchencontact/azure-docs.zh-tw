---
title: Microsoft Azure 和 Azure 監視器中的傳統警示概觀 | Microsoft Docs
description: 警示可讓您監視 Azure 資源度量、事件或記錄檔，並在您所指定條件符合時收到通知。
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: robb
ms.openlocfilehash: 039ab7226b4ea7e011e1c0cbb4cac528b5237483
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204000"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>什麼是 Microsoft Azure 中的傳統警示？

> [!NOTE]
> 本文說明如何建立舊版傳統計量警示。 「Azure 監視器」現已支援[新版的近乎即時計量警示和新的警示體驗](monitoring-overview-unified-alerts.md)。 
>

警示可讓您對資料設定條件，並在最近的監視資料符合條件時收到通知。


## <a name="alerts-on-azure-monitor-data"></a>Azure 監視器資料的相關警示
可用的傳統警示類型有兩種：計量警示和活動記錄警示。

* **傳統計量警示**：當指定的計量值超出您指派的閾值時，就會觸發此警示。 當警示為「已啟動」時 (超出閾值且符合警示條件時)，以及當警示為「已解決」時 (再次超出閾值且不再符合條件時)，警示會產生通知。 如需較新的計量警示，請參閱下文。

* **傳統活動記錄警示** - 產生符合您所指派篩選準則之「活動記錄」事件時會觸發的資料流記錄警示。 這些警示只有「已啟動」這個狀態，因為警示引擎只會將篩選準則套用至任何新的事件。 您可以使用這些警示，在發生新的服務健康狀態事件時，或是在使用者或應用程式於您的訂用帳戶中執行作業時 (例如「刪除虛擬機器」)，收到通知。

針對透過 Azure 監視器提供的診斷記錄資料，建議將資料路由傳送至 Log Analytics (先前稱為 OMS) 並使用 Log Analytics 查詢警示。 Log Analytics 現在使用[新的警示方法](monitoring-overview-unified-alerts.md) 

下圖摘要說明 Azure 監視器中的資料來源，以及就概念而言如何發出該資料的警示。

![警示的說明](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Azure 監視器警示的分類法 (傳統)
Azure 使用下列詞彙來描述傳統警示及其功能：
* **警示** - 符合時會啟動之準則 (一或多個規則或條件) 的定義。
* **作用中** - 符合傳統警示所定義準則時的狀態。
* **已解決** - 先前符合傳統警示所定義的準則，但之後已不再符合時的狀態。
* **通知** - 根據變成作用中之傳統警示而採取的動作。
* **動作** - 傳送給通知接收者的特定呼叫 (例如以電子郵件傳送位址或張貼到 Webhook URL)。 通知通常可觸發多個動作。

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>如何收到 Azure 監視器傳統警示傳來的通知？
在過去，Azure 的警示來自不同的服務，各自使用其專屬的內建通知方法。 

現在，Azure 監視器提供可重複使用的通知群組，稱為「動作群組」。 動作群組會指定一組通知接收者，每當啟動參考此動作群組的警示時，所有接收者都會收到該通知。 這可讓您在許多警示物件之間重複使用一組接收者 (例如您隨時待命的工程師清單)。 除了電子郵件地址、簡訊號碼和一些其他動作，動作群組的通知支援還包括張貼到 Webhook URL。  如需詳細資訊，請參閱[動作群組](monitoring-action-groups.md)。 

較舊的傳統活動記錄警示會使用動作群組。

不過，較舊的計量警示不會使用動作群組。 您可以改為設定下列動作： 
* 將電子郵件通知傳送至服務管理員、共同管理員或您指定的其他電子郵件。
* 呼叫 webhook，可讓您啟動其他自動化動作。

Webhook 可啟用自動化和修復，例如使用：
    - Azure 自動化 Runbook
    - Azure Function
    - Azure 邏輯應用程式
    - 第三方服務

## <a name="next-steps"></a>後續步驟
使用下列項目取得有關警示規則和設定這些規則的資訊：

* 深入了解[計量](monitoring-overview-metrics.md)
* [透過 Azure 入口網站設定傳統的計量警示](insights-alerts-portal.md)
* 設定[傳統的計量警示 PowerShell](insights-alerts-powershell.md)
* 設定[傳統的計量警示命令列介面 (CLI)](insights-alerts-command-line-interface.md)
* 設定[傳統的計量警示 Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* 深入了解[活動記錄](monitoring-overview-activity-logs.md)
* [透過 Azure 入口網站設定活動記錄警示](monitoring-activity-log-alerts.md)
* [透過 Resource Manager 設定活動記錄警示](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* 檢閱[活動記錄警示 webhook 結構描述](monitoring-activity-log-alerts-webhook.md)
* 深入了解[動作群組](monitoring-action-groups.md)
* 設定[較新的警示](monitor-alerts-unified-usage.md)
