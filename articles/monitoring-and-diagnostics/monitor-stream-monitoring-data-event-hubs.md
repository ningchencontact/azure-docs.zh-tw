---
title: "監視資料至事件中心的 Azure 資料流 |Microsoft 文件"
description: "了解如何將串流處理所有的 Azure 監視資料到事件中心取得的資料插入合作夥伴 SIEM 或分析工具。"
author: johnkemnetz
manager: robb
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: johnkem
ms.openlocfilehash: 59f0cba66a5d8d2a528700861efff86967c1c748
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>資料流的 Azure 監視資料到事件中心取用外部工具

Azure 的監視器 」 提供單一管線取得存取權的所有監視資料從 Azure 環境，讓您輕鬆地設定夥伴 SIEM 和監控工具會使用該資料。 本文逐步設定不同的資料層從 Azure 環境以傳送到單一的事件中樞命名空間或事件中樞，其中外部工具所收集。

## <a name="what-data-can-i-send-into-an-event-hub"></a>到事件中心可以傳送哪些資料？ 

在 Azure 環境中，有數個 '層' 的監視資料，並從每一層存取資料的方法會有些微。 一般而言，這些層可以描述為：

- **監視資料的應用程式：**的效能和功能的程式碼撰寫，並且會在 Azure 上執行的相關資料。 監視資料的應用程式的範例包括效能追蹤、 應用程式記錄檔和使用者遙測。 監視資料的應用程式通常會收集下列方式之一：
  - 透過這類檢測您的程式碼的 sdk 來[Application Insights SDK](../application-insights/app-insights-overview.md)。
  - 執行監視的代理程式，它會接聽新的應用程式記錄檔在電腦上執行應用程式，例如[Windows Azure 診斷代理程式](./azure-diagnostics.md)或[Linux Azure 診斷代理程式](../virtual-machines/linux/diagnostic-extension.md)。
- **監視資料的客體 OS:**您的應用程式執行所在的作業系統的相關資料。 客體 OS 的監視資料的範例是 Linux syslog 或 Windows 系統事件。 若要收集此類型的資料，您需要安裝代理程式，例如[Windows Azure 診斷代理程式](./azure-diagnostics.md)或[Linux Azure 診斷代理程式](../virtual-machines/linux/diagnostic-extension.md)。
- **監視資料的 azure 資源：** Azure 資源的作業相關的資料。 某些 Azure 資源類型，例如虛擬機器沒有客體作業系統和應用程式，監視的 Azure 服務內。 其他 Azure 資源，例如網路安全性的群組，監視資料的資源已可以使用資料的最高的層 （因為沒有客體作業系統或應用程式執行這些資源中）。 此資料可以使用收集[資源的診斷設定](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)。
- **監視資料的 azure 平台：**作業和管理 Azure 訂用帳戶或租用戶的相關資料，以及相關的健全狀況和作業的 Azure 資料本身。 [活動記錄檔](./monitoring-overview-activity-logs.md)，包括服務健全狀況資料，以及 Active Directory 稽核是平台監視資料的範例。 這個收集資料，可以使用診斷的設定。

從任何層可將資料傳送到事件中心，其中要提取到協力廠商工具。 下節說明如何設定要傳送到事件中心的每一層的資料。 步驟假設您已經有該層要監視的資產。

在開始之前，您需要[建立事件中樞命名空間和事件中心](../event-hubs/event-hubs-create.md)。 此命名空間和事件中心是所有的監視資料的目的地。

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>我該如何串流處理至事件中心設定 Azure 平台監視資料？

Azure 平台監視資料來自兩個主要來源：
1. [Azure 活動記錄檔](./monitoring-overview-activity-logs.md)，其中包含建立、 更新和刪除作業從 [資源管理員] 中的變更[Azure 服務健全狀況](../service-health/service-health-overview.md)，可能會影響您的訂用帳戶中，而中的資源[資源健全狀況](../service-health/resource-health-overview.md)狀態轉換，以及一些其他類型的訂用帳戶層級事件。 [這篇文章說明 Azure 活動記錄檔中所顯示事件的所有分類](./monitoring-activity-log-schema.md)。
2. [Azure Active Directory 報告](../active-directory/active-directory-reporting-azure-portal.md)，其中包含的登入活動和稽核記錄的特定租用戶中所做的變更歷程記錄。 它還不可能串流處理到事件中心的 Azure Active Directory 資料。

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>資料流 Azure 活動記錄檔資料到事件中心

若要將資料從 Azure 活動記錄檔傳送到事件中樞命名空間，您設定您的訂用帳戶上設定記錄檔。 [請遵循本指南](./monitoring-stream-activity-logs-event-hubs.md)設定您的訂用帳戶上的記錄檔設定檔。 一旦您想要監視每個訂閱，請執行這項操作。

> [!TIP]
> 記錄檔的設定檔目前只可讓您選取的事件中樞命名空間中，事件中心會透過在名稱 ' insights-操作的記錄檔。 ' 它還不可以將記錄檔中指定您自己的事件中樞名稱。

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>我該如何串流處理至事件中心設定 Azure 資源的監視資料？

Azure 資源發出兩種類型的監視資料：
1. [資源的診斷記錄檔](./monitoring-overview-of-diagnostic-logs.md)
2. [計量](monitoring-overview-metrics.md)

這兩種類型的資料會傳送到事件中心使用資源的診斷設定。 [請遵循本指南](./monitoring-stream-diagnostic-logs-to-event-hubs.md)設定特定的資源上的資源診斷設定。 設定您想要收集記錄檔的每個資源的資源診斷設定。

> [!TIP]
> 您可以使用 Azure 原則以確保，在特定範圍內的每個資源一律以設定診斷設定[原則規則中使用 DeployIfNotExists 效果](../azure-policy/policy-definition.md#policy-rule)。 現今 DeployIfNotExists 才支援內建的原則。

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>我該如何串流處理至事件中心設定客體 OS 的監視資料？

您要安裝代理程式，以將客體作業系統的監視資料傳送到事件中心。 適用於 Windows 或 Linux，您可以指定您想要傳送到事件中心，以及應該在組態檔中的資料並將該組態檔傳遞至 VM 上執行的代理程式的事件中樞的資料。

### <a name="stream-linux-data-to-an-event-hub"></a>資料流到事件中心的 Linux 資料

[Linux Azure 診斷代理程式](../virtual-machines/linux/diagnostic-extension.md)可以用來傳送監視資料到事件中心 Linux 機器。 這麼做將事件中心新增為您年輕人中接收 JSON 的組態檔的保護設定。 [請參閱這篇文章以深入了解加入到 Linux Azure 診斷代理程式的事件中樞接收器](../virtual-machines/linux/diagnostic-extension.md#protected-settings)。

> [!NOTE]
> 您無法設定客體 OS 監視資料到事件中心在入口網站中的資料流。 相反地，您必須手動編輯組態檔。

### <a name="stream-windows-data-to-an-event-hub"></a>資料串流處理 Windows 到事件中心

[Windows Azure 診斷的代理程式](./azure-diagnostics.md)可以用來傳送監視資料從 Windows 電腦到事件中心。 這麼做為接收您 privateConfig 區段，WAD 組態檔中新增事件中樞。 [請參閱這篇文章以深入了解加入到 Windows Azure 診斷代理程式的事件中樞接收器](./azure-diagnostics-streaming-event-hubs.md)。

> [!NOTE]
> 您無法設定客體 OS 監視資料到事件中心在入口網站中的資料流。 相反地，您必須手動編輯組態檔。

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>如何設定應用程式監視資料串流處理至事件中心？

監視資料的應用程式需要您的程式碼已檢測的 sdk，所以沒有一般用途的解決方案，來監視資料到事件中心在 Azure 中的路由應用程式。 不過， [Azure Application Insights](../application-insights/app-insights-overview.md)可用來收集 Azure 應用程式層級資料的一個服務。 如果您使用 Application Insights，您可以透過下列方式來串流處理監視資料到事件中心：

1. [設定連續匯出](../application-insights/app-insights-export-telemetry.md)儲存體帳戶的 Application Insights 資料。

2. 設定計時器觸發邏輯應用程式的[提取資料，從 blob 儲存體](../connectors/connectors-create-api-azureblobstorage.md#use-an-action)和[將其做為訊息發送到事件中心](../connectors/connectors-create-api-azure-event-hubs.md#send-events-to-your-event-hub-from-your-logic-app)。

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>我可以監視資料傳送到我的事件中心用來做什麼？

路由至事件中心的 Azure 監視的監視資料，可讓您輕鬆地與合作夥伴 SIEM 及監視工具整合。 大部分的工具需要的事件中樞連接字串和特定權限給您 Azure 訂用帳戶，從事件中心讀取資料。 工具與 Azure 監視器整合的非完整清單如下：

* **IBM QRadar** -建立事件中樞的 QRadar 連接器目前處於 beta。 它即將提供從 IBM 修正中央或自動透過 QRadar 的自動更新程序手動安裝。
* **Splunk** - [Splunk 的 Azure 監視附加元件](https://splunkbase.splunk.com/app/3534/)位於 Splunkbase 和開放原始碼專案。 [文件與您見面](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)。
* **SumoLogic** -設定 SumoLogic，從事件中心取用資料的指示是[這裡](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)

## <a name="next-steps"></a>後續步驟
* [將活動記錄檔封存至儲存體帳戶](monitoring-archive-activity-log.md)
* [閱讀 Azure 活動記錄檔的概觀](monitoring-overview-activity-logs.md)
* [根據活動記錄檔事件設定警示](insights-auditlog-to-webhook-email.md)

