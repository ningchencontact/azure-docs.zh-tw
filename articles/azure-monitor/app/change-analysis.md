---
title: Azure 監視器應用程式變更分析-探索可能會影響即時網站問題/中斷與 Azure 監視器應用程式的變更將會變更分析 |Microsoft Docs
description: 針對 Azure App Service 上使用 Azure 監視器應用程式變更分析的應用程式即時網站問題進行疑難排解
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 5bd3816e65398283de85b4551a137b3f97db4cc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226298"
---
# <a name="application-change-analysis-public-preview"></a>Application Change Analysis (公開預覽版)

即時網站問題中斷發生時，快速判斷根本原因是重要的。 監視解決方案的標準可協助您快速識別問題，而通常甚至元件失敗。 但這不一定會導致立即發生失敗的原因說明。 現在它已經破壞您的網站會處理五分鐘。 在過去五分鐘內變更內容？ 這是新功能的 Azure 監視器應用程式變更分析設計為回答這個問題。 藉由建置的能力[Azure 資源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)應用程式變更分析提供深入了解您的 Azure 應用程式變更，以增加可檢視性，並減少 MTTR （平均時間來修復）。

> [!IMPORTANT]
> Azure 監視器應用程式變更分析目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview-of-change-analysis-service"></a>變更分析服務概觀
變更分析服務偵測到各種類型的基礎結構層到應用程式部署的變更。 它是訂用帳戶層級的 Azure 資源提供者，就會查看訂用帳戶中的資源變更，並提供各種不同的診斷工具，來協助使用者了解哪些變更的資料可能會造成問題。

下圖說明變更分析服務的架構：![架構圖表變更 analysis services 取得的方式變更資料，並提供資料給用戶端工具](./media/change-analysis/overview.png)

目前此工具已整合到應用程式服務 web 應用程式診斷並解決問題體驗。 請參閱*針對應用程式服務 Web 應用程式變更 Analysis services*一節有關如何啟用和檢視的 web 應用程式所做的變更。

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager 部署的變更
運用[Azure 資源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)變更分析工具會提供如何隨著時間變更裝載您的應用程式的 Azure 資源的歷程記錄。 比方說，如果 web 應用程式新增至它的標記，則變更會反映在變更分析工具。
這項資訊是永遠可用，只要`Microsoft.ChangeAnalysis`資源提供者是上的架到 Azure 訂用帳戶。

### <a name="web-application-deployment-and-configuration-changes"></a>Web 應用程式部署和組態變更
變更分析工具會每隔 4 小時的應用程式的部署和設定狀態擷取計算差異，並呈現變更的功能。 這類變更的範例包括環境變數變更時，應用程式、 IP 設定規則變更、 受控服務身分識別變更、 SSL 設定的變更等等。
不同於 Resource Manager 的變更，這種類型的變更資訊可能無法立即在工具中。 若要檢視最新的變更，使用工具中的 '掃描變更現在' 按鈕。

![變更掃描的螢幕擷取畫面現在按鈕診斷並解決問題的工具，搭配 app service web 應用程式的變更分析整合](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>相依性變更
相依性資源也可能是問題的原因。 例如，如果 web 應用程式呼叫 Redis 快取，Redis 快取 SKU 所可能會影響 web 應用程式效能。 藉由查看 web 應用程式的 DNS 記錄變更分析服務也會顯示相依性變更的資訊來識別可能導致問題的應用程式的所有元件中的變更。


## <a name="change-analysis-service-for-app-services-web-app"></a>變更應用程式服務 Web 應用程式的分析服務

Azure 監視器應用程式變更分析目前內建的自助**診斷並解決問題**體驗，從可以存取哪一個**概觀**一節的 Azure App Service應用程式：

![Azure App Service 的螢幕擷取畫面概觀頁面上，使用 概觀 按鈕周圍的紅色方塊以及診斷和解決問題 按鈕](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-diagnose-and-solve-problems-tool"></a>啟用變更分析診斷並解決問題的工具

1. 選取**可用性和效能**

    ![可用性和效能疑難排解選項的螢幕擷取畫面](./media/change-analysis/availability-and-performance.png)

2. 按一下 **應用程式當機**圖格。

   ![與應用程式當機之磚的螢幕擷取畫面](./media/change-analysis/application-crashes-tile.png)

3. 若要啟用**變更分析**選取**立即啟用**。

   ![可用性和效能疑難排解選項的螢幕擷取畫面](./media/change-analysis/application-crashes.png)

4. 要採取的完整優點變更分析功能集**變更 Analysis**，**程式碼變更掃描**，和**Alwayson**至**上**然後選取**儲存**。

    ![Azure App Service 啟用變更分析使用者介面的螢幕擷取畫面](./media/change-analysis/change-analysis-on.png)

    如果**變更分析**已啟用，您將能夠偵測資源層級變更。 如果**程式碼變更掃描**已啟用，您將也會看到部署檔案和站台設定變更。 啟用**Always on**會最佳化變更掃描的效能，但可能會產生額外的費用，從計費的觀點來看。

5.  一旦啟用的所有項目，選取**診斷並解決問題** > **可用性和效能** > **應用程式當機**可讓您存取變更分析體驗。 圖形將摘要說明發生一段時間，以及詳細資料，這些變更的變更的類型：

     ![變更差異檢視的螢幕擷取畫面](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-service-at-scale"></a>啟用大規模的變更分析服務
如果您有許多 web 應用程式，您的訂用帳戶中，啟用在每個 web 應用程式層級的服務將會沒有效率。 以下是一些替代的上線指示。

#### <a name="registering-change-analysis-resource-provider-for-your-subscription"></a>註冊訂用帳戶的變更分析資源提供者

1. 註冊變更分析預覽功能旗標

    由於這項功能處於預覽狀態，您必須先註冊才會顯示您訂用帳戶的功能旗標。
    - 開啟 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)。

    ![變更 Azure Cloud Shell 的螢幕擷取畫面](./media/change-analysis/cloud-shell.png)

    - 殼層類型變更為 PowerShell:

    ![變更 Azure Cloud Shell 的螢幕擷取畫面](./media/change-analysis/choose-powershell.png)

    - 執行下列 PowerShell 命令：

    ``` PowerShell

    Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to onboard
    Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
    Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag

    ```

2. 變更分析資源提供者註冊訂用帳戶

    - 瀏覽至 訂用帳戶，選取您想要上架變更服務的訂用帳戶，然後按一下 資源提供者：

        ![正在變更分析 RP 註冊從訂用帳戶 刀鋒視窗的螢幕擷取畫面](./media/change-analysis/register-rp.png)

    - 選取  *Microsoft.ChangeAnalysis*然後按一下*註冊*頁面頂端。

    - 上架資源提供者之後，請依照下列指示*無法擷取變更的分析資訊*下方設定隱藏的標記的 web 應用程式，以啟用部署層級變更偵測 web 應用程式。

3. 或者以上述步驟 2，您可以註冊資源提供者，透過 PowerShell 指令碼：

    ```PowerShell
    Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
    ```

4. 若要設定隱藏的標記的 web 應用程式，使用 PowerShell 上，執行下列命令：

    ```powershell
    $webapp=Get-AzWebApp -Name <name_of_your_webapp>
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
    ```

> [!NOTE]
> 加入隱藏的標記後，您可能仍然需要一開始等候最多 4 小時，可以先檢視變更。 這是因為 4 小時 freqeuncy 變更分析服務用來掃描您 web 應用程式，同時又能限制掃描的效能影響。

## <a name="next-steps"></a>後續步驟

- 提升您監視 Azure App Service[藉由啟用 Application Insights 功能](azure-web-apps.md)Azure 監視器。
- 增強了解[Azure 資源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)幫助 power 變更分析的 Azure 監視器應用程式。
