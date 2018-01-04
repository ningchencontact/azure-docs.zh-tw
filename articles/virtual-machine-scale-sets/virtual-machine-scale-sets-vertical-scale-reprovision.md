---
title: "垂直調整 Azure 虛擬機器擴展集 | Microsoft Docs"
description: "如何垂直調整虛擬機器大小以回應 Azure 自動化的監視警示"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: negat
ms.openlocfilehash: 6e4733e023d1dc27fb099216f9afea07fe07446c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>與虛擬機器規模垂直自動調整規模設定
這篇文章描述如何使用或不使用重新佈建以垂直調整 Azure [虛擬機器擴充集](https://azure.microsoft.com/services/virtual-machine-scale-sets/) 。 不在擴展集 Vm 的垂直調整，請參閱[垂直調整 Azure 虛擬機器與 Azure 自動化](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

垂直調整也稱為向相應增加和相應減少，意味增加或減少虛擬機器 (VM) 大小以回應工作負載。 比較此行為與[水平延展](virtual-machine-scale-sets-autoscale-overview.md)，也稱為*向外延展*和*縮放*，並根據工作負載受到變更的 Vm 數目。

重新佈建表示移除現有的 VM，並以新的 VM 取代它。 當您增加或減少虛擬機器規模的 Vm 大小設定，在某些情況下，您想要調整大小的現有 Vm，並保留您的資料，而在其他情況下，您需要部署新的 Vm 的新大小。 本文件涵蓋這兩種情況。

在下列情況下，垂直調整可能十分有用︰

* 內建在虛擬機器上的服務使用量過低 (例如在週末)。 減少 VM 大小可以降低每月成本。
* 增加 VM 大小以應付更大的需求，而不需要建立額外的 VM。

您可以設定垂直縮放比例觸發根據度量根據的警示是從您的虛擬機器規模集。 啟動警示時，它就會引發 webhook 可以調整您的標尺的 runbook 設定向上或向下該觸發程序。 您可以依照下列步驟來設定垂直調整︰

1. 使用執行身分功能來建立 Azure 自動化帳戶。
2. 虛擬機器擴展集的垂直延展的 Azure 自動化 runbook 匯入您的訂用帳戶。
3. 將 Webhook 加入您的 Runbook 中。
4. 加入您的虛擬機器擴展集 使用 webhook 通知警示。

> [!NOTE]
> 垂直自動調整只能在特定範圍的 VM 大小內進行。 先比較各種大小的規格，然後再決定從一種大小調整成另一種大小 (數字較大並不一定代表 VM 大小較大)。 您可以在以下大小配對之間選擇調整︰
> 
> | 成對的調整 VM 大小 |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | 標準_D1 |標準_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>使用執行身分功能來建立 Azure 自動化帳戶
您必須進行第一件事是建立 Azure 自動化帳戶裝載用來調整虛擬機器規模集執行個體的 runbook。 最近[Azure 自動化](https://azure.microsoft.com/services/automation/)導入了對服務主體上設定自動執行 runbook 代表使用者的 「 執行身分帳戶 」 功能。 如需詳細資訊，請參閱

* [使用 Azure 執行身分帳戶驗證 Runbook](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>將 Azure 自動化垂直調整大小 Runbook 匯入訂用帳戶
在 Azure 自動化 Runbook 資源庫中已發佈的垂直調整您的虛擬機器擴展集所需的 runbook。 若要將其匯入到您的訂用帳戶，請依照這篇文章的步驟︰

* [Azure 自動化的 Runbook 和模組資源庫](../automation/automation-runbook-gallery.md)

從 [Runbooks] 功能表選擇 [瀏覽資源庫] 選項︰

![要匯入的 Runbook][runbooks]

需要如下所示匯入 Runbook。 根據您要使用重新佈建來垂直調整，以選取 Runbook：

![Runbook 資源庫][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>將 Webhook 加入您的 Runbook 中
一旦您已匯入 runbook，將 webhook 加入 runbook，可藉由從虛擬機器規模集警示。 本文說明如何為 Runbook 建立 Webhook 的詳細資訊：

* [Azure 自動化 Webhook](../automation/automation-webhooks.md)

> [!NOTE]
> 請確定您複製之後才關閉 webhook 對話將會需要此位址在下一節中的 webhook URI。
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>加入您的虛擬機器規模集的警示
示範如何將警示新增至虛擬機器規模的 PowerShell 指令碼設定如下。 請參閱下列文章，取得度量名稱以引發警示︰[Azure 監視器自動調整的常用度量](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md)。

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> 建議您設定合理的警示時間範圍以避免頻繁觸發垂直調整，及任何相關聯的服務中斷。 請考慮至少 20-30 分鐘以上的時段。 如果需要避免任何中斷，請考慮水平調整。
> 
> 

如需有關如何建立警示的詳細資訊，請參閱下列文章：

* [Azure 監視 PowerShell 快速入門範例](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Azure 監視跨平台 CLI 快速入門範例](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>總結
這篇文章示範簡單的垂直調整範例。 藉助這些建置組塊 (自動化帳戶、Runbook、Webhook、警示)，您可以連接各式各樣的事件與一組自訂的動作。

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
