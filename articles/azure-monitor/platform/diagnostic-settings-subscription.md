---
title: 使用診斷設定收集 Azure 活動記錄檔（預覽）-Azure 監視器 |Microsoft Docs
description: 使用診斷設定，將 Azure 活動記錄轉送至 Azure 監視器記錄、Azure 儲存體或 Azure 事件中樞。
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 10/31/2019
ms.openlocfilehash: 6104a8b01cc9fca5ff8de973e7fc2af77cda8515
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048198"
---
# <a name="collect-azure-activity-log-with-diagnostic-settings-preview"></a>使用診斷設定收集 Azure 活動記錄（預覽）
[Azure 活動記錄](activity-logs-overview.md)是一個[平臺記錄](platform-logs-overview.md)，可讓您深入瞭解 Azure 中發生的訂用帳戶層級事件。 到目前為止，您已建立記錄設定檔，以將活動記錄專案傳送至[事件中樞或儲存體帳戶](activity-log-export.md)，並使用連接器將其收集到[log Analytics 工作區](activity-log-collect.md)。

您現在可以使用用來收集[資源記錄](resource-logs-overview.md)的相同[診斷設定](diagnostic-settings.md)來設定 Azure 活動記錄檔的收集。 使用診斷設定與目前的方法相比有下列優點：

- 收集所有平臺記錄的一致方法。
- 跨多個訂用帳戶和租使用者收集活動記錄。
- 篩選集合，只收集特定類別的記錄。
- 收集所有活動記錄類別。 某些分類不會使用先前的方法收集。
- 記錄內嵌的延遲較快。 先前的方法大約會有15分鐘的延遲，而診斷設定只會加上1分鐘。

## <a name="considerations"></a>注意事項
啟用這項功能之前，請先考慮使用診斷設定的下列活動記錄收集詳細資料。

- 您應該停用現有的活動集合，再使用診斷設定加以啟用。 啟用這兩個可能會導致重複的資料。
- 已移除將活動記錄收集至 Azure 儲存體的保留設定，這表示資料會無限期地儲存，直到您將其移除為止。
- 您可以將多個訂用帳戶的活動記錄傳送至單一 Log Analytics 工作區。 您可以將任何單一訂用帳戶的活動記錄傳送到最多五個 Log Analytics 工作區。

## <a name="configure-diagnostic-settings"></a>設定診斷設定
請使用下列程式在 Azure 入口網站中建立診斷設定，以收集 Azure 活動記錄。 您目前無法使用其他方法來建立訂用帳戶層級的診斷設定。

1. 停用活動記錄的現有 Log Analytics 工作區集合：
   1. 開啟 Azure 入口網站中的  **Log Analytics 工作區** 功能表，然後選取要收集活動記錄的工作區。
   2. 在工作區功能表的 [**工作區資料來源**] 區段中，選取 [ **Azure 活動記錄**]。
   3. 按一下您要中斷連線的訂用帳戶。
   4. 按一下 **[中斷連線]** ，**然後在系統**要求您確認您的選擇時才會出現。
2. 從 Azure 入口網站的 [ **Azure 監視器**] 功能表中，選取 [**活動記錄**]。
3. 按一下 [診斷設定]。
   
   ![診斷設定](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. 按一下舊版體驗的紫色橫幅，並停用儲存體或事件中樞的任何目前集合。 

    ![舊版體驗](media/diagnostic-settings-subscription/legacy-experience.png)

5. 遵循在 Azure 入口網站中[建立診斷設定](diagnostic-settings.md#create-diagnostic-settings-in-azure-portal)中的程式來建立診斷設定。 請參閱[活動記錄中的類別](activity-logs-overview.md#categories-in-the-activity-log)，以取得您可以用來從活動記錄篩選事件的類別的說明。 


## <a name="differences-in-data"></a>資料的差異
診斷設定會收集與先前用來收集活動記錄的方法相同的資料，但目前的差異如下：

下列屬性已移除：

- ActivityStatus
- ActivitySubstatus
- OperationName
- ResourceProvider 

已加入下列屬性：

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-monitoring-solution"></a>活動記錄監視解決方案
Azure Log Analytics 監視解決方案包含多個記錄查詢和視圖，可用於分析 Log Analytics 工作區中的活動記錄檔記錄。 此解決方案會使用 Log Analytics 工作區中收集的記錄資料，如果您使用診斷設定收集活動記錄，則不需要任何變更即可繼續工作。 如需此解決方案的詳細資訊，請參閱[活動記錄分析監視解決方案](activity-log-collect.md#activity-logs-analytics-monitoring-solution)。

## <a name="next-steps"></a>後續步驟

* [深入了解活動記錄](../../azure-resource-manager/resource-group-audit.md)
* [深入瞭解診斷設定](diagnostic-settings.md)
