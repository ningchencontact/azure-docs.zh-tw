---
title: 使用 PowerShell 建立 Azure 服務的傳統警示 | Microsoft Docs
description: 當符合您指定的條件時，觸發電子郵件或通知、或呼叫網站 URL (Webhook) 或自動化。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: b08a8f66add45d64085ac05605fe3c7d7f91b705
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286194"
---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>使用 PowerShell 建立 Azure 服務的警示
> [!div class="op_single_selector"]
> * [入口網站](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

> [!NOTE]
> 本文說明如何建立舊版傳統計量警示。 Azure 監視器現在支援[更新更好的計量警示](monitoring-near-real-time-metric-alerts.md)。 這些警示可以監視多個計量，且能針對維度計量發出警示。 即將推出更新計量警示的 PowerShell 支援。
>
>

本文說明如何使用 PowerShell 來設定 Azure 傳統計量警示。  

您會收到以 Azure 服務計量為基礎的警示，或 Azure 中發生的事件警示。

* **計量值**：當指定的計量值超出您在任一方向指派的臨界值時會觸發警示。 也就是說，先符合條件而之後該條件不再符合時，兩方面皆會觸發警示。    
* **活動記錄事件**：警示可在「每一個」事件上觸發，或是在發生特定事件時觸發。 若要深入了解活動記錄警示，請參閱[建立活動記錄警示 (傳統)](monitoring-activity-log-alerts.md)。

您可以設定讓傳統計量警示在觸發時執行下列工作︰

* 傳送電子郵件通知給服務管理員和共同管理員。
* 傳送電子郵件給您指定的其他電子郵件地址。
* 呼叫 Webhook。
* 啟動執行 Azure Runbook (現階段只能從 Azure 入口網站啟動)。

您可以從下列位置設定和取得有關警示規則的資訊： 

* [Azure 入口網站](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure 命令列介面 (CLI)](insights-alerts-command-line-interface.md)
* [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

如需詳細資訊，您可以在需要說明的 PowerShell 命令後輸入 ```Get-Help```。

## <a name="create-alert-rules-in-powershell"></a>在 PowerShell 中建立警示規則
1. 登入 Azure。   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. 取得您可使用的訂用帳戶清單。 確認您使用正確的訂用帳戶。 如果不是，請使用 `Get-AzureRmSubscription` 的輸出來使用正確的訂用帳戶。

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. 使用下列命令列出資源群組上的現有規則：

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. 若要建立規則，您需要先取得幾項重要資訊：

    - 針對您想要為其設定警示的資源，取得其**資源識別碼**。
    - 該資源可用的**計量定義**。

     取得資源識別碼的方法之一，是使用 Azure 入口網站。 假設已建立資源，在入口網站中選取它。 然後在下一個刀鋒視窗中，選取 [設定] 區段中的 [屬性]。 [資源識別碼] 是下一個刀鋒視窗中的欄位。 
     
     您也可以使用 [Azure 資源總管](https://resources.azure.com/)取得資源識別碼。

     以下是 Web 應用程式的範例資源識別碼：

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     您可以使用 `Get-AzureRmMetricDefinition` 檢視特定資源的所有計量定義清單：

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     下列範例會產生資料表，其中有計量名稱及該計量的單位：

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     執行 `Get-Help Get-AzureRmMetricDefinition -Detailed` 可取得 Get-AzureRmMetricDefinition 的可用選項完整清單。
5. 下列範例會設定網站資源的警示。 只要持續 5 分鐘有收到任何流量，就會觸發警示，在這之後，5 分鐘沒收到任何流量也會觸發警示。

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. 若要在警示觸發時建立 Webhook 或傳送電子郵件，請先建立電子郵件或 Webhook。 然後立即在 -Actions 標籤後建立規則，如下列範例所示。 您無法將 Webhook 或電子郵件與已建立的規則建立關聯。

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. 查看個別規則，以確認您已正確建立警示。

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. 刪除您的警示。 這些命令會刪除先前在本文中建立的規則。

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>後續步驟
* [取得 Azure 監視的概觀](monitoring-overview.md) 中說明您可以收集和監視的資訊類型。
* 了解如何[在警示中設定 Webhook](insights-webhooks-alerts.md)。
* 了解如何[針對活動記錄事件設定警示](monitoring-activity-log-alerts.md)。
* 深入了解 [Azure 自動化 Runbook](../automation/automation-starting-a-runbook.md)。
* 依照 [收集診斷記錄檔概觀](monitoring-overview-of-diagnostic-logs.md) 中的做法，收集您服務中詳細的高頻率計量。
* 依照 [計量集合概觀](insights-how-to-customize-monitoring.md) 中的做法，確保您的服務可使用且有回應。
