---
title: 使用 Azure 入口網站建立 Azure 服務的傳統警示 | Microsoft Docs
description: 當符合您指定的條件時，觸發電子郵件或通知、或呼叫網站 URL (Webhook) 或自動化。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/23/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: d0e5512eb3f963898ded6d7155f8b75cfb6ef911
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287424"
---
# <a name="use-the-azure-portal-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>使用 Azure 入口網站在 Azure 監視器中為 Azure 服務建立傳統計量警示 

> [!div class="op_single_selector"]
> * [入口網站](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>

> [!NOTE]
> 本文說明如何建立舊版傳統計量警示。 「Azure 監視器」現已支援[新版的計量警示](monitoring-near-real-time-metric-alerts.md)。 


本文說明如何使用 Azure 入口網站來設定傳統 Azure 計量警示。 

您會收到以 Azure 服務計量為基礎的警示，或 Azure 中發生的事件警示。

* **量值**：當指定的量值超出您在任一方向指派的閾值時會觸發警示。 也就是說，先符合條件而之後該條件不再符合時，兩方面皆會觸發警示。    

* **活動記錄事件**：警示可在「每一個」事件上觸發，或是在發生特定事件時觸發。 深入了解[活動記錄警示](monitoring-activity-log-alerts.md)。

您可以設定讓傳統計量警示在觸發時執行下列動作︰

* 傳送電子郵件通知給服務管理員和共同管理員。
* 傳送電子郵件給您指定的其他電子郵件地址。
* 呼叫 Webhook。
* 啟動執行 Azure Runbook (現階段只能從 Azure 入口網站啟動)。

您可以從下列位置設定和取得有關傳統計量警示規則的資訊： 

* [Azure 入口網站](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 入口網站建立計量的警示規則
1. 在[入口網站](https://portal.azure.com/)中，找出您想要監視的資源，然後選取它。

2. 選取 [監視] 區段底下的 [警示 (傳統)]。 不同資源的文字和圖示會有些許不同。 如果在此處找不到 [警示 (傳統)]，可能會位在 [警示] 或 [警示規則] 中。

    ![監視](./media/insights-alerts-portal/AlertRulesButton.png)

3. 選取 [新增計量警示 (傳統)] 命令，然後填寫各欄位。

    ![新增警示](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **命名**警示規則。 然後選擇也會顯示在電子郵件通知中的 [描述]。

5. 選取您想要監視的**計量**。 然後選擇計量的**條件**和**閾值**。 同時選擇警示觸發程序之前，計量規則必須滿足的 [期間]。 例如，如果您使用「超過最後 5 分鐘」期間，且您的警示會尋找高於 80% 的 CPU，當 CPU 已持續 5 分鐘高於 80%，警示就會觸發。 發生第一次觸發之後，它會在 CPU 持續 5 分鐘低於 80 % 時再次觸發。 系統每分鐘會測量一次 CPU 計量。

6. 如果您想要讓系統管理員和共同管理員在警示引發時收到電子郵件通知，請選取 [電子郵件的擁有者...] 。

7. 如果您想要在警示引發時傳送通知給其他電子郵件地址，請在 [其他管理員的電子郵件] 欄位中新增。 使用下列格式，以分號分隔多個電子郵件：email@contoso.com; email2@contoso.com

8. 如果您想在警示引發時呼叫 Webhook，在 [Webhook]  欄位中放入有效的 URI。

9. 如果您使用 Azure 自動化，可以選取警示引發時執行的 Runbook。

10. 選取 [確定] 可建立警示。   

在幾分鐘之內，警示會開始作用，且先前所述觸發。

## <a name="manage-your-alerts"></a>管理警示
建立警示之後，您可以選取此警示，然後進行下列其中一個工作：

* 檢視圖表，其中顯示計量閾值與前一天的實際值。
* 編輯或刪除警示。
* 如果您想要暫時停止或恢復接收警示的通知，可以**停用**或**啟用**警示。

## <a name="next-steps"></a>後續步驟
* [取得 Azure 監視的概觀](monitoring-overview.md) 中說明您可以收集和監視的資訊類型。
* 深入了解[新版計量警示](monitoring-near-real-time-metric-alerts.md)。
* 深入了解 [在警示中設定 webhook](insights-webhooks-alerts.md)。
* 深入了解[在活動記錄事件上設定警示](monitoring-activity-log-alerts.md)。
* 深入了解 [Azure 自動化 Runbook](../automation/automation-starting-a-runbook.md)。
* 依照[診斷記錄概觀](monitoring-overview-of-diagnostic-logs.md) 中的做法，收集您服務中詳細的高頻率計量。
* 依照[計量集合概觀](insights-how-to-customize-monitoring.md) 中的做法，確保您的服務可使用且有回應。
