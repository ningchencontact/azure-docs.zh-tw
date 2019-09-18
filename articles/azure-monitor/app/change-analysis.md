---
title: 在 Azure 監視器中使用應用程式變更分析來尋找 web 應用程式的問題 |Microsoft Docs
description: 在 Azure 監視器中使用應用程式變更分析, 針對 Azure App Service 上的即時網站上的應用程式問題進行疑難排解。
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 84e423ac055c074028df217060a548b932823496
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033389"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>在 Azure 監視器中使用應用程式變更分析 (預覽)

當發生即時網站問題或中斷時, 快速判斷根本原因是很重要的。 標準監視解決方案可能會警告您發生問題。 他們甚至可能會指出哪個元件失敗。 但此警示不一定會立即說明失敗的原因。 您知道您的網站在五分鐘前就已正常運作, 現在已中斷。 過去五分鐘內有哪些變更？ 這是應用程式變更分析設計來在 Azure 監視器中回答的問題。

藉由[Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)的威力, 變更分析可讓您深入瞭解 Azure 應用程式變更, 以增加可檢視性並減少 MTTR (平均修復時間)。

> [!IMPORTANT]
> 變更分析目前為預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供。 不建議將此版本用於生產工作負載。 某些功能可能不受支援, 或可能有限制的功能。 如需詳細資訊, 請參閱[Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

## <a name="overview"></a>總覽

變更分析會偵測各種類型的變更, 從基礎結構層到應用程式部署都是如此。 這是訂用帳戶層級的 Azure 資源提供者, 可檢查訂用帳戶中的資源變更。 變更分析會針對各種診斷工具提供資料, 以協助使用者瞭解哪些變更可能會造成問題。

下圖說明變更分析的架構:

![變更分析如何取得變更資料, 並將其提供給用戶端工具的架構圖](./media/change-analysis/overview.png)

目前的變更分析已整合到 App Service web 應用程式中的**診斷和解決問題**體驗。 若要在 web 應用程式中啟用變更偵測和查看變更, 請參閱本文稍後的*變更 Web Apps 功能的分析*一節。

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager 部署變更

變更分析會使用[Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), 提供裝載應用程式的 Azure 資源在一段時間內變更的歷程記錄。 例如, 變更分析可以偵測 IP 設定規則中的變更、受控身分識別, 以及 SSL 設定。 因此, 如果將標記新增至 web 應用程式, 則變更分析會反映變更。 只要 Azure 訂用帳戶中已啟用`Microsoft.ChangeAnalysis`資源提供者, 就可以使用這項資訊。

### <a name="changes-in-web-app-deployment-and-configuration"></a>Web 應用程式部署和設定的變更

變更分析會每隔4小時捕獲應用程式的部署和設定狀態。 例如, 它可以偵測應用程式環境變數中的變更。 此工具會計算差異, 並顯示已變更的內容。 不同于 Resource Manager 變更, 工具中可能不會立即提供程式碼部署變更資訊。 若要在 [變更分析] 中查看最新的變更, 請選取 [**立即掃描變更**]。

![[立即掃描變更] 按鈕的螢幕擷取畫面](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>相依性變更

變更資源相依性也會導致 web 應用程式發生問題。 例如, 如果 web 應用程式呼叫 Redis 快取, Redis cache SKU 可能會影響 web 應用程式的效能。 若要偵測相依性中的變更, 變更分析會檢查 web 應用程式的 DNS 記錄。 如此一來, 它會識別可能造成問題的所有應用程式元件中的變更。
目前支援下列相依性：
- Web 應用程式
- Azure 儲存體
- Azure SQL


## <a name="change-analysis-for-the-web-apps-feature"></a>變更 Web Apps 功能的分析

在 Azure 監視器中, 變更分析目前內建于自助**診斷和解決問題**體驗。 從您 App Service 應用程式的 [**總覽**] 頁面存取這項體驗。

![[總覽] 按鈕和 [診斷並解決問題] 按鈕的螢幕擷取畫面](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>在診斷和解決問題工具中啟用變更分析

1. 選取 [**可用性和效能**]。

    ![[可用性和效能] 疑難排解選項的螢幕擷取畫面](./media/change-analysis/availability-and-performance.png)

1. 選取 [**應用程式變更**]。 但**應用程式**當機時也無法使用此功能。

   ![[應用程式當機] 按鈕的螢幕擷取畫面](./media/change-analysis/application-changes.png)

1. 若要啟用變更分析, 請選取 [**立即啟用**]。

   ![[應用程式當機] 選項的螢幕擷取畫面](./media/change-analysis/enable-changeanalysis.png)

1. 開啟 [**變更分析**], 然後選取 [**儲存**]。

    ![[啟用變更分析] 使用者介面的螢幕擷取畫面](./media/change-analysis/change-analysis-on.png)


1. 若要存取變更分析, 請選取 [**診斷並解決問題** > **可用性和效能** > **應用程式**當機]。 您會看到一個圖表, 其中匯總一段時間的變更類型, 以及這些變更的詳細資料:

     ![變更差異視圖的螢幕擷取畫面](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>啟用大規模的變更分析

如果您的訂用帳戶包含多個 web 應用程式, 則在 web 應用程式層級上啟用服務會沒有效率。 執行下列腳本，以啟用您訂用帳戶中的所有 web 應用程式。

先決條件：
* PowerShell Az 模組。 請遵循[安裝 Azure PowerShell 模組中的](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-2.6.0)指示

執行下列指令碼：

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>後續步驟

- 啟用[Azure App Services 應用程式](azure-web-apps.md)的 Application Insights。
- 啟用[AZURE VM 和 azure 虛擬機器擴展集 IIS 託管應用程式](azure-vm-vmss-apps.md)的 Application Insights。
- 深入瞭解[Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), 其可協助您進行電源變更分析。
