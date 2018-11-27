---
title: 教學課程：使用 Azure DevOps Projects 為現有的程式碼建立 CI/CD 管線
description: Azure DevOps Projects 可讓您輕鬆地開始使用 Azure。 DevOps Projects 可協助您透過幾個簡單的步驟，使用自己的程式碼和 GitHub 存放庫，在您選擇的 Azure 服務上啟動應用程式。
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 88ee15a3b5cc53542d9e098dee485b8a526bb9a6
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161747"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>教學課程：使用 Azure DevOps Projects 為現有的程式碼建立 CI/CD 管線

Azure DevOps Projects 提供簡化的體驗，讓您可以自備現有程式碼和 Git 存放庫，或者選擇一個範例應用程式，在 Azure 中建立持續整合 (CI) 和持續傳遞 (CD) 管線。

您將：

> [!div class="checklist"]
> * 使用 DevOps Projects 建立 CI/CD 管線
> * 設定對 GitHub 存放庫的存取並選擇架構
> * 設定 Azure DevOps 與 Azure 訂用帳戶 
> * 將變更認可至 GitHub 並自動部署至 Azure
> * 檢查 Azure Pipelines 的 CI/CD 管線
> * 設定 Azure Application Insights 監視
> * 清除資源

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 您可以透過 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 取得免費的訂用帳戶。
* 可存取其中包含 .NET、Java、PHP、Node、Python 或靜態 Web 程式碼的 GitHub 或外部 Git 存放庫。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

Azure DevOps Projects 會在 Azure Pipelines 中建立 CI/CD 管線。 您可以建立新的 Azure DevOps 組織或使用現有組織。 Azure DevOps Projects 也會在您選擇的 Azure 訂用帳戶中建立 Azure 資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左側窗格中，選取 [新增]。

1. 在 [搜尋] 方塊中，輸入 **DevOps Projects**，然後選取 [建立]。

    ![DevOps Projects 儀表板](_img/azure-devops-project-github/fullbrowser.png)

1. 選取 [自備程式碼]，然後選取 [下一步]。

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>設定對 GitHub 存放庫的存取並選擇架構

1. 選取 [GitHub] 或外部 Git 存放庫，然後選取您的存放庫和包含應用程式的分支。

1. 選取您的 Web 架構，然後選取 [下一步]。

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

    您先前選擇的應用程式架構，會指出這裡可用的 Azure 服務部署目標類型。 
    
1. 選取目標服務，然後選取 [下一步]。

## <a name="configure-azure-devops-and-an-azure-subscription"></a>設定 Azure DevOps 與 Azure 訂用帳戶 

1. 建立新的 Azure DevOps 組織或選取現有組織。

    a. 為您在 Azure DevOps 中的專案輸入名稱。 
    
    b. 選取 Azure 訂用帳戶和位置、輸入應用程式名稱，然後選取 [完成]。

    在幾分鐘後，Azure 入口網站中便會顯示 DevOps Projects 儀表板。 系統會在您 Azure DevOps 組織的存放庫中設定範例應用程式、執行建置，然後將您的應用程式部署到 Azure。 此儀表板可顯示您的 GitHub 程式碼存放庫、CI/CD 管線，和您在 Azure 中的應用程式。 
    
1. 選取 [瀏覽]以檢視執行中應用程式。

    ![DevOps Projects 儀表板檢視](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps Projects 會自動設定 CI 建置和發行觸發程序。 您的程式碼會保留在 GitHub 存放庫或其他外部存放庫中。 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>將變更認可至 GitHub 並自動部署至 Azure 

您現在已準備好要使用 CI/CD 程序來與小組共同對應用程式執行作業，並將您最新的工作自動部署至網站。 對 GitHub 存放庫進行的每個變更都會在 Azure DevOps 中起始一個建置，且 CD 管線會對 Azure 執行部署。

1. 變更您的應用程式，並將變更認可至 GitHub 存放庫。  
    不久後，Azure Pipelines 中就會起始一個建置。 您可以在 DevOps Projects 儀表板監視建置狀態，或使用 Azure DevOps 組織在瀏覽器中監視建置狀態。

1. 建置完成之後，請重新整理您的應用程式，以確認變更。

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>檢查 Azure Pipelines 的 CI/CD 管線

Azure DevOps Projects 會在 Azure Pipelines 中自動設定 CI/CD 管線。 瀏覽管線，並視需要進行自訂。 若要讓自己熟悉建置和發行管線，請執行下列作業：

1. 在 DevOps Projects 儀表板頂端選取 [建置管線]。  
    瀏覽器索引標籤會顯示新專案的建置管線。

1. 指向 [狀態] 欄位，然後選取省略符號 (...)。  
    功能表會顯示數個選項，例如將新建置排入佇列、暫停建置和編輯建置管線。

1. 選取 [編輯]。

1. 在此窗格中，您可以檢查建置管線的各種工作。  
    建置會執行各種工作，例如從 Git 存放庫擷取來源、還原相依性，以及發佈用來進行部署的輸出。

1. 在建置管線的頂端，選取建置管線名稱。

1. 將建置管線的名稱變更成較具描述性的名稱，並選取 [儲存並排入佇列]，然後選取 [儲存]。

1. 在建置管線名稱下，選取 [記錄]。  
    您會看到組建最近變更的稽核線索。 Azure DevOps 會追蹤對建置管線進行的任何變更，且可讓您比較版本。

1. 選取 [觸發程序]。  
    Azure DevOps Projects 會自動建立 CI 觸發程序，且每次對存放庫的認可都會啟動新的建置。 您可以選擇性地選擇要在 CI 程序中包含還是排除分支。

1. 選取 [保留期]。  
        根據案例，您可以指定原則來保留或移除特定數目的組建。

1. 選取 [建置及發行]，然後選取 [版本]。  
    Azure DevOps Projects 會建立發行管線來管理 Azure 的部署。

1. 選取發行管線旁邊的省略符號 (...)，然後選取 [編輯]。  
    發行管線中包含 [管線]，它會定義發行程序。 
    
1. 在 [成品] 下，選取 [置放]。  
    您在先前步驟中檢查的建置管線會產生用於成品的輸出。 

1. 在 [置放] 圖示旁邊，選取 [持續部署觸發程序]。  
    這個發行管線已啟用 CD 觸發程序，每次有新的建置成品可用時，它就會執行部署。 您可以選擇性地停用觸發程序，因此需要手動執行部署。 

1. 從左側選取 [工作]。  
    工作是您部署程序所執行的活動。 在此範例中，會建立一個工作以部署到 Azure App 服務。

1. 從右側選取 [檢視版本]，以顯示版本的歷程記錄。

1. 選取發行旁邊的省略符號 (...)，然後選取 [開啟]。  
    您可以瀏覽數個功能表，例如版本摘要、相關聯的工作項目及測試。

1. 選取 [認可]。  
    此檢視會顯示與此部署相關聯的程式碼認可。 

1. 選取 [記錄]。  
    記錄包含關於部署程序的實用資訊。 您可以在部署期間和部署之後加以檢視。

## <a name="configure-azure-application-insights-monitoring"></a>設定 Azure Application Insights 監視

Azure Application Insights 可讓您輕鬆監視應用程式的效能和使用情形。 Azure DevOps Projects 會自動為您的應用程式設定 Application Insights 資源。 您可以視需要進一步設定各種警示和監視功能。

1. 在 Azure 入口網站中，移至 DevOps Projects 儀表板。 

1. 從右下角選取應用程式的 **Application Insights** 連結。  
    **Application Insights** 窗格會隨即開啟。 此檢視包含您應用程式的使用情形、效能和可用性監視資訊。

    ![Application Insights 窗格](_img/azure-devops-project-github/appinsights.png) 

1. 選取 [時間範圍]，然後選取 [過去一小時]。 選取 [更新] 以篩選結果。  
    此時您可以看到過去 60 分鐘內的所有活動。 若要結束此時間範圍，請選取 [x]。

1. 選取 [警示]，然後選取 [新增計量警示]。 

1. 輸入警示的名稱。

1. 在 [來源變更位置] 下拉式清單中，選取您的 **App Service 資源。** <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. 在 [計量] 下拉式清單中，檢查各種警示計量。  
    預設警示是**伺服器回應時間大於 1 秒**的警示。 您可以輕鬆地設定各種警示，以改善應用程式的監視功能。

1. 選取 [透過電子郵件擁有者、參與者和讀取者進行通知] 核取方塊。  
    您也可以選擇性地執行 Azure 邏輯應用程式，以在警示顯示時執行其他動作。

1. 選取 [確定] 可建立警示。  
    不久之後，警示就會在儀表板上呈現為作用中狀態。
    
1. 退出 [警示] 區域，並返回 [Application Insights] 窗格。

1. 選取 [可用性]，然後選取 [新增測試]。 

1. 輸入測試名稱，然後選取 [建立]。  
    此時會建立簡單的 Ping 測試，以確認應用程式的可用性。 幾分鐘後即會產生測試結果，且 Application Insights 儀表板會顯示可用性狀態。

## <a name="clean-up-resources"></a>清除資源

如果不再需要，您可以刪除在此教學課程中建立的 Azure App 服務和相關資源。 若要這樣做，請使用 DevOps Projects 儀表板上的 [刪除] 功能。

## <a name="next-steps"></a>後續步驟

當您在此教學課程中設定 CI/CD 程序時，系統會自動在 Azure DevOps Projects 中建立建置和發行管線。 您可以修改這些建置和發行管線，以符合小組的需求。 您已了解如何︰

> [!div class="checklist"]
> * 使用 DevOps Projects 建立 CI/CD 管線
> * 設定對 GitHub 存放庫的存取並選擇架構
> * 設定 Azure DevOps 與 Azure 訂用帳戶 
> * 將變更認可至 GitHub 並自動部署至 Azure
> * 檢查 Azure Pipelines 的 CI/CD 管線
> * 設定 Azure Application Insights 監視
> * 清除資源

若要深入了解 CI/CD 管線，請參閱：

> [!div class="nextstepaction"]
> [定義多階段的持續部署 (CD) 管線](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
