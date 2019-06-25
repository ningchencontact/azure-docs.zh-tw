---
title: 收集並分析 Azure 活動記錄在 Log Analytics 工作區 |Microsoft Docs
description: 收集 Azure 活動記錄檔，在 Azure 監視器記錄檔，並用以分析和搜尋所有您 Azure 訂用帳戶的 Azure 活動記錄檔中的監視解決方案。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 5839fd40a128097e400f13acbe4fb6ef90c656b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248125"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>收集並分析 Azure 活動記錄中 Azure 監視器中的 Log Analytics 工作區
[Azure 活動記錄檔](activity-logs-overview.md)可深入了解您的 Azure 訂用帳戶中發生的訂用帳戶層級事件。 本文說明如何收集到 Log Analytics 工作區的 活動記錄檔以及如何使用活動 Log Analytics[監視解決方案](../insights/solutions.md)，文中提供的記錄檔查詢和檢視來分析此資料。 

連接到 Log Analytics 工作區的 活動記錄檔提供下列優點：

- 將合併到一個位置中進行分析多個 Azure 訂用帳戶的活動記錄檔。
- 儲存時間超過 90 天的活動記錄檔項目。
- 相互關聯與 Azure 監視器所收集的其他監視資料的活動記錄檔資料。
- 使用[記錄查詢](../log-query/log-query-overview.md)來執行複雜的分析，並在 活動記錄項目上的深入解析。

## <a name="connect-to-log-analytics-workspace"></a>連線到 Log Analytics 工作區
活動記錄檔可以連線到只有一個工作區中，但單一工作區可以連線到相同的 Azure 租用戶中的多個訂用帳戶的活動記錄。 針對跨多個租用戶的集合，請參閱[收集 Azure 活動記錄到 Log Analytics 工作區跨訂用帳戶中不同的 Azure Active Directory 租用戶](activity-log-collect-tenants.md)。

您可以使用下列程序來連接到您的 Log Analytics 工作區的 活動記錄檔：

1. 從**Log Analytics 工作區**功能表在 Azure 入口網站中，選取要收集活動記錄檔的工作區。
1. 在 **工作區資料來源**區段的工作區功能表上，選取**Azure 活動記錄檔**。
1. 按一下您想要連線的訂用帳戶。

    ![工作區](media/activity-log-export/workspaces.png)

1. 按一下  **Connect**連接到選取的工作區的 訂用帳戶中的活動記錄檔。 如果訂用帳戶已連線到另一個工作區中，按一下**中斷連線**首先要中斷其連線。

    ![連線的工作區](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>在 Log Analytics 工作區中進行分析
當您連接活動記錄到 Log Analytics 工作區時，項目會寫入至工作區資料表，稱為**AzureActivity** ，您可以使用擷取[記錄查詢](../log-query/log-query-overview.md)。 此資料表的結構有所不同[類別的記錄項目](activity-logs-overview.md#categories-in-the-activity-log)。 請參閱[Azure 活動記錄事件結構描述](activity-log-schema.md)如需每個類別目錄的描述。

## <a name="activity-logs-analytics-monitoring-solution"></a>活動記錄檔分析監視解決方案
Azure Log Analytics 監視解決方案包含多個記錄檔查詢和檢視來分析您的 Log Analytics 工作區中的活動記錄檔記錄。

### <a name="install-the-solution"></a>安裝解決方案
使用中的程序[安裝的監控解決方案](../insights/solutions.md#install-a-monitoring-solution)若要安裝**Activity Log Analytics**解決方案。 沒有需要其他設定。

### <a name="use-the-solution"></a>使用解決方案
從存取監視解決方案**監視器**在 Azure 入口網站中的功能表。 選取 **更多**中**Insights**一節，以開啟**概觀**與解決方案的圖格的頁面。 **Azure 活動記錄**圖格會顯示的數目計數**AzureActivity**工作區中的記錄。

![Azure 活動記錄圖格](media/collect-activity-logs/azure-activity-logs-tile.png)


按一下  **Azure 活動記錄**圖格以開啟**Azure 活動記錄**檢視。 檢視包含下表中的視覺效果組件。 每個部分會列出最多 10 個比對指定的時間範圍內的該部分準則的項目。 您可以執行記錄檔查詢傳回所有符合的記錄，依序按一下**查看所有**底部的組件。

![Azure 活動記錄儀表板](media/collect-activity-logs/activity-log-dash.png)

| 視覺效果組件 | 描述 |
| --- | --- |
| Azure 活動記錄項目 | 顯示橫條圖的最上層的 Azure 活動記錄檔項目記錄的總計，您已選取的日期範圍，並顯示前 10 個活動呼叫端的清單。 按一下長條圖即可執行 `AzureActivity` 的記錄搜尋。 按一下 呼叫端項目執行記錄搜尋，傳回該項目的所有活動記錄檔項目。 |
| 依狀態列出的活動記錄 | 顯示在選定的日期範圍和清單的前 10 筆狀態記錄的 Azure 活動記錄檔狀態的環圈圖。 按一下圖表可執行的記錄查詢`AzureActivity | summarize AggregatedValue = count() by ActivityStatus`。 按一下狀態項目執行記錄搜尋，傳回該狀態記錄的所有活動記錄檔項目。 |
| 依資源列出的活動記錄 | 顯示活動記錄的資源總數，並列出前十個資源記錄會計算每個資源。 按一下總計區域即可執行 `AzureActivity | summarize AggregatedValue = count() by Resource` 的記錄搜尋，這會顯示解決方案可用的所有 Azure 資源。 按一下資源來執行記錄檔查詢傳回該資源的所有活動記錄。 |
| 依資源提供者列出的活動記錄 | 顯示產生活動記錄檔的資源提供者總數，並列出前 10 個。 按一下總計區域即可執行的記錄查詢`AzureActivity | summarize AggregatedValue = count() by ResourceProvider`，其中顯示所有的 Azure 資源提供者。 按一下 [資源提供者來執行記錄查詢，傳回提供者的所有活動記錄]。 |

## <a name="next-steps"></a>後續步驟

- 深入了解[活動記錄](activity-logs-overview.md)。
- 深入了解[Azure 監視器的資料平台](data-platform.md)。
- 使用[記錄查詢](../log-query/log-query-overview.md)檢視從活動記錄檔的詳細的資訊。
