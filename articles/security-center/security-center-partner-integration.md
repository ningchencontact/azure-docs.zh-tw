---
title: 在 Azure 資訊安全中心整合安全性解決方案 | Microsoft Docs
description: 了解 Azure 資訊安全中心如何與夥伴整合，以提高您 Azure 資源的整體安全性。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 435ae9f08f718a9310fd1687fb7859058edf8b45
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384256"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>在 Azure 資訊安全中心整合安全性解決方案
這份文件可協助您管理已連線到 Azure 資訊安全中心的安全性解決方案，並且新增新的項目。

> [!NOTE]
> A subset of security solutions has been retired on July 31st, 2019. For more information and alternative services, see [Retirement of Security Center features (July 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>整合式 Azure 安全性解決方案
資訊安全中心可以使得在 Azure 中啟用整合式安全性解決方案變得簡單。 優點包括：

- **簡化部署**：資訊安全中心提供整合式合作夥伴解決方案的精簡佈建。 For solutions like antimalware and vulnerability assessment, Security Center can provision the agent on your virtual machines. For firewall appliances, Security Center can take care of much of the network configuration required.
- **整合偵測**：來自合作夥伴解決方案的安全性事件會自動收集、彙總以及顯示為資訊安全中心警示和事件的一部分。 這些事件也會與來自其他來源的偵測整合，以提供進階的威脅偵測功能。
- **統一的健全狀況監視與管理**：客戶可以使用整合式的健康情況事件，一眼就能監視所有合作夥伴解決方案。 提供基本管理功能，而且可以讓您輕鬆使用夥伴解決方案存取進階設定。

Currently, integrated security solutions include vulnerability assessment by [Qualys](https://www.qualys.com/public-cloud/#azure) and [Rapid7](https://www.rapid7.com/products/insightvm/) and Microsoft Application Gateway Web application firewall.

> [!NOTE]
> Security Center does not install the Microsoft Monitoring Agent on partner virtual appliances because most security vendors prohibit external agents running on their appliances.
>
>

## <a name="how-security-solutions-are-integrated"></a>安全性解決方案如何整合
從資訊安全中心部署的 Azure 安全性解決方案會自動連線。 You can also connect other security data sources, including computers running on-premises or in other clouds.

![夥伴解決方案整合](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>管理整合式 Azure 安全性解決方案和其他資料來源

1. 登入 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)。

2. 在 [Microsoft Azure] 功能表中，選取 [資訊安全中心]。 [資訊安全中心 - 概觀] 隨即開啟。

3. 在 [資訊安全中心] 功能表下，選取 [安全性解決方案]。

   ![資訊安全中心概觀](./media/security-center-partner-integration/overview.png)

In **Security solutions**, you can see the health of integrated Azure security solutions and run basic management tasks.

### <a name="connected-solutions"></a>連線的解決方案

The **Connected solutions** section includes security solutions that are currently connected to Security Center. It also shows the health status of each solution.  

![連線的解決方案](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

合作夥伴解決方案的狀態可以是︰

* Healthy (green) - no health issues.
* Unhealthy (red) - there's a health issue that requires immediate attention.
* 健康情況問題 (橘色) - 解決方案已停止報告其健康情況。
* Not reported (gray) - the solution hasn't reported anything yet and no health data is available. A solution's status may be unreported if it was connected recently and is still deploying.

> [!NOTE]
> 如果健康情況狀態資料無法使用，資訊安全中心就會顯示最後收到之事件的日期和時間，以指出解決方案是否進行報告。 If no health data is available and no alerts were received within the last 14 days, Security Center indicates that the solution is unhealthy or not reporting.
>
>

1. Select **VIEW** for additional information and options such as:

   - **解決方案主控台**。 開啟這個解決方案的管理體驗。
   - **連結 VM**。 Opens the Link Applications page. 您可以在這裡將資源連接到合作夥伴解決方案。
   - **刪除解決方案**。
   - **設定**。

   ![合作夥伴解決方案詳細資料](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>探索到的解決方案

Security Center automatically discovers security solutions running in Azure but not connected to Security Center and displays the solutions in the **Discovered solutions** section. These  solutions include Azure solutions, like [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), and partner solutions.

> [!NOTE]
> 已探索解決方案功能的訂用帳戶層級需要資訊安全中心的標準層。 See [Pricing](security-center-pricing.md) to learn more about the pricing tiers.
>
>

Select **CONNECT** under a solution to integrate with Security Center and be notified of security alerts.

### <a name="add-data-sources"></a>新增資料來源

[新增資料來源] 區段包含可以連線的其他可用資料來源。 如需從這些來源新增資料的指示，請按一下 [新增]。

![資料來源](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>將資料匯出至 SIEM

You can configure your SIEMs or other monitoring tools to receive Azure Security Center events.

All events from Azure Security Center are published to Azure Monitor's Azure [Activity log](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Azure Monitor uses [a consolidated pipeline](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) to  stream the data to an Event Hub where it can then be pulled into your monitoring tool.

以下幾節說明如何設定資料以串流到事件中樞。 這些步驟假設您已經在 Azure 訂用帳戶中設定 Azure 資訊安全中心。

### <a name="high-level-overview"></a>高階概述

![高階概述](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>公開至 SIEM 的 Azure 安全性資料為何？

In this version, we expose the [security alerts.](../security-center/security-center-managing-and-responding-alerts.md) 在即將發行版本中，我們將使用安全性建議來擴充此資料集。

### <a name="how-to-set-up-the-pipeline"></a>How to set up the pipeline

#### <a name="create-an-event-hub"></a>建立事件中心

Before you begin, [create an Event Hubs namespace](../event-hubs/event-hubs-create.md) - the destination for all your monitoring data.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>將 Azure 活動記錄檔串流至事件中樞

See the following article [stream activity log to Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>安裝合作夥伴 SIEM 連接器 

使用 Azure 監視器將監視資料路由傳送到事件中樞，可讓您輕鬆地與合作夥伴 SIEM 和監視工具整合。

See the following article for the list of [supported SIEMs](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub)

### <a name="example-for-querying-data"></a>查詢資料範例 

Here are some Splunk queries you can use to pull alert data:

| **查詢描述** | **查詢** |
|----|----|
| 所有警示| index=main Microsoft.Security/locations/alerts|
| 依作業名稱對作業計數進行總結| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| 取得警示資訊：時間、名稱、狀態、識別碼和訂用帳戶 | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何在資訊安全中心中整合夥伴解決方案。 如要深入了解資訊安全中心，請參閱下列文章：

* [資訊安全中心的安全性健康情況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [Azure 資訊安全中心常見問題](security-center-faq.md)。 取得有關使用資訊安全中心常見問題的答案。
* [Azure 安全性部落格](https://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 安全性與相容性的部落格文章。
