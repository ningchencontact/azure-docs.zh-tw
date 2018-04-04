---
title: 探索 Azure 監視器中的新警示體驗 | Microsoft Docs
description: 了解 Azure 中簡單且可調整的新警示體驗如何讓您能更輕鬆地編寫、檢視及管理警示
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: 356988e8ae743d73c8e2cc7cc106cbc5b0d1a423
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Azure 監視器中的新警示體驗

## <a name="overview"></a>概觀
警示有了新體驗。 舊版警示體驗現在位於 [警示 (傳統)] 索引標籤底下。與 [警示 (傳統)] 體驗相比，新的 [警示] 體驗具有下列優點：

 - **分隔引發的警示和警示規則** - 區分 [警示規則] (定義觸發警示的條件) 和 [引發的警示] (警示規則引發的執行個體)，因此將操作與設定檢視分開。
 - **統一的撰寫體驗** -「Azure 監視器」、Log Analytics 及 Application Insights 之計量、記錄和活動記錄的所有警示建立作業都在一個地方完成。 
 - **在 Azure 入口網站中檢視引發的 Log Analytics 警示** - 您現在也能在訂用帳戶中查看引發的 Log Analytics 警示。 先前這些警示是在個別的入口網站中。 
 - **更好的工作流程** - 新的警示編寫體驗會引導使用者進行設定警示規則的程序，而能更容易找到要取得警示的正確項目。
 

下列各節詳細說明新體驗的運作方式。

## <a name="alert-rules-terminology"></a>警示規則術語
新的警示體驗使用下列概念來分隔 [警示規則] 和 [引發的警示] 物件，同時在不同的警示類型之間又維持統一的編寫體驗。

- **目標資源** - 目標可以是任何 Azure 資源。 目標資源定義可用於警示的範圍和訊號。 範例目標：虛擬機器、儲存體帳戶、虛擬機器擴展集、Log Analytics 工作區或 Application Insights 資源。

- **準則** - 準則是目標資源上所套用訊號和邏輯的組合。 範例：百分比 CPU > 70%、伺服器回應時間 > 4 毫秒、記錄查詢的結果計數 > 100 等等。 

- **訊號** - 訊號是由目標資源所發出並可以是數種類型。 **計量**、**活動記錄**、**Application Insights** 及**記錄**都是支援的訊號類型。

- **邏輯** - 可檢查訊號是否在預期的範圍/值內的使用者定義邏輯。  
 
- **動作** - 引發警示時所採取的動作。 例如，傳送郵件給某個電子郵件地址或呼叫 Webhook URL。 引發警示時，可能會進行多個動作。 這些警示支援動作群組。  
 
- **警示規則** - 將觸發警示的條件。 警示規則會擷取用於警示的「目標」和「準則」。 警示規則可處於已啟用或已停用狀態。
 
    > [!NOTE]
    > 這與 [警示 (傳統)] 體驗不同，其中警示同時代表規則和引發的警示，因此狀態可以是 [警告]、[作用中] 或 [已停用] 其中之一。
    >

## <a name="single-place-to-view-and-manage-alerts"></a>檢視和管理警示的單一位置
[警示] 體驗的目標是要成為檢視和管理所有 Azure 警示的單一位置。 下列各小節說明新體驗每個個別畫面的功能。

### <a name="alerts-overview-page"></a>警示概觀頁面
[監視器 - 警示] 概觀頁面會顯示所有引發之警示及已設定/已啟用之警示規則總計的彙總摘要。 它也會顯示所有已引發警示的清單。 變更訂用帳戶或篩選參數會更新彙總和引發警示的清單。

> [!NOTE]
> [警示] 中顯示的 [引發的警示] 僅限於支援的計量和活動記錄警示；[Azure 監視器概觀] 會顯示引發的警示計數，包括舊版「Azure 警示」中引發的警示

 ![警示概觀](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>警示規則管理
[監視器 - 警示 > 規則] 是可管理各個 Azure 訂用帳戶之所有警示規則的單一頁面。 它會列出所有警示規則 (已啟用或已停用)，並可根據目標資源、資源群組、規則名稱或狀態來排序。 您也可以從這個頁面停用/啟用或編輯警示規則。  

 ![警示規則](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>所有監視資源一體適用的警示編寫體驗
在新的 [警示] 體驗中，不論監視服務或訊號類型為何，都能以一致的方式編寫警示。 所有引發的警示和相關的詳細資料都提供在單一頁面中。  
 
編寫警示是一個三步驟工作，使用者先挑選警示的目標，接著選取正確的訊號，然後指定要對訊號套用的邏輯來作為警示規則的一部分。 這個簡化的編寫流程讓使用者不再需要先知道監視來源或支援的訊號，就能選取 Azure 資源。 一般編寫體驗會根據選取的目標資源自動篩選可用訊號的清單，然後引導建立警示邏輯

您可以在[這裡](monitor-alerts-unified-usage.md)深入了解如何建立下列警示類型。
- 計量警示
- 記錄警示 (Log Analytics)
- 記錄警示 (活動記錄)
- 記錄警示 (Application Insights)

 

## <a name="alert-types-supported"></a>支援的警示類型


| **訊號類型** | **監視來源** | **說明** | 
|-------------|----------------|-------------|
| 計量 | Azure 監視器 | 也稱為[**近乎即時計量警示**](monitoring-near-real-time-metric-alerts.md)，這些計量警示支援以 1 分鐘一次的頻率評估計量條件，並允許多計量規則。 所支援的資源類型清單可在[這裡](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported)取得。 新的 [警示] 體驗中不支援如[這裡](monitoring-overview-alerts.md#alerts-in-different-azure-services)所定義的舊版計量警示。 您可以在 [警示 (傳統)] 底下找到它們|
| 記錄檔  | Log Analytics | 當記錄搜尋查詢高於計量和/或事件資料符合特定準則時，接收通知或執行自動化動作。|
| 活動記錄檔 | 活動記錄檔 | 此類別包含的記錄是透過選取的目標 (資源/資源群組/訂用帳戶) 執行的所有建立、更新及刪除動作。 |
| 記錄檔  | 服務健康狀態記錄 | 在 [警示] 體驗中不支援。   |
| 記錄檔  | Application Insights | 此類別包含的記錄含有應用程式的效能詳細資料。 您可以使用分析查詢，定義要根據應用程式資料來採取動作的條件。 |
| 計量 | Application Insights | 在 [警示] 體驗中不支援。 您可以在 [警示 (傳統)] 底下找到它們 |
| 可用性測試 | Application Insights | 在 [警示] 體驗中不支援。 您可以在 [警示 (傳統)] 底下找到它們 |


## <a name="next-steps"></a>後續步驟
- [了解如何使用新的警示體驗來建立、檢視及管理警示](monitor-alerts-unified-usage.md)
- [了解警示體驗中的記錄警示](monitor-alerts-unified-log.md)
- [了解警示體驗中的計量警示](monitoring-near-real-time-metric-alerts.md)
- [了解警示體驗中的活動記錄警示](monitoring-activity-log-alerts-new-experience.md)
