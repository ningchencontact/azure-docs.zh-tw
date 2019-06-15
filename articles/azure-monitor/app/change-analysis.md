---
title: 使用 Azure 監視器中的應用程式變更分析，來尋找 web 應用程式的問題 |Microsoft Docs
description: 使用 Azure 監視器中的應用程式變更分析，在 Azure App Service 上的即時站台上的應用程式問題進行疑難排解。
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 2a31131b662d01f9841a3f1c5b0a6c459a117e77
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075371"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>使用 Azure 監視器 」 中的應用程式變更分析 （預覽）

當即時網站問題或中斷發生時，快速判斷根本原因是重要的。 標準的監視解決方案可能會通知您的問題。 它們甚至可能會指出失敗的元件。 但是，此警示不一定會立即說明失敗的原因。 您知道您的網站處理五分鐘，而現在它已經破壞。 在過去五分鐘內變更內容？ 這是難以抉擇應用程式變更分析是設計用來回答 Azure 監視器中。 

建置的能力[Azure 資源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)，變更分析提供深入了解您的 Azure 應用程式變更，以增加可檢視性，並減少 MTTR （平均時間來修復）。

> [!IMPORTANT]
> 變更分析目前為預覽狀態。 此預覽版本提供不含服務等級合約。 此版本不建議用於生產工作負載。 某些功能可能不支援，或可能已經限制功能。 如需詳細資訊，請參閱 <<c0> [ 使用 Microsoft Azure 預覽補充規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview"></a>概觀

變更分析偵測到各種類型的變更，從基礎結構層到應用程式部署。 它是檢查資源變更訂用帳戶中的訂用帳戶層級的 Azure 資源提供者。 變更分析提供各種診斷工具，可協助使用者了解哪些變更的資料可能會造成問題。

下圖說明變更分析的架構：

![變更分析取得變更資料的方式，並提供它給用戶端工具的架構圖](./media/change-analysis/overview.png)

目前的變更分析會整合到**診斷並解決問題**App Service web 應用程式中體驗。 若要啟用變更偵測，並在 web 應用程式中檢視變更，請參閱*Web 應用程式功能的變更分析*本文稍後的章節。

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager 部署的變更

使用[Azure 資源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)，變更分析會提供如何隨著時間變更裝載您的應用程式的 Azure 資源的歷程記錄。 變更分析可以偵測，例如，變更 IP 組態規則、 受管理的身分識別和 SSL 設定。 因此如果標記新增至 web 應用程式時，變更分析會反映變更。 這項資訊可用，只要`Microsoft.ChangeAnalysis`中 Azure 訂用帳戶已啟用資源提供者。

### <a name="changes-in-web-app-deployment-and-configuration"></a>在 web 應用程式部署和設定的變更

變更分析會擷取每隔 4 小時的應用程式的部署和設定狀態。 它可以偵測，例如，應用程式的環境變數中的變更。 此工具會計算差異，並提供變更的功能。 不同於 Resource Manager 的變更，可能無法立即出現在此工具的程式碼部署變更資訊。 若要變更分析中檢視最新的變更，請選取**掃描變更現在**。

![「 現在變更掃描 」 按鈕的螢幕擷取畫面](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>相依性變更

資源相依性的變更也可能導致問題的 web 應用程式中。 例如，如果 web 應用程式呼叫 Redis 快取，Redis 快取 SKU 可能會影響 web 應用程式效能。 若要偵測變更相依性中，變更分析會檢查 web 應用程式的 DNS 記錄。 如此一來，它會識別可能會造成問題的所有應用程式元件中的變更。

## <a name="change-analysis-for-the-web-apps-feature"></a>變更分析 Web 應用程式功能

Azure 監視器變更分析目前內建的自助**診斷並解決問題**體驗。 存取這項體驗，從**概觀**App Service 應用程式頁面。

![[概觀] 按鈕的螢幕擷取畫面和 「 診斷及解決的問題 」 按鈕](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>啟用 診斷中的變更分析，並解決問題的工具

1. 選取 **可用性和效能**。

    ![「 可用性與效能 > 疑難排解選項的螢幕擷取畫面](./media/change-analysis/availability-and-performance.png)

1. 選取 **應用程式當機**。

   ![「 應用程式當機 」 按鈕的螢幕擷取畫面](./media/change-analysis/application-crashes-tile.png)

1. 若要啟用變更分析，請選取**立即啟用**。

   ![「 應用程式當機 」 選項的螢幕擷取畫面](./media/change-analysis/application-crashes.png)

1. 若要充分利用完整的變更分析功能，開啟**變更分析**，**程式碼變更掃描**，並**Alwayson**。 然後選取 [儲存]  。

    ![「 啟用變更分析 」 使用者介面的螢幕擷取畫面](./media/change-analysis/change-analysis-on.png)

    - 啟用**變更分析**偵測資源層級變更。 
    - 啟用**程式碼變更掃描**查看部署檔案和站台設定變更。 
    - 啟用**Always on**變更掃描的效能最佳化。 但請記住，這項設定可能會導致額外的費用。

1. 若要存取變更分析，請選取**診斷並解決問題** > **可用性和效能** > **應用程式當機**。 您會看到摘要的變更類型經過一段時間，以及這些變更的詳細資訊的圖形：

     ![變更差異檢視的螢幕擷取畫面](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>啟用大規模的變更分析

如果您的訂用帳戶包含多個 web 應用程式，啟用層級的 web 應用程式服務會是效率不佳。 在此情況下，請遵循這些替代指示。

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>註冊您的訂用帳戶的變更分析資源提供者

1. 註冊變更分析功能旗標 （預覽）。 功能旗標為預覽狀態，因為您需要註冊，如此即可讓它顯示您訂用帳戶：

   1. 開啟 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)。

      ![變更 Cloud Shell 的螢幕擷取畫面](./media/change-analysis/cloud-shell.png)

   1. 將介面類型變更為**PowerShell**。

      ![變更 Cloud Shell 的螢幕擷取畫面](./media/change-analysis/choose-powershell.png)

   1. 執行下列 PowerShell 命令：

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```
    
1. 註冊訂用帳戶的變更分析資源提供者。

   - 移至**訂用帳戶**，然後選取您想要變更服務中啟用的訂用帳戶。 然後選取 資源提供者：

        ![螢幕擷取畫面顯示如何註冊變更分析資源提供者](./media/change-analysis/register-rp.png)

       - 選取  **Microsoft.ChangeAnalysis**。 然後在頁面頂端，選取**註冊**。

       - 啟用資源提供者之後，您可以在 web 應用程式，可偵測的變更層級的部署設定隱藏的標籤。 若要設定隱藏的標記，請依照下列指示**無法擷取變更分析資訊**。

   - 或者，您可以使用 PowerShell 指令碼以註冊資源提供者：

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration
    
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        若要使用 PowerShell 來設定隱藏的標記的 web 應用程式上，執行下列命令：
    
        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > 新增隱藏的標記之後，您可能仍然需要等待最多 4 個小時再開始看到變更。 結果會延遲，因為變更分析會掃描您 web 應用程式只能每隔 4 小時。 4 小時排程限制掃描的效能影響。

## <a name="next-steps"></a>後續步驟

- 監視應用程式服務更有效地依[啟用 Application Insights 功能](azure-web-apps.md)Azure 監視器中。
- 深入了解[Azure 資源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)，它可協助 power 變更分析。
