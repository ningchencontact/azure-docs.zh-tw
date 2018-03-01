---
title: "將 Azure 監視資料串流至事件中樞 | Microsoft Docs"
description: "了解如何將所有的 Azure 監視資料串流至事件中樞，以將資料匯入夥伴 SIEM 或分析工具。"
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
ms.date: 2/13/2018
ms.author: johnkem
ms.openlocfilehash: d449be98cd59756e2bafc584e0501b8c83c594eb
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>將 Azure 監視資料串流至事件中樞以供外部工具取用

Azure 監視器有提供單一管道以存取 Azure 環境的所有監視資料，讓您輕鬆設定夥伴 SIEM 和監視工具來取用該資料。 本文逐步解說設定 Azure 環境的不同資料層，以供傳送至單一事件中樞命名空間或事件中樞，再由外部工具收集。

## <a name="what-data-can-i-send-into-an-event-hub"></a>我可以將哪些資料傳送至事件中樞？ 

Azure 環境內有數個「層級」的監視資料，而存取每一層資料的方法都略有不同。 一般而言，針對各層的描述如下：

- **應用程式監視資料：**您所撰寫並在 Azure 上執行之程式碼的效能和功能相關資料。 應用程式監視資料的範例包括效能追蹤、應用程式記錄，以及使用者遙測。 應用程式監視資料通常會以下列其中一種方式收集：
  - 透過使用 [Application Insights SDK](../application-insights/app-insights-overview.md) 之類的 SDK 來檢測程式碼。
  - 透過執行會在執行應用程式的電腦上接聽新應用程式記錄的監視代理程式，例如 [Windows Azure 診斷代理程式](./azure-diagnostics.md)或 [Linux Azure 診斷代理程式](../virtual-machines/linux/diagnostic-extension.md)。
- **客體 OS 監視資料：**有關執行應用程式之作業系統的資料。 客體 OS 監視資料的範例為 Linux syslog 或 Windows 系統事件。 若要收集這類資料，您必須安裝像是 [Windows Azure 診斷代理程式](./azure-diagnostics.md)或 [Linux Azure 診斷代理程式](../virtual-machines/linux/diagnostic-extension.md) 的代理程式。
- **Azure 資源監視資料：**有關 Azure 資源之作業的資料。 針對某些 Azure 資源類型 (例如虛擬機器)，在該 Azure 服務內會有要監視的客體 OS 與應用程式。 針對其他 Azure 資源 (例如網路安全性群組)，資源監視資料是最高層級的可用資料 (因為在那些資源中沒有執行任何客體 OS 或應用程式)。 這類資料可使用[資源診斷設定](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)來收集。
- **Azure 平台監視資料：**有關 Azure 訂用帳戶或租用戶之作業和管理的資料，以及有關 Azure 本身之健康情況和作業的資料。 [活動記錄](./monitoring-overview-activity-logs.md)包括服務健康情況資料，而 Active Directory 稽核則是平台監視資料的範例。 這類資料也可以使用診斷設定來收集。

來自任何層的資料都能傳送至事件中樞，然後再提取至夥伴工具。 以下幾節說明如何設定來自每一層的資料，以串流至事件中樞。 當中的步驟均假設您在要監視的層級皆已具有資產。

在開始之前，您必須[建立事件中樞命名空間和事件中樞](../event-hubs/event-hubs-create.md)。 此命名空間和事件中樞是所有監視資料的目的地。

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>我要如何設定 Azure 平台監視資料，以將它串流至事件中樞？

Azure 平台監視資料的主要來源有二：
1. [Azure 活動記錄](./monitoring-overview-activity-logs.md)：其中包含來自 Resource Manager 的建立、更新及刪除作業、可能會影響訂用帳戶中資源的 [Azure 服務健康情況](../service-health/service-health-overview.md)變更、[資源健康情況](../service-health/resource-health-overview.md)在狀態上的轉換，以及數種其他類型的訂用帳戶層級事件。 [本文詳細說明 Azure 活動記錄中所出現事件的所有類別](./monitoring-activity-log-schema.md)。
2. [Azure Active Directory 報告](../active-directory/active-directory-reporting-azure-portal.md)：其中包含在特定租用戶內所進行之登入活動的歷程記錄和所做變更的稽核記錄。 目前還不能將 Azure Active Directory 資料串流至事件中樞。

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>將 Azure 活動記錄資料串流至事件中樞

若要將 Azure 活動記錄的資料傳送至事件中樞命名空間，您必須在訂用帳戶上設定記錄設定檔。 [遵循此指南](./monitoring-stream-activity-logs-event-hubs.md)以在訂用帳戶上設定記錄設定檔。 請在想要監視的每個訂用帳戶執行這項操作一次。

> [!TIP]
> 記錄設定檔目前僅允許您選取事件中樞命名空間，其中會以 'insights-operational-logs' 的名稱建立事件中樞。 目前還不能在記錄設定檔中自行指定事件中樞名稱。

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>我要如何設定 Azure 資源監視資料，以將它串流至事件中樞？

Azure 資源會發出兩種監視資料：
1. [資源診斷記錄](./monitoring-overview-of-diagnostic-logs.md)
2. [計量](monitoring-overview-metrics.md)

這兩種資料都會使用資源診斷設定來傳送至事件中樞。 [遵循此指南](./monitoring-stream-diagnostic-logs-to-event-hubs.md)以在特定資源上設定資源診斷設定。 在想要收集記錄的每個資源上設定資源診斷設定。

> [!TIP]
> 您可以使用 Azure 原則以透過[在原則規則中使用 DeployIfNotExists 效果](../azure-policy/policy-definition.md#policy-rule)，來確保特定範圍內的每個資源一律會搭配診斷設定進行設定。 目前僅於內建原則上支援 DeployIfNotExists。

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>我要如何設定客體 OS 監視資料，以將它串流至事件中樞？

您必須安裝代理程式，才能將客體 OS 監視資料傳送至事件中樞。 不論是使用 Windows 或 Linux，您都要在設定檔中指定想傳送至事件中樞的資料，以及要傳送資料的目標事件中樞，並將該設定檔傳遞至 VM 上執行的代理程式。

### <a name="stream-linux-data-to-an-event-hub"></a>將 Linux 資料串流至事件中樞

[Linux Azure 診斷代理程式](../virtual-machines/linux/diagnostic-extension.md)可用來將 Linux 電腦的監視資料傳送至事件中樞。 請在 LAD 設定檔保護的設定 JSON 中，將事件中樞新增為接收來執行此動作。 [請參閱此文章以深入了解將事件中樞接收新增至 Linux Azure 診斷代理程式](../virtual-machines/linux/diagnostic-extension.md#protected-settings)。

> [!NOTE]
> 您無法在入口網站中進行將客體 OS 監視資料串流至事件中樞的設定。 相反地，您必須手動編輯設定檔。

### <a name="stream-windows-data-to-an-event-hub"></a>將 Windows 資料串流至事件中樞

[Windows Azure 診斷代理程式](./azure-diagnostics.md)可用來將 Windows 電腦的監視資料傳送至事件中樞。 請在 WAD 設定檔的 privateConfig 區段中，將事件中樞新增為接收來執行此動作。 [請參閱此文章以深入了解將事件中樞接收新增至 Windows Azure 診斷代理程式](./azure-diagnostics-streaming-event-hubs.md)。

> [!NOTE]
> 您無法在入口網站中進行將客體 OS 監視資料串流至事件中樞的設定。 相反地，您必須手動編輯設定檔。

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>我要如何設定應用程式監視資料，以將它串流至事件中樞？

應用程式監視資料要求您的程式碼需搭配 SDK 進行檢測，因此沒有一般的解決方案可將應用程式監視資料路由至 Azure 中的事件中樞。 不過，[Azure Application Insights](../application-insights/app-insights-overview.md) 是可以用來收集 Azure 應用程式層級資料的其中一個服務。 如果您是使用 Application Insights，便執行下列動作來將監視資料串流至事件中樞：

1. 針對儲存體帳戶[設定連續匯出](../application-insights/app-insights-export-telemetry.md) Application Insights 資料。

2. 設定以計時器觸發的邏輯應用程式，使它能[從 Blob 儲存體提取資料](../connectors/connectors-create-api-azureblobstorage.md#use-an-action)，並[以訊息的形式將它推送至事件中樞](../connectors/connectors-create-api-azure-event-hubs.md#send-events-to-your-event-hub-from-your-logic-app)。

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>傳送至事件中樞的監視資料有何功用？

使用 Azure 監視器將監視資料路由至事件中樞，可讓您輕鬆與夥伴 SIEM 和監視工具整合。 大部分工具皆需要事件中樞連接字串和 Azure 訂用帳戶的特定權限，才能讀取事件中樞的資料。 可與 Azure 監視器整合的部分工具清單如下：

* **IBM QRadar** - Microsoft Azure DSM 與 Microsoft Azure 事件中樞通訊協定均可從 [IBM 支援網站](http://www.ibm.com/support) \(英文\) 下載。 您可以[從這裡深入了解與 Azure 的整合](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0) \(英文\)。
* **Splunk** -根據您的 Splunk 安裝程式而定，共有兩種方法：
    1. [適用於 Splunk 的 Azure 監視器附加元件](https://splunkbase.splunk.com/app/3534/) \(英文\) 可從 Splunkbase 和開放原始碼專案中取得。 [文件在此](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk) \(英文\)。
    2. 如果您無法在 Splunk 執行個體中安裝附加元件 (例如， 如果使用 Proxy 或在 Splunk Cloud 上執行)，您可以使用[由事件中樞中的新訊息觸發的這個功能](https://github.com/sebastus/AzureFunctionForSplunkVS)，將這些事件轉寄給 Splunk HTTP Event Collector。
* **SumoLogic** - 設定 SumoLogic 以從事件中樞取用資料的指示[可從這裡取得](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) \(英文\)

## <a name="next-steps"></a>後續步驟
* [將活動記錄檔封存至儲存體帳戶](monitoring-archive-activity-log.md)
* [閱讀 Azure 活動記錄檔的概觀](monitoring-overview-activity-logs.md)
* [根據活動記錄檔事件設定警示](insights-auditlog-to-webhook-email.md)

