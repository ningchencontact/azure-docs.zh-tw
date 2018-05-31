---
title: 將警示從 OMS 入口網站擴充 (複製) 到 Azure - 概觀 | Microsoft Docs
description: 將警示從 OMS 入口網站複製到 Azure 警示的程序概觀，詳細說明常見的客戶考量。
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: vinagara
ms.openlocfilehash: 25dcbad8607a651a7dd4b79f4f418cc473a2bf0e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166487"
---
# <a name="extend-copy-alerts-from-oms-portal-into-azure"></a>將警示從 OMS 入口網站擴充 (複製) 到 Azure
Operations Management Suite (OMS) 入口網站只會顯示 Log Analytics 警示。  全新警示體驗現已整合 Microsoft Azure 中各種服務與組件間的警示體驗。 Azure 入口網站中的 Azure 監視器下提供的 [警示] 新體驗，包含 Log Analytics 和 Application Insights 的活動記錄警示、計量警示和記錄警示。 


但部分使用者是透過 [Microsoft Operation Management Suite (OMS) 入口網站](../operations-management-suite/operations-management-suite-overview.md)在使用 Log Analytics 及同盟功能 (如警示)。 因此，為了讓他們在使用 Log Analytics 時也能夠輕鬆管理其他 Azure 資源，Microsoft 已有系統地確保 Azure 入口網站中也會提供 OMS 入口網站的功能。 在這個考量下，Azure 警示已讓使用者能夠管理 Log Analytics 的查詢式警示，如需詳細資訊，請參閱 [Azure 警示上的記錄警示](monitor-alerts-unified-log.md)。 在 Azure 監視器下的 [警示] 中，在 OMS 入口網站中建立的警示已列在適當的 Log Analytics 工作區下。 但是對 OMS 入口網站中建立的這類警示進行任何編輯或變更時，使用者必須離開 Azure 並使用 OMS 入口網站；然後，當他們需要管理任何其他服務時，再回到 Azure。 為了減少此繁複程序，Microsoft 現在已可讓使用者將其警示從 OMS 入口網站擴充至 Azure。

## <a name="benefits-of-extending-your-alerts"></a>擴充您警示的好處
除了不必瀏覽至 Azure 入口網站外部的好處以外，將警示從 OMS 入口網站擴充到 Azure 還有其他顯著的好處

- 不同於在 OMS 入口網站中只能建立及檢視 250 個警示，在 Azure 警示中並沒有這項限制
- 在 Azure 警示中，您可以管理、列舉以及檢視所有警示類型；而不是像使用 OMS 入口網站時只能處理 Log Analytics 警示
- 使用 [Azure 監視器角色](monitoring-roles-permissions-security.md)，將使用者的存取權控制於僅限監視和警示
- Azure 警示會利用[動作群組](monitoring-action-groups.md)，讓您能夠對每個警示執行多個動作，包括 SMS、語音通話、自動化 Runbook、Webhook、ITSM 連接器等等。 

## <a name="process-of-extending-your-alerts"></a>擴充警示的程序
將警示從 OMS 入口網站擴充至 Azure 的程序「不」包含以任何方式變更您的警示定義、查詢或設定。 唯一需要的變更是，Azure 中的所有動作 (例如電子郵件通知、Webhook 呼叫、執行自動化 Runbook 或連線至 ITSM 工具) 都是透過「動作群組」完成。 因此，如果適當的動作群組與您的警示相關聯，它們就會擴充至 Azure。

由於擴充程序不具破壞性，且不會造成作業中斷，Microsoft 從 **2018 年 5 月 14 日**開始，會將在 OMS 入口網站中建立的警示自動擴充至 Azure 警示。 這天起，Microsoft 會開始針對將警示擴充至 Azure 的作業進行排程，並逐漸讓所有警示出現在 OMS 入口網站中，且也可從 Azure 入口網站進行管理。 

> [!NOTE]
> 自 2018 年 5 月 14 日起 - Microsoft 將會啟動將警示自動擴充到 Azure 的程序。 並非所有工作區和警示都會在這一天進行擴充；而是，Microsoft 會在之後幾週內開始分批進行自動擴充警示。 因此，您在 OMS 入口網站中的警示將不會在 2018 年 5 月 14 日立即自動擴充至自 Azure，而且在此期間，使用者仍可[手動擴充自己的警示](monitoring-alerts-extend-tool.md)。

當 Log Analytics 工作區中的警示要根據排程擴充至 Azure 時，這些警示會繼續運作，且「不會」以任何方式妨礙您的監視。 當排程時，您的警示可能暫時無法進行修改/編輯；但可以在這個短暫的時間內繼續建立新的 Azure 警示。 在這個短暫的時間內，如果從 OMS 入口網站進行了任何警示編輯或建立，使用者將可以選擇要在 Azure Log Analytics 還是 Azure 警示中繼續進行。

 ![在排程的期間，系統會將警示上的使用者動作重新導向至 Azure](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> 如果您遵循 [Azure 監視器定價原則](https://azure.microsoft.com/pricing/details/monitor/)中所述的限制及條件，則將警示從 OMS 入口網站擴充至 Azure 時並不會收費，而且用於查詢式 Log Analytics 警示的 Azure 警示也不會計費  

在這個日期前，使用者只要主動選擇在 Azure 中管理自己的警示，即可享受擴充警示的好處。

### <a name="how-to-voluntarily-extending-your-alerts"></a>如何主動擴充您的警示
為了讓 OMS 使用者輕鬆地移入 Azure 警示，Microsoft 已建立可擴充警示的工具。 Microsoft OMS 入口網站的客戶可以從 OMS 入口網站中的精靈，或是使用新的 API 以程式設計方式將其警示擴充至 Azure。 如需詳細資訊，請參閱[使用 OMS 入口網站和 API 將警示擴充至 Azure](monitoring-alerts-extend-tool.md)。


## <a name="usage-after-extending-your-alerts"></a>擴充警示之後的使用
如先前所述，Microsoft Operation Management Suite 中建立的警示將會擴充至 Azure 警示；在這之後，您就可以從 Azure 進行管理。 警示會繼續列在「OMS 入口網站 - 警示設定」區段中。 如下圖所示：

 ![擴充至 Azure 之後列出警示的 OMS 入口網站](./media/monitor-alerts-extend/PostExtendList.png)

對於在 OMS 入口網站中於警示上進行的任何作業 (例如編輯或建立)，系統會以透明的方式將使用者導向至 Azure 警示。 

> [!NOTE]
> 對於在 OMS 中於警示上進行的任何新增或編輯動作，系統將會以透明的方式將使用者帶往 Azure - 確保使用者正確地對應[使用 Azure 監視器和警示的適當權限](monitoring-roles-permissions-security.md)

警示會從現有的 [Log Analytics API](../log-analytics/log-analytics-api-alerts.md) 和 [Log Analytics 資源範本](../monitoring/monitoring-solutions-resources-searches-alerts.md)繼續進行先前的建立作業，僅有些微變更，也就是在將警示擴充至 Azure 之後，必須在排程中讓動作群組產生關聯。

## <a name="next-steps"></a>後續步驟

* 了解[將警示從 OMS 初始化擴充至 Azure](monitoring-alerts-extend-tool.md)的工具
* 深入了解新的 [Azure 警示體驗](monitoring-overview-unified-alerts.md)。
* 了解 [Azure 警示中的記錄警示](monitor-alerts-unified-log.md)。
