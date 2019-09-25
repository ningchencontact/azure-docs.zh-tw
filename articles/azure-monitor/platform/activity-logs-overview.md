---
title: Azure 活動記錄檔概觀
description: 認識 Azure 活動記錄檔，並了解如何使用它來了解您的 Azure 訂用帳戶內發生的事件。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ee3a1fef379e2950172dddc389b30e0a363127ae
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262135"
---
# <a name="overview-of-azure-activity-log"></a>Azure 活動記錄的總覽

**Azure 活動記錄**可讓您深入瞭解 azure 中發生的訂用帳戶層級事件。 所涵蓋的資料範圍從 Azure Resource Manager 作業資料到服務健康情況事件的更新。 活動記錄先前稱為「_審核記錄_」或「作業_記錄_」，因為「系統管理」類別會針對您的訂用帳戶報告控制平面事件。 

使用活動記錄來_判斷對訂_用帳戶中的資源所採取的任何寫入作業（PUT、 _POST、DELETE_）的_內容_、物件和時機。 您也可以了解作業的狀態和其他相關屬性。 

活動記錄不包含讀取（GET）作業，或是使用傳統/RDFE 模型之資源的作業。

## <a name="comparison-to-resource-logs"></a>與資源記錄檔的比較
每個 Azure 訂用帳戶都有單一活動記錄。 它會從外部（「控制平面」）提供有關資源之作業的資料。 資源[記錄](resource-logs-overview.md)是由資源發出，並提供該資源作業的相關資訊（「資料平面」）。 您必須為每個資源建立診斷設定，以收集資源記錄。

![與資源記錄相較之下的活動記錄](media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Azure 活動記錄主要是針對 Azure Resource Manager 中發生的活動。 此記錄不會追蹤使用傳統/RDFE 模型的資源。 某些傳統資源類型在 Azure Resource Manager 中有 Proxy 資源提供者 (例如，Microsoft.ClassicCompute)。 如果您透過使用這些 Proxy 資源提供者的 Azure Resource Manager 來與傳統資源類型互動，則作業會顯示在活動記錄。 如果您在 Azure Resource Manager Proxy 之外與傳統資源類型互動，您的動作將只會記錄於「作業記錄」。 可在入口網站的個別區段中，瀏覽作業記錄。

## <a name="activity-log-retention"></a>活動記錄檔保留期
建立之後，系統不會修改或刪除活動記錄專案。 此外，您也無法在介面中或以程式設計方式變更它們。 活動記錄事件會儲存90天。 若要將此資料儲存較長的時間，請[在 Azure 監視器中將其收集](activity-log-collect.md)，或將[它匯出至儲存體或事件中樞](activity-log-export.md)。

## <a name="view-the-activity-log"></a>查看活動記錄
從 Azure 入口網站中的 **監視** 功能表，查看所有資源的活動記錄。 從該資源功能表中的 [**活動記錄**] 選項，查看特定資源的活動記錄檔。 您也可以使用 PowerShell、CLI 或 REST API 來取出活動記錄檔記錄。  請參閱[View and 取出 Azure 活動記錄事件](activity-log-view.md)。

![查看活動記錄](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>收集 Azure 監視器中的活動記錄
將活動記錄收集到 Log Analytics 工作區的 Azure 監視器中，以使用其他監視資料進行分析，並保留超過90天的資料。 請參閱[在 Azure 監視器中收集和分析 Log Analytics 工作區中的 Azure 活動記錄](activity-log-collect.md)。

![查詢活動記錄](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>匯出活動記錄
將活動記錄檔匯出至 Azure 儲存體進行封存，或將它串流至事件中樞，以供協力廠商服務或自訂分析解決方案進行內嵌。 請參閱[匯出 Azure 活動記錄](activity-log-export.md)。 您也可以使用[**Power BI 內容套件**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)，在 Power BI 中分析活動記錄事件。

## <a name="alert-on-activity-log"></a>活動記錄警示
您可以在活動記錄中建立具有[活動記錄警示](activity-log-alerts.md)的特定事件時建立警示。 當您的活動記錄連接到 Log Analytics 工作區時，您也可以使用[記錄查詢](alerts-log-query.md)來建立警示，但記錄查詢警示需要成本。 活動記錄警示不會產生任何費用。

## <a name="categories-in-the-activity-log"></a>活動記錄中的類別
活動記錄中的每個事件都具有下表中所述的特定類別。 如需這些類別結構描述的完整詳細資料，請參閱 [Azure 活動記錄事件結構描述](activity-log-schema.md)。 

| Category | 描述 |
|:---|:---|
| 系統管理 | 包含透過 Resource Manager 執行的所有建立、更新、刪除和動作作業的記錄。 系統管理事件的範例包括 [_建立虛擬機器_] 和 [_刪除網路安全性群組_]。<br><br>使用 Resource Manager 的使用者或應用程式所採取的每個動作，都會在特定資源類型上模型化為作業。 如果作業類型為 [_寫入_]、[_刪除_] 或 [_動作_]，該作業的 [啟動] 和 [成功] 或 [失敗] 記錄都會記錄在 [系統管理] 類別中。 系統管理事件也包括對訂用帳戶中的角色型存取控制所做的任何變更。 |
| 服務健康狀態 | 包含 Azure 中發生之任何服務健康狀態事件的記錄。 _美國東部的 SQL Azure_服務健康狀態事件的範例是發生停機。 <br><br>服務健康狀態事件分為六種：_必要動作_、_協助_復原、_事件_、_維護_、_資訊_或_安全性_。 只有當您的訂用帳戶中有會受到事件影響的資源時，才會建立這些事件。
| 資源健康狀態 | 包含 Azure 資源已發生之任何資源健康狀態事件的記錄。 資源健康狀態事件的一個範例是_虛擬機器健全狀況狀態已變更為 [無法使用_]。<br><br>資源健康狀態事件可以代表四種健全狀況狀態之一：_可用_、_無法使用_、已_降級_和_不明_。 此外，資源健康狀態事件可以分類為已_起始平臺_或_使用者起始_。 |
| 警示 | 包含 Azure 警示的啟用記錄。 警示事件的範例是_myVM 上的 CPU% 在過去5分鐘內已超過 80_。|
| 自動調整 | 包含根據您在訂用帳戶中定義的自動調整規模設定，與自動調整引擎作業相關之任何事件的記錄。 自動調整規模事件的一個範例是 [_自動調整相應增加] 動作失敗_。 |
| 建議 | 包含來自 Azure Advisor 的建議事件。 |
| 安全性 | 包含 Azure 資訊安全中心所產生之任何警示的記錄。 安全性事件的範例是_執行可疑的雙重擴充_檔案。 |
| 原則 | 包含 Azure 原則所執行之所有效果動作作業的記錄。 原則事件的範例包括_Audit_和_Deny_。 原則所採取的每個動作會模型化為資源上的作業。 |


## <a name="next-steps"></a>後續步驟

* [建立記錄檔設定檔以匯出 Azure 活動記錄](activity-log-export.md)
* [將 Azure 活動記錄檔串流至事件中樞](activity-logs-stream-event-hubs.md)
* [將 Azure 活動記錄檔封存至儲存體](archive-activity-log.md)

