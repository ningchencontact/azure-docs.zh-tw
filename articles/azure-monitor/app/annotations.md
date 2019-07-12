---
title: Application Insights 的發行註解 | Microsoft Docs
description: 在 Application Insights 中對計量瀏覽器新增部署或建置標記。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mbullwin
ms.openlocfilehash: e3ec202ba6126b150fb78c76591682f163018661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604551"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights 中度量圖表上的註解

註釋[計量瀏覽器](../../azure-monitor/app/metrics-explorer.md)圖表會顯示您可在此部署新的組建或其他重要事件。 註解，讓您輕鬆查看您的變更是否有任何對您的應用程式效能的影響。 它們可以自動建立所[Azure 管線](https://docs.microsoft.com/azure/devops/pipelines/tasks/)建置系統。 您也可以從 PowerShell 建立註解來標記您想要的任何事件。

> [!NOTE]
> 本文反映已被取代的**傳統計量體驗**。 註解目前僅適用於傳統經驗和 **[活頁簿](../../azure-monitor/app/usage-workbooks.md)** 中。 若要深入了解目前的計量體驗，請參閱[進階功能的 Azure 計量瀏覽器](../../azure-monitor/platform/metrics-charts.md)。

![註解的範例](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>版本註解與 Azure 管線組建

發行註解是 Azure DevOps 的雲端式 Azure 管線服務的功能。

### <a name="install-the-annotations-extension-one-time"></a>安裝註解擴充功能 (一次)
若要能夠建立發行註解，您必須安裝其中一個可用的許多 Azure DevOps 」 擴充功能在 Visual Studio Marketplace 中。

1. 登入您[Azure DevOps](https://azure.microsoft.com/services/devops/)專案。
   
1. 在 Visual Studio Marketplace[版本註解擴充功能](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)頁面上，選取您 Azure DevOps 的組織，然後選取**安裝**將擴充功能新增至您 Azure DevOps 的組織。
   
   ![選取 Azure DevOps 的組織，然後選取 安裝。](./media/annotations/1-install.png)
   
您只需要安裝一次為您的 Azure DevOps 組織擴充功能。 您現在可以在組織中設定發行註解的任何專案。

### <a name="configure-release-annotations"></a>設定發行註解

針對每個您 Azure 管線發行範本建立個別的 API 金鑰。

1. 登入[Azure 入口網站](https://portal.azure.com)並開啟監視您的應用程式的 Application Insights 資源。 如果您沒有帳戶，或者[建立新的 Application Insights 資源](../../azure-monitor/app/app-insights-overview.md)。
   
1. 開啟**API 存取權**索引標籤，並複製**Application Insights 識別碼**。
   
   ![在 API 存取，複製 應用程式識別碼。](./media/annotations/2-app-id.png)

1. 在另一個瀏覽器視窗中，開啟或建立發行範本，可管理您的 Azure 管線部署。
   
1. 選取 **新增工作**，然後選取**Application Insights 發行註解**從功能表的工作。
   
   ![選取新增的工作，然後選取 Application Insights 發行註解。](./media/annotations/3-add-task.png)
   
1. 底下**應用程式識別碼**、 貼上您複製從 Application Insights 識別碼**API 存取** 索引標籤。
   
   ![貼上 Application Insights 識別碼](./media/annotations/4-paste-app-id.png)
   
1. 在 Application Insights **API 存取權**視窗中，選取**建立 API 金鑰**。 
   
   ![在 API 存取 索引標籤中，選取 建立 API 金鑰。](./media/annotations/5-create-api-key.png)
   
1. 在 [**建立 API 金鑰**] 視窗中，輸入描述，選取**寫入註釋**，然後選取**產生金鑰**。 複製新的金鑰。
   
   ![在 [建立 API 金鑰] 視窗中，輸入描述選取寫入註釋]，然後選取 [產生金鑰。](./media/annotations/6-create-api-key.png)
   
1. 在 [發行範本] 視窗中，在**變數**索引標籤上，選取**新增**來建立新的 API 金鑰的變數定義。

1. 底下**名稱**，輸入`ApiKey`，然後在**值**，貼上您從複製的 API 金鑰**API 存取** 索引標籤。
   
   ![在 Azure DevOps 變數 索引標籤中，選取 新增 名稱變數 ApiKey，並將值之下的 API 金鑰。](./media/annotations/7-paste-api-key.png)
   
1. 選取 [**儲存**儲存範本的主要發行範本] 視窗中。

## <a name="view-annotations"></a>檢視註解
現在，每當您使用發行範本來部署新的版本中，註解傳送至 Application Insights。 在圖表上顯示的註釋**計量瀏覽器**。

選取任何的註解標記 （淺灰色箭頭），即可開啟版本，包括要求者、 原始檔控制分支、 發行管線，與環境相關詳細資料。

![選取版本註解標記。](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>從 PowerShell 建立自訂註解
您可以使用[CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)從 GitHub，以從任何您喜歡，而不需使用 Azure DevOps 的處理程序建立註解的 PowerShell 指令碼。 

1. 進行的本機副本[CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)。
   
1. 使用上述程序中的步驟，以取得您的 Application Insights 識別碼，並從您的 Application Insights 中建立 API 金鑰**API 存取** 索引標籤。
   
1. 呼叫 PowerShell 指令碼，以角括弧括住的預留位置值取代為下列程式碼。 `-releaseProperties`是選擇性的。 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

您可以修改指令碼，例如若要建立過去的註解。

## <a name="next-steps"></a>後續步驟

* [建立工作項目](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [使用 PowerShell 進行自動化](../../azure-monitor/app/powershell.md)
