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
ms.openlocfilehash: b04bd57c66b52e9a2c14d43c9e89d7c54fc48ae2
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415808"
---
# <a name="azure-monitor-application-change-analysis-public-preview"></a>Azure 監視器應用程式變更分析 （公開預覽）

即時網站問題中斷發生時，快速判斷根本原因是重要的。 監視解決方案的標準可協助您快速識別問題，而通常甚至元件失敗。 但這不一定會導致立即發生失敗的原因說明。 現在它已經破壞您的網站會處理五分鐘。 在過去五分鐘內變更內容？ 這是新功能的 Azure 監視器應用程式變更分析設計為回答這個問題。 藉由建置的能力[Azure 資源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)應用程式變更分析提供深入了解您的 Azure App Service 應用程式變更，以增加可檢視性，並減少 MTTR （平均時間來修復）。

> [!IMPORTANT]
> Azure 監視器應用程式變更分析目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-do-i-use-application-change-analysis"></a>如何使用應用程式變更分析？

Azure 監視器應用程式變更分析目前內建的自助**診斷並解決問題**體驗，從可以存取哪一個**概觀**一節的 Azure App Service應用程式：

![Azure App Service 的螢幕擷取畫面概觀頁面上，使用 概觀 按鈕周圍的紅色方塊以及診斷和解決問題 按鈕](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis"></a>啟用變更分析

1. 選取**可用性和效能**

    ![可用性和效能疑難排解選項的螢幕擷取畫面](./media/change-analysis/availability-and-performance.png)

2. 按一下 **應用程式當機**圖格。

   ![與應用程式當機之磚的螢幕擷取畫面](./media/change-analysis/application-crashes-tile.png)

3. 若要啟用**變更分析**選取**立即啟用**。

   ![可用性和效能疑難排解選項的螢幕擷取畫面](./media/change-analysis/application-crashes.png)

4. 要採取的完整優點變更分析功能集**變更 Analysis**，**程式碼變更掃描**，和**Alwayson**至**上**然後選取**儲存**。

    ![Azure App Service 啟用變更分析使用者介面的螢幕擷取畫面](./media/change-analysis/change-analysis-on.png)

    如果**變更分析**已啟用，您將能夠偵測資源層級變更。 如果**程式碼變更掃描**已啟用，您將也會看到部署檔案和站台設定變更。 啟用**Always on**會最佳化變更掃描的效能，但可能會產生額外的費用，從計費的觀點來看。

5.  一旦啟用的所有項目，選取**診斷並解決問題** > **可用性和效能** > **應用程式當機**可讓您存取變更分析體驗。 圖形將 summerize 發生一段時間，以及詳細資料，這些變更的變更類型：

     ![變更差異檢視的螢幕擷取畫面](./media/change-analysis/change-view.png)

## <a name="troubleshooting"></a>疑難排解

### <a name="unable-to-fetch-change-analysis-information"></a>無法擷取變更分析資訊。

這是暫時性的問題，目前的預覽完成登入。 因應措施是由 web 應用程式上設定隱藏的標籤，然後重新整理頁面所組成：

   ![隱藏變更標記的螢幕擷取畫面](./media/change-analysis/hidden-tag.png)

若要設定隱藏的標記使用 PowerShell:

1. 開啟 Azure Cloud Shell:

    ![變更 Azure Cloud Shell 的螢幕擷取畫面](./media/change-analysis/cloud-shell.png)

2. 殼層類型變更為 PowerShell:

   ![變更 Azure Cloud Shell 的螢幕擷取畫面](./media/change-analysis/choose-powershell.png)

3. 執行以下命令：

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
