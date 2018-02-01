---
title: "使用自動化 Runbook 回應 Azure 警示 | Microsoft Docs"
description: "了解如何觸發 Runbook 以在 Azure 警示引發時執行。"
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 486a3c80c9d6c19ca0a7ba7d4e3cdcde927053b9
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2018
---
# <a name="respond-to-azure-alerts-with-an-automation-runbook"></a>使用自動化 Runbook 回應 Azure 警示

[Azure 監視器](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json)可針對 Microsoft Azure 中的大多數服務提供基本等級的計量與記錄。 Azure 自動化 Runbook 可以使用[動作群組](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json)呼叫或是從傳統警示呼叫，以根據警示自動化工作。 本文示範如何根據這些警示設定及執行 Runbook。

## <a name="alert-types"></a>警示類型

Runbook 是在所有三種類型警示上支援的動作。 當警示呼叫 Runbook 時，實際呼叫是對 Webhook 的 HTTP POST 要求。 POST 要求的本文包含 JSON 格式的物件，此物件中包含與警示相關的實用屬性。 下表包含每種警示類型的承載結構描述連結：

|警示  |說明|承載結構描述  |
|---------|---------|---------|
|[傳統計量警示](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |當任何平台層級度量符合特定條件時收到通知 (例如 VM 上的 CPU % 在過去 5 分鐘大於 90)。| [承載結構描述](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[活動記錄警示](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |當 Azure 活動記錄中有任何新事件符合特定條件時收到通知 (例如當 myProductionResourceGroup 中發生「刪除 VM」作業時，或當新服務健康狀態事件的狀態顯示為「作用中」時)。| [承載結構描述](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[近乎即時計量警示](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |當一或多個平台層級計量符合指定的條件 (例如過去 5 分鐘 VM 上的 CPU% 超過 90 且 Network In 超過 500 MB)，以比計量警示快的速度接收通知。| [承載結構描述](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

由於每個警示所提供的資料不同，所以必須以不同方式處理每個警示。 在下一節中，您可以了解如何建立 Runbook 來處理這些不同類型的警示。

## <a name="create-a-runbook-to-handle-alerts"></a>建立 Runbook 來處理警示

若要對警示使用自動化，您需要具有邏輯的 Runbook，以管理傳遞給 Runbook 的警示 JSON 承載。 下列範例 Runbook 必須從 Azure 警示呼叫。 如上一節中所述，每一種警示類型有不同的結構描述。 指令碼會從警示採用 `WebhookData` Runbook 輸入參數中的 Webhook 資料，並且評估 JSON 承載，以判斷使用哪個警示類型。 下列範例會在來自 VM 的警示上使用。 它會擷取承載的 VM 資料，並使用該資訊來停止 VM。 連線必須在執行 Runbook 所在的自動化帳戶中設定。

Runbook 會使用 **AzureRunAsConnection** [執行身分帳戶](automation-create-runas-account.md)向 Azure 驗證，以便對虛擬機器執行管理動作。

下列 PowerShell 指令碼可以針對用於許多不同資源進行改變。

採用下列範例並使用範例 PowerShell 建立稱為 **Stop-AzureVmInResponsetoVMAlert** 的 Runbook。

1. 開啟自動化帳戶。
1. 按一下 [程序自動化] 下的 [Runbook]。 隨即顯示 Runbook 的清單。
1. 按一下清單頂端的 [新增 Runbook] 按鈕。 在 [新增 Runbook] 分頁上，選取 [快速建立]。
1. 針對 Runbook [名稱] 輸入 "Stop-AzureVmInResponsetoVMAlert"，然後針對 [Runbook 類型] 選取 [PowerShell]。 按一下頁面底部的 [新增] 。
1. 將下列 PowerShell 範例複製到編輯窗格中。 按一下 [發行] 以儲存並發行 Runbook。

```powershell-interactive
<#
.SYNOPSIS
  This runbook will stop a resource management VM in response to an Azure alert trigger.

.DESCRIPTION
  This runbook will stop an resource management VM in response to an Azure alert trigger.
  Input is alert data with information needed to identify which VM to stop.
  
  DEPENDENCIES
  - The runbook must be called from an Azure alert via a webhook.
  
  REQUIRED AUTOMATION ASSETS
  - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
  - An Automation certificate asset called "AzureRunAsCertificate".

.PARAMETER WebhookData
   Optional (user does not need to enter anything, but the service will always pass an object)
   This is the data that is sent in the webhook that is triggered from the alert.

.NOTES
   AUTHOR: Azure Automation Team
   LASTEDIT: 2017-11-22
#>

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Get the info needed to identify the VM (depends on the payload schema)
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {
        # This is the near-real-time Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $ResourceName = $AlertContext.resourceName
        $status = ($WebhookBody.data).status
    }
    elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
        # This is the Activity Log Alert schema
        $AlertContext = [object] (($WebhookBody.data).context).activityLog
        $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
        $status = ($WebhookBody.data).status
    }
    elseif ($schemaId -eq $null) {
        # This is the original Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $status = $WebhookBody.status
    }
    else {
        # Schema not supported
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    Write-Verbose "status: $status" -Verbose
    if ($status -eq "Activated")
    {
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId
        Write-Verbose "resourceType: $ResourceType" -Verbose
        Write-Verbose "resourceName: $ResourceName" -Verbose
        Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
        Write-Verbose "subscriptionId: $SubId" -Verbose

        # Do work only if this is a resource management VM
        if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
        {
            # This is the VM
            Write-Verbose "This is a resource management VM." -Verbose

            # Authenticate to Azure with service principal and certificate and set subscription
            Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
            $ConnectionAssetName = "AzureRunAsConnection"
            Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Verbose "Authenticating to Azure with service principal." -Verbose
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Write-Verbose "Setting subscription to work against: $SubId" -Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Stop the VM
            Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
            Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
            # [OutputType(PSAzureOperationResponse")]
        }
        else {
            # ResourceType not supported
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    }
    else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
    }
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="create-an-action-group"></a>建立動作群組

動作群組是根據警示採取的動作集合。 Runbook 只是多個可用於動作群組之動作的其中之一。

1. 在入口網站中，選取 [監視]。

1. 選取 [設定] 底下的 [動作群組]。

1. 選取 [新增動作群組]，並填寫各欄位。

1. 在 [動作群組名稱] 方塊中輸入名稱，然後在 [簡短名稱] 方塊中，輸入名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。

1. [訂用帳戶] 方塊會自動填入您目前的訂用帳戶。 訂用帳戶是要儲存動作群組的位置。
選取要在其中儲存動作群組的 [資源群組]。

針對此範例，您會建立兩個動作，Runbook 動作和通知動作。

### <a name="runbook-action"></a>Runbook 動作

下列步驟會在動作群組內建立 Runbook 動作。

1. 在 [動作] 底下，指定動作的名稱。

1. 針對 [動作類型] 選取 [自動化 Runbook]。

1. 在 [詳細資料] 底下選取 [編輯詳細資料]

1. 在 [設定 Runbook] 分頁上，選取 [Runbook 來源] 底下的 [使用者]。

1. 選擇您的**訂用帳戶**和**自動化帳戶**，最後選取您在上一個步驟建立，稱為「Stop-AzureVmInResponsetoVMAlert」的 Runbook。

1. 完成時，按一下 [確定]。

### <a name="notification-action"></a>通知動作

下列步驟會在動作群組內建立通知動作。

1. 在 [動作] 底下，指定動作的名稱。

1. 針對 [動作類型] 選取 [電子郵件]。

1. 在 [詳細資料] 底下選取 [編輯詳細資料]

1. 在 [電子郵件] 分頁上，輸入要通知的電子郵件地址，然後按一下 [確定]。 新增電子郵件地址以及 Runbook 作為動作很有幫助，因為您會在 Runbook 啟動時收到通知。 您的動作群組看起來應該如下圖所示：

   ![新增動作群組分頁](./media/automation-create-alert-triggered-runbook/add-action-group.png)

1. 按一下 [確定] 來建立動作群組。

您可以使用建立的動作群組，建立[活動記錄警示](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)或[近乎即時警示](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal)，並使用您建立的動作群組。

## <a name="classic-alert"></a>傳統警示

傳統警示是根據計量而且不會使用動作群組，但是具有根據它們的 Runbook 動作。 若要建立傳統警示，請使用下列步驟：

1. 選取 [+ 新增計量警示]。

1. 將計量警示命名為 'myVMCPUAlert'，並提供警示的簡短描述。

1. 將計量警示的 [條件] 設為「大於」，將 [閾值] 設為 '10'，將 [期間] 設為「過去 5 分鐘」。

1. 在 [採取動作] 底下，選取 [從此警示執行 Runbook]

1. 在 [設定 Runbook] 分頁上，針對 [Runbook 來源] 選取 [使用者]。 選擇您的自動化帳戶，並選取 **Stop-AzureVmInResponsetoVMAlert** Runbook。 按一下 [確定]，然後按一下 [確定] 以儲存警示規則。

## <a name="next-steps"></a>後續步驟

* 如需有關使用 Webhook 啟動自動化 Runbook 的詳細資訊，請參閱[從 Webhook 啟動 Runbook](automation-webhooks.md)
* 如需以不同方式啟動 Runbook 的詳細資訊，請參閱[啟動 Runbook](automation-starting-a-runbook.md)。
* 若要了解如何建立活動記錄警示，請參閱[建立活動記錄警示](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)
* 若要了解如何建立近乎即時警示，請造訪[使用 Azure 入口網站建立警示規則](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal)。