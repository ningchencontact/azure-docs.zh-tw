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
ms.date: 05/31/2019
ms.author: mbullwin
ms.openlocfilehash: 6567d7f2ebaab5bd7b5bc8fb7b5a62970f169161
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476163"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights 中度量圖表上的註解

[計量瀏覽器](../../azure-monitor/app/metrics-explorer.md)圖表上的註解會顯示您在哪裡部署了新的組建，或是其他重要事件。 註解可讓您輕鬆查看變更是否對應用程式的效能有任何影響。 [Azure DevOps Services 建置系統](https://docs.microsoft.com/azure/devops/pipelines/tasks/)可以自動建立這些註解。 您也可以從 PowerShell 建立註解來標記您想要的任何事件。

> [!NOTE]
> 本文反映已被取代的**傳統計量體驗**。 註解目前僅適用於傳統經驗和 **[活頁簿](../../azure-monitor/app/usage-workbooks.md)** 中。 若要深入了解目前的計量體驗，您可以查閱[這篇文章](../../azure-monitor/platform/metrics-charts.md)。

![註解的範例](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>與 Azure DevOps Services 組建搭配的發行註解

發行註解是 Azure DevOps Services 的雲端式 Azure Pipelines 服務的功能。

### <a name="install-the-annotations-extension-one-time"></a>安裝註解擴充功能 (一次)
若要能夠建立發行註解，必須安裝 Visual Studio Marketplace 中的許多可用 Azure DevOps Services 擴充功能之一。

1. 登入您的 [Azure DevOps Services](https://azure.microsoft.com/services/devops/) 專案。
2. 在 Visual Studio Marketplace 中[取得發行註解擴充功能](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)，並將其新增至您的 Azure DevOps Services 組織。

![選取 Azure DevOps 的組織，然後再安裝。](./media/annotations/1-install.png)

您只需要為 Azure DevOps Services 組織執行一次此動作。 現在，您即可為組織中的任何專案設定發行註解。

### <a name="configure-release-annotations"></a>設定發行註解

您必須為每個 Azure DevOps Services 發行範本取得個別的 API 金鑰。

1. 登入[Microsoft Azure 入口網站](https://portal.azure.com)並開啟監視您的應用程式的 Application Insights 資源。 (或如果您尚未建立該資源，請[立即建立一個](../../azure-monitor/app/app-insights-overview.md))。
2. 開啟**API 存取權**索引標籤，並複製**Application Insights 識別碼**。
   
    ![在 portal.azure.com 中，開啟您的 Application Insights 資源然後選擇 [設定]。 開啟 [API 存取]。 複製應用程式識別碼](./media/annotations/2-app-id.png)

4. 在另一個瀏覽器視窗中，開啟 (或建立) 可從 Azure DevOps Services 管理部署的發行範本。
   
    新增工作，然後從功能表中選取 Application Insights 發行註解工作。

   ![按一下加號以新增的工作，然後選取 Application Insights 發行註解。 貼上 Application Insights 識別碼。](./media/annotations/3-add-task.png)

    貼上**應用程式識別碼**，您所複製的 [API 存取] 索引標籤。
   
    ![貼上 Application Insights 識別碼](./media/annotations/4-paste-app-id.png)

5. 回到 Azure 視窗，建立新的「API 金鑰」並複製它。
   
    ![在 Azure 視窗的 API 存取 索引標籤，按一下 建立 API 金鑰。](./media/annotations/5-create-api-key.png)

    ![在 建立 API 金鑰 索引標籤提供註解、 核取寫入註解，並按一下 產生金鑰。 複製新的金鑰。](./media/annotations/6-create-api-key.png)

6. 開啟發行範本的 [設定] 索引標籤。
   
    建立 `ApiKey`的變數定義。
   
    將您的 API 金鑰貼上至 ApiKey 變數定義。
   
    ![在 Azure DevOps 服務 視窗中，選取 變數 索引標籤，然後按一下 新增。 設定名稱 apikey，並在 值，貼上您產生的金鑰，然後按一下鎖定圖示。](./media/annotations/7-paste-api-key.png)
1. 最後，**儲存**發行管線。


## <a name="view-annotations"></a>檢視註解
現在，每當您使用發行範本來部署新的發行，就會將註解傳送至 Application Insights。 註解將會出現在計量瀏覽器的圖表上。

按一下以開啟詳細資料的任何註解標記 （淺灰色箭頭） 有關的版本，包括要求者、 原始檔控制分支，發行管線、 環境及更多資訊。

![按一下任一版本註解標記。](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>從 PowerShell 建立自訂註解
您也可以從任何您慣用的程序建立註解 (不使用 Azure DevOps Services)。 


1. 建立一個[來自 GitHub 的 Powershell 指令碼](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)的本機複本。

2. 取得應用程式識別碼，並從 [API 存取] 索引標籤中建立 API 金鑰。

3. 依下列方式呼叫指令碼：

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

修改指令碼很簡單，例如，修改成建立過去的註解。

## <a name="next-steps"></a>後續步驟

* [建立工作項目](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [使用 PowerShell 進行自動化](../../azure-monitor/app/powershell.md)
