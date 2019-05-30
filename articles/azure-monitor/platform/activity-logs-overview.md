---
title: Azure 活動記錄檔概觀
description: 認識 Azure 活動記錄檔，並了解如何使用它來了解您的 Azure 訂用帳戶內發生的事件。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 34857108cf7f0580c380ffbd4bbcedb5cd5a807a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245031"
---
# <a name="overview-of-azure-activity-log"></a>Azure 活動記錄檔概觀

**Azure 活動記錄檔**提供深入了解在 Azure 中發生的訂用帳戶層級事件。 这包括从 Azure 资源管理器操作数据到服务运行状况事件更新的一系列数据。 活動記錄之前稱做_稽核記錄檔_或是_操作記錄檔_，因為系統管理類別會報告訂用帳戶的控制平面事件。 

使用活動記錄，來判斷_什麼_，_人員_，並_時_之任何寫入作業 (PUT、 POST、 DELETE)，您的訂用帳戶中的資源上。 您也可以了解作業的狀態和其他相關屬性。 

活動記錄不包含讀取 (GET) 作業，或是使用傳統 /RDFE 模型的資源的作業。

## <a name="comparison-to-diagnostic-logs"></a>診斷記錄的比較
會有單一的活動記錄，每個 Azure 訂用帳戶。 它提供有關外部資源作業的資料 （「 控制台 」）。 [診斷記錄](diagnostic-logs-overview.md)由資源發出，提供該資源 （「 資料平面 」） 之作業的相關資訊。 您必須啟用每個資源的診斷設定。

![相較於診斷記錄的活動記錄檔](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Azure 活動記錄主要是針對 Azure Resource Manager 中發生的活動。 它不會追蹤使用傳統 /RDFE 模型的 ugresources。 某些傳統資源類型在 Azure Resource Manager 中有 Proxy 資源提供者 (例如，Microsoft.ClassicCompute)。 如果您透過使用這些 Proxy 資源提供者的 Azure Resource Manager 來與傳統資源類型互動，則作業會顯示在活動記錄。 如果您在 Azure Resource Manager Proxy 之外與傳統資源類型互動，您的動作將只會記錄於「作業記錄」。 可在入口網站的個別區段中，瀏覽作業記錄。

## <a name="activity-log-retention"></a>活動記錄保留
活動記錄檔事件會儲存 90 天。 較長的時間，儲存此資料[收集在 Azure 監視器](activity-log-collect.md)或是[將它匯出至儲存體或事件中樞](activity-log-export.md)。

## <a name="view-the-activity-log"></a>檢視活動記錄檔
檢視活動記錄檔，所有的資源**監視器**在 Azure 入口網站中的功能表。 檢視特定資源中的活動記錄檔**活動記錄檔**該資源的功能表中的選項。 您也可以擷取活動記錄檔記錄，使用 PowerShell、 CLI 或 REST API。  請參閱[檢視，並擷取 Azure 活動記錄事件](activity-log-view.md)。

![檢視活動記錄檔](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>收集 Azure 監視器中的活動記錄檔
收集活動記錄到 Azure 監視器來分析與其他監視資料，但保留超過 90 天的資料中的 Log Analytics 工作區中。 請參閱[收集和分析 Azure 活動記錄檔，在 Azure 監視器中的 Log Analytics 工作區](activity-log-collect.md)。

![查詢活動記錄檔](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>匯出活動記錄
將活動記錄匯出到 Azure 儲存體中，以封存或第三方服務或自訂的分析解決方案將它串流至事件中樞以供擷取。 請參閱[將 Azure 活動記錄檔匯出](activity-log-export.md)。 您也可以分析活動記錄事件，在 Power BI 中使用[ **Power BI 內容套件**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)。

## <a name="alert-on-activity-log"></a>在 活動記錄警示
在活動記錄檔中建立特定事件時，您可以建立警示[活動記錄警示](activity-log-alerts.md)。 您也可以建立警示使用[記錄檔查詢](alerts-log-query.md)當活動記錄連線到 Log Analytics 工作區中，但不需費用，以記錄查詢警示。 沒有任何活動記錄警示的成本。

## <a name="categories-in-the-activity-log"></a>活動記錄中的類別
活動記錄中的每個事件都有特定分類下表中所述。 如需這些類別結構描述的完整詳細資料，請參閱 [Azure 活動記錄事件結構描述](activity-log-schema.md)。 

| 類別 | 描述 |
|:---|:---|
| 管理 | 包含的所有記錄，建立、 更新、 刪除和動作作業執行透過 Resource Manager。 系統管理事件的範例包括_建立虛擬機器_並_刪除網路安全性群組_。<br><br>每個使用者或應用程式使用 Resource Manager 所採取的動作會模型化為特定資源類型上的作業。 如果作業類型是_撰寫_，_刪除_，或_動作_，同時代表起點與成功的記錄 」 或 「 失敗的作業會記錄在系統管理類別。 系統管理事件也會包含訂用帳戶中的角色型存取控制的任何變更。 |
| 服務健康情況 | 包含在 Azure 中發生的任何服務健康情況事件的記錄。 服務健康狀態事件的範例_美國東部的 SQL Azure 發生停機_。 <br><br>服務健康情況事件有五個種類：_所需的動作_，_協助復原_，_事件_，_維護_，_資訊_，或_安全性_。 如果您擁有的資源會受到事件訂用帳戶中，只會建立這些事件。
| 資源健全狀況 | 包含對 Azure 資源內發生的任何資源健康狀態事件的記錄。 資源健康狀態事件的範例_虛擬機器健全狀況狀態變更為 無法使用_。<br><br>資源健康狀態事件可以代表其中一個四個健全狀況狀態：_可用_，_無法使用_，_降級_，和_未知_。 此外，資源健康情況事件可分類為正在_平台起始_或是_使用者起始_。 |
| 警示 | 包含 Azure 警示的啟用記錄。 警示事件的範例_myVM 上的 CPU 百分比已超過 80 過去 5 分鐘_。|
| Autoscale | 包含與作業有關的自動調整引擎，根據您定義您的訂用帳戶中的任何自動調整規模任何的設定事件的記錄。 自動調整事件的範例_自動調整相應增加動作失敗_。 |
| 建議 | 包含來自 Azure Advisor 的建議事件。 |
| 安全性 | 包含 Azure 資訊安全中心所產生的任何警示的記錄。 舉例來說，安全性事件_執行的可疑雙重擴充檔案_。 |
| 原則 | 包含所有效果動作所都執行的操作 Azure 原則的記錄。 原則事件的範例包括_稽核_並_拒絕_。 原則所採取的每個動作會模型化為資源上的作業。 |


## <a name="next-steps"></a>後續步驟

* [建立記錄檔設定檔，匯出 Azure 活動記錄檔](activity-log-export.md)
* [將 Azure 活動記錄檔串流至事件中樞](activity-logs-stream-event-hubs.md)
* [Azure 活動記錄封存至儲存體](archive-activity-log.md)

