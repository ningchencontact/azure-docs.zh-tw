---
title: OMS 入口網站移至 Azure | Microsoft 文件
description: OMS 入口網站目前終止服務，連同其所有功能將移至 Azure 入口網站。 本文將提供此次轉換的詳細資料。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 1a8ccc818cafac4867cb533c83f297af61a21836
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39309097"
---
# <a name="oms-portal-moving-to-azure"></a>OMS 入口網站移至 Azure
我們不斷收到 Log Analytics 客戶的意見反應，要求以單一使用者體驗來監控和管理內部部署和 Azure 的工作負載。 Azure 入口網站為所有 Azure 服務的中樞，提供豐富的管理體驗，例如建立資源關聯的儀表板、 尋找資源的智慧型搜尋，以及資源管理的標記等功能。 為了整合及簡化監控和管理工作流程，我們已陸續將 OMS 入口網站的功能加入 Azure 入口網站。 我們很高興在此宣佈，現在 OMS 入口網站的功能大多已納入 Azure 入口網站。 事實上，流量分析等一些新功能僅能在 Azure 入口網站中使用。 目前只剩下一些差距，還有一些解決方案仍在移至 Azure 入口網站的程序中。 如果您並未使用這些功能，那麼，您將能夠在 Azure 入口網站中完成您在 OMS 入口網站中所執行的動作以及其他更多項目。 如果您尚未這麼做，我們建議您立即開始使用 Azure 入口網站 ！ 

我們預計在 2018 年 8 月前解決這兩個入口網站之間的差距。 我們會根據客戶的意見反應，來溝通終止 OMS 入口網站的時間表。 我們很高興移至 Azure 入口網站，並預期此次轉換將會很簡單。 但我們了解任何變更均有其難度，而且可能會對您造成困擾。 如有任何問題、意見反應或考量，請傳送至 **LAUpgradeFeedback@microsoft.com**。 本文其餘部分將介紹關鍵案例、 目前的差距以及此次轉換的藍圖。 

## <a name="progress"></a>Progress
以下是從本文前幾版以來所完成的更新。

### <a name="july-27"></a>7 月 27 日

- [DNS 分析](log-analytics-dns.md)現在已可在 Azure 入口網站中完整運作。
- [更新管理](../automation/automation-update-management.md)已更新為可在 Azure 入口網站中完整運作。 如需詳細資料，請參閱[將您的 OMS 更新部署遷移至 Azure](../automation/migrate-oms-update-deployments.md)。
- [警示](#changes-to-alerts)功能現在已完全擴充至 Azure 入口網站。
- [自訂記錄預覽功能](log-analytics-data-sources-custom-logs.md)現在會針對所有工作區自動啟用。

## <a name="what-will-change"></a>會變更哪些功能？ 
下列變更會隨著 OMS 入口網站的淘汰一併宣布。 並將在下列各節中逐一詳細說明這些變更。

- 您只可以在 Azure 入口網站中建立新的工作區。
- 新的警示管理體驗將會取代 「 警示管理 」 解決方案。
- 在 Azure 入口網站中將使用 Azure 角色型存取控制來進行使用者存取管理。
- 您將不再需要 Application Insights Connector，因為可以啟用透過跨工作區的查詢來啟用相同的功能。
- OMS 行動裝置應用程式將會被淘汰。 
- NSG 解決方案將由透過流量分析解決方案提供的增強功能所取代。
- System Center Operations Manager 與 Log Analytics 的新連線需要更新的管理組件。


## <a name="current-known-gaps"></a>目前已知差距 
由於目前仍有一些功能差距，您需要繼續使用 OMS 入口網站。 我們正努力縮減落差，並會隨進度而更新此文件。 如需瞭解有關擴充功能和變更的持續公告，請一併參閱 [Azure 更新](https://azure.microsoft.com/updates/?product=log-analytics)。

- 下列解決方案還無法在 Azure 入口網站中完全正常運作。 您應該在傳統入口網站中繼續使用這些解決方案，直到更新為止。

    - Windows 分析解決方案 ([更新整備小幫手](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started)、[裝置健康情況](https://docs.microsoft.com/windows/deployment/update/device-health-monitor)，和[更新合規性](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started))
    - [Surface Hub](log-analytics-surface-hubs.md)

-  若要存取 Azure 中的 Log Analytics 資源，使用者必須透過 [Azure 角色型存取](#user-access-and-role-migration)來取得存取權限。


## <a name="what-should-i-do-now"></a>我現在該怎麼辦？  
如需有關如何轉換到 Azure 入口網站的資訊，請參閱 [Log Analytics 使用者從 OMS 入口網站轉換至 Azure 入口網站的常見問題](../log-analytics/log-analytics-oms-portal-faq.md)。 如果[上述的差距](#current-known-gaps)不適用於您的環境，那麼您應該考慮開始使用 Azure 入口網站作為您的主要體驗。 如有任何意見反應、問題或疑慮，請傳送至LAUpgradeFeedback@microsoft.com。

## <a name="new-workspaces"></a>新工作區
自 7 月 29 日起，您將無法再使用 OMS 入口網站來建立新的工作區。 若要在 Azure 入口網站中建立新工作區，請遵循[在 Azure 入口網站中建立 Log Analytics 工作區](log-analytics-quick-create-workspace.md)的指引。

## <a name="changes-to-alerts"></a>警示的變更 

### <a name="alert-extension"></a>警示擴充功能  

> [!NOTE]
> 警示功能現在已完全擴充至 Azure 入口網站。 您可以在 OMS 入口網站中檢視現有的警示規則，但只能在 Azure 入口網站中管理這些警示規則。

警示正在[延伸至 Azure 入口網站](../monitoring-and-diagnostics/monitoring-alerts-extend.md)的程序中。 一旦完成後，警示的管理動作將只能在 Azure 入口網站中使用。 現有警示會繼續列在 OMS 入口網站中。 若您藉由使用 Log Analytics 警示 REST API 或 Log Analytics 警示資源範本以程式設計的方式存取警示，則必須使用動作群組，而非您 API 呼叫、Azure Resource Manager 範本及 PowerShell 命令中的動作。

### <a name="alert-management-solution"></a>警示管理解決方案
您可以使用 [Azure 監視器的統一警示介面](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)，而不用[警示管理解決方案](log-analytics-solution-alert-management.md)，即可以視覺化方式檢視和管理您的通知。 這個新體驗會從 Azure 內多個來源彙總警示，包括 Log Analytics 的記錄檔警示。 您可以看到警示的分佈、透過智慧型群組 充分利用對相關警示的自動化分組功能，以及套用豐富的篩選器同時檢視多個訂閱的警示。 所有這些功能自 2018 年 6 月 4 日起均可提供預覽。 警示管理解決方案無法在 Azure 入口網站使用。 

只要為工作區安裝解決方案，警示管理解決方案  (具有警報類型的記錄) 所收集的資料就會繼續存在於 Log Analytics 中。 自 2018 年 8 月起，將啟用從統一警示進入工作區的警示串流，取代此項功能。 預期有部分結構描述將會變更，並將在日後公告。

## <a name="user-access-and-role-migration"></a>使用者存取權和角色的移轉
Azure 入口網站存取管理功能比在 OMS 入口網站中的存取管理功能更為豐富而強大，但需要某些轉換。 請參閱[管理工作區](log-analytics-manage-access.md#manage-accounts-and-users)，以瞭解 Log Analytics 中存取管理的詳細資料。

自 7 月 30 日起，會開始將 OMS 入口網站的存取控制權限自動轉換為 Azure 入口網站權限的作業。 完成轉換後，OMS 入口網站使用者管理區段會將使用者路由傳送至 Azure 中的存取控制 (IAM)。 

在轉換期間，系統會檢查 OMS 入口網站中具有權限的每位使用者或安全性群組，並判定其是否在 Azure 中具有相同的層級或權限。 如果缺少權限，將會為相關的工作區和解決方案指派下列角色。

| OMS 入口網站權限 | Azure 角色 |
|:---|:---|
| 唯讀 | Log Analytics 讀者 |
| 參與者 | Log Analytics 參與者 |
| 系統管理員 | 擁有者 |

為了確保沒有將過多的權限指派給使用者，系統將不會在資源群組層級中自動指派這些權限。 如此一來，工作區的管理員必須在資源群組或訂閱層級中手動指派_擁有者_或_參與者_的角色給自己，來執行下列動作。

- 新增或移除解決方案
- 定義新的自訂檢視
- 管理警示 

在某些情況下，自動轉換無法套用權限，將會提示管理員手動指派權限。

## <a name="oms-mobile-app"></a>OMS 行動裝置應用程式
OMS 行動裝置應用程式將會連同 OMS 入口網站一併終止。 您不必使用 OMS 行動裝置應用程式來存取有關 IT 基礎結構、 儀表板和已儲存查詢的資訊，而可以直接從您的行動裝置瀏覽器中存取 Azure 入口網站。 若要收到警示，請設定 [Azure 動作群組](../monitoring-and-diagnostics/monitoring-action-groups.md)，即可以簡訊 或語音電話的形式接收通知

## <a name="application-insights-connector-and-solution"></a>Application Insights Connector 與解決方案
[Application Insights Connector](log-analytics-app-insights-connector.md) 可用來將 Application Insights 資料移入 Log Analytics 工作區。 若要啟用整個基礎結構和應用程式資料的可見度，則需要複製此資料。

有了[跨資源查詢](log-analytics-cross-workspace-search.md)的支援，就不再需要複製資料。 因此，將取代現有的 Application Insights 解決方案。 從 7 月開始，您將無法從新的 Application Insights 資源連結到 Log Analytics 工作區。 現有的連結和儀表板將會繼續運作到 2018 年 11 月為止。


## <a name="azure-network-security-group-analytics"></a>Azure 網路安全性群組分析
[Azure 網路安全性群組分析解決方案](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics)將由最近推出的[流量分析](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/)所取代，讓您清楚掌握雲端網路上使用者和應用程式的活動。 流量分析可協助您稽核貴組織的網路活動、保護應用程式和資料、將工作負載效能最佳化並保持符合規範。 

此解決方案可分析 NSG 流程記錄，並提供下列深入見解。

- 在 Azure 與網際網路、公用雲端區域、VNET 以及子網路之間的網路流量。
- 您網路上的應用程式和通訊協定，而不需要 Sniffer 或專用的流量收集設備。
- 發言最多者、通訊流量高的應用程式、雲端中的 VM 對話、流量作用區。
- 跨 VNET 的流量來源和目的地，關鍵業務服務和應用程式之間的相互關係。
- 安全性包括 惡意流量、對網際網路開放的連接埠、嘗試存取網際網路的應用程式或 VM。
- 容量使用率，這可協助您解決過度佈建或使用量過低的問題。

您可以繼續仰賴診斷設定將 NSG 記錄檔傳送到 Log Analytics，如此一來，您現有已儲存的搜尋、 警示、 儀表板將可繼續運作。 在進一步通知之前，已安裝解決方案的客戶可以繼續使用。 自 8 月 15 日起，「網路安全性群組分析」解決方案將從市集中移除，並透過社群以 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights)的方式提供。

## <a name="system-center-operations-manager"></a>System Center Operations Manager
如果您已經[將 Operations Manager 管理群組連線到 Log Analytics](log-analytics-om-agents.md)，則此服務將繼續運作，無須變更。 然而，針對新連線，您必須依照[使用 Microsoft System Center Operations Manager 管理組件設定 Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/) 中的指引。

## <a name="next-steps"></a>後續步驟
- 如需有關從 OMS 入口網站移至 Azure 入口網站的指引，請參閱 [Log Analytics 使用者從 OMS 入口網站轉換至 Azure 入口網站的常見問題](log-analytics-oms-portal-faq.md)。