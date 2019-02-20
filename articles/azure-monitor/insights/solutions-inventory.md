---
title: Azure 中管理解決方案的資料收集詳細資料 | Microsoft Docs
description: Azure 中的管理解決方案是邏輯、視覺效果和資料擷取規則的集合，可提供針對特定問題領域進行計量的樞紐分析。  本文提供 Microsoft 所提供的管理解決方案清單，以及其資料收集方法和頻率的相關詳細資料。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: aac58cc0887c566c7377edf08f5a86e2d12cdf28
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993226"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Azure 中管理解決方案的資料收集詳細資料
本文包含 Microsoft 所提供的[管理解決方案清單](solutions.md)，以及其詳細文件的連結。  此外，還提供資料收集到 Azure 監視器中的方法和頻率相關資訊。  您可以使用本文中的資訊來識別不同的可用解決方案，以及了解不同管理解決方案的資料流程和連線需求。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-management-solutions"></a>管理解決方案清單

下表列出 Microsoft 在 Azure 中提供的[管理解決方案](solutions.md)。 欄中的項目表示解決方案會使用該方法將資料收集到 Azure 監視器中。  如果解決方案未選取任何欄，則它會從另一個 Azure 服務直接寫入至 Azure 監視器。 請遵循各自的連結來連到其詳細的文件，以取得詳細資訊。

各欄的說明如下所示：

- **Microsoft Monitoring Agent** - Windows 和 Linux 上的代理程式，可用於從 SCOM 執行管理組件以及從 Azure 執行管理解決方案。 在此組態中，代理程式會直接連線到 Azure 監視器，而不需連線至 Operations Manager 管理群組。 
- **Operations Manager** - 與 Microsoft Monitoring Agent 相同的代理程式。 在此組態中，它會[連線到 Operations Manager 管理群組](../../azure-monitor/platform/om-agents.md)，而該管理群組已連線到 Azure 監視器。 
-  **Azure 儲存體** - 解決方案會從 Azure 儲存體帳戶收集資料。 
- **是否需要 Operations Manager？** - 管理解決方案需要已連線的 Operations Manager 管理群組才能收集資料。 
- **透過管理群組傳送的 Operations Manager 代理程式資料** - 如果代理程式[已連線到 SCOM 管理群組](../../azure-monitor/platform/om-agents.md)，則會從管理伺服器將資料傳送到 Azure 監視器。 在此情況下，代理程式不需要直接連線到 Azure 監視器。 如果未選取此方塊，即使代理程式已連線到 SCOM 管理群組，也會從代理程式直接將資料傳送到 Azure 監視器。 它必須能夠透過 [Azure 監視器閘道](../../azure-monitor/platform/gateway.md)與 Log Analytics 通訊。
- **收集頻率** - 指定管理解決方案收集資料的頻率。 



| **管理解決方案** | **平台** | **Microsoft Monitoring Agent** | **Operations Manager 代理程式** | **Azure 儲存體** | **是否需要 Operations Manager？** | **透過管理群組傳送的 Operations Manager 代理程式資料** | **收集頻率** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [活動記錄分析](../../azure-monitor/platform/collect-activity-logs.md) | Azure | | | | | | 與通知同時 |
| [AD 評估](../../azure-monitor/insights/ad-assessment.md) | Windows |&#8226; |&#8226; | | |&#8226; |7 天 |
| [AD 複寫狀態](../../azure-monitor/insights/ad-replication-status.md) | Windows |&#8226; |&#8226; | | |&#8226; |5 天 |
| [代理程式健全狀況](solution-agenthealth.md) | Windows 和 Linux | &#8226; | &#8226; | | | &#8226; | 1 分鐘 |
| [警示管理](../../azure-monitor/platform/alert-management-solution.md) (Nagios) | Linux |&#8226; | | | | |與抵達同時 |
| [警示管理](../../azure-monitor/platform/alert-management-solution.md) (Zabbix) | Linux |&#8226; | | | | |1 分鐘 |
| [警示管理](../../azure-monitor/platform/alert-management-solution.md) (Operations Manager) | Windows | |&#8226; | |&#8226; |&#8226; |3 分鐘 |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/a |
| [Application Insights Connector (已淘汰)](../../azure-monitor/platform/app-insights-connector.md) | Azure | | | |  |  | 與通知同時 |
| [自動化混合式背景工作角色](../../automation/automation-hybrid-runbook-worker.md) |  Windows | &#8226; | &#8226; |  |  |  | n/a |
| [Azure 應用程式閘道分析](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | 與通知同時 |
| **管理解決方案** | **平台** | **Microsoft Monitoring Agent** | **Operations Manager 代理程式** | **Azure 儲存體** | **是否需要 Operations Manager？** | **透過管理群組傳送的 Operations Manager 代理程式資料** | **收集頻率** |
| [Azure 網路安全性群組分析 (已淘汰)](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | 與通知同時 |
| [Azure SQL Analytics (預覽)](../../azure-monitor/insights/azure-sql.md) |  Windows | | | | | | 1 分鐘 |
| [備份](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | 與通知同時 |
| [容量與效能 (預覽)](../../azure-monitor/insights/capacity-performance.md) | Windows |&#8226; |&#8226; | | |&#8226; |與抵達同時 |
| [變更追蹤](../../automation/automation-change-tracking.md) | Windows |&#8226; |&#8226; | | |&#8226; |每小時 |
| [變更追蹤](../../automation/automation-change-tracking.md) | Linux |&#8226; | | | | |每小時 |
| [容器](../../azure-monitor/insights/containers.md) | Windows 和 Linux | &#8226; | &#8226; |  |  |  | 3 分鐘 |
| [金鑰保存庫分析](../../azure-monitor/insights/azure-key-vault.md) | Windows | | | | | |與通知同時 |
| [惡意程式碼評估](../../security-center/security-center-install-endpoint-protection.md) | Windows |&#8226; |&#8226; | | |&#8226; |每小時 |
| [網路效能監視器](../../azure-monitor/insights/network-performance-monitor.md) |  Windows | &#8226; | &#8226; |  |  |  | TCP 會每 5 秒交握一次，而資料會每 3 分鐘傳送一次 |
| [Office 365 分析 (預覽)](solution-office-365.md) | Windows | | | | | |與通知同時 |
| **管理解決方案** | **平台** | **Microsoft Monitoring Agent** | **Operations Manager 代理程式** | **Azure 儲存體** | **是否需要 Operations Manager？** | **透過管理群組傳送的 Operations Manager 代理程式資料** | **收集頻率** |
| [Service Fabric 分析](../../service-fabric/service-fabric-diagnostics-oms-setup.md) | Windows | | |&#8226; | | |5 分鐘 |
| [服務對應](../../azure-monitor/insights/service-map.md) | Windows 和 Linux | &#8226; | &#8226; |  |  |  | 15 秒 |
| [SQL 評估](../../azure-monitor/insights/sql-assessment.md) | Windows |&#8226; |&#8226; | | |&#8226; |7 天 |
| [SurfaceHub](../../azure-monitor/insights/surface-hubs.md) | Windows |&#8226; | | | | |與抵達同時 |
| [System Center Operations Manager 評定 (預覽)](../../azure-monitor/insights/scom-assessment.md) |  Windows | &#8226; | &#8226; |  |  | &#8226; | 7 天 |
| [更新管理](../../automation/automation-update-management.md) |  Windows |&#8226; |&#8226; | | |&#8226; |安裝更新之後 15 分鐘，至少一天 2 次 |
| [升級整備程度](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) |  Windows | &#8226; |  |  |  |  | 2 天 |
| [VMware 監控 (已淘汰)](../../azure-monitor/insights/vmware.md) |  Linux | &#8226; |  |  |  |  | 3 分鐘 |
| [Wire Data 2.0 (預覽)](../../azure-monitor/insights/wire-data.md) |Windows (2012 R2/8.1 或更新版本) |&#8226; |&#8226; | | | | 1 分鐘 |




## <a name="next-steps"></a>後續步驟
* 了解如何[建立查詢](../../azure-monitor/log-query/log-query-overview.md)，以分析管理解決方案所收集的資料。
