---
title: 教學課程：使用 Azure DevOps Projects 為現有的程式碼建立 CI/CD 管線
description: Azure DevOps Projects 可讓您輕鬆地開始使用 Azure。 DevOps Projects 可協助您透過幾個簡單的步驟，使用自己的程式碼和 GitHub 存放庫，在您選擇的 Azure 服務上啟動應用程式。
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 4a48b5c413f6dfb58a1ba3f896ded49250cde306
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286272"
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
> * 清除資源

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 您可以透過 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 取得免費的訂用帳戶。
* 可存取其中包含 .NET、Java、PHP、Node、Python 或靜態 Web 程式碼的 GitHub 或外部 Git 存放庫。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

Azure DevOps Projects 會在 Azure Pipelines 中建立 CI/CD 管線。 您可以建立新的 Azure DevOps 組織或使用現有組織。 Azure DevOps Projects 也會在您選擇的 Azure 訂用帳戶中建立 Azure 資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側窗格中，選取左側瀏覽列中的 [+建立資源]  ，搜尋 [DevOps Projects]  ，然後選取 [建立]  。

   ![DevOps Projects 儀表板](_img/azure-devops-project-github/azuredashboard.png)

3. 選取 [自備程式碼]  ，然後選取 [下一步]  。

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>設定對 GitHub 存放庫的存取並選擇架構

1. 選取 **GitHub** 或外部 **Git** 程式碼存放庫。 針對本教學課程，請選取 [GitHub]  。 第一次設定時，您可能需要先向 GitHub 進行驗證，以允許 Azure 存取 GitHub 存放庫。

2. 完成選取 [存放庫]  及 [分支]  後，請選取 [下一步]  。

3. 如果您使用 Docker 容器，請將 [應用程式是否 Docker 化]  變更為 [是]  ，但在本教學課程中，請選取 [否]  ，然後選取 [下一步]  。 如需有關使用 Docker 容器的詳細資訊，請將滑鼠停留在 [i]  圖示上。

   ![.NET Framework](_img/azure-devops-project-github/appframework.png)

4. 從下拉式清單中選取**應用程式執行階段**和**架構**，然後選取 [下一步]  。 您選擇的應用程式架構會指出可用的 Azure 服務部署目標類型。

5. 選取 **Azure 服務**以部署應用程式，然後選取 [下一步]  。

## <a name="configure-azure-devops-and-an-azure-subscription"></a>設定 Azure DevOps 與 Azure 訂用帳戶

1. 輸入**專案名稱**。

2. 免費建立新的 **Azure DevOps 組織**或從下拉式清單中選擇現有組織。

3. 選取 **Azure 訂用帳戶**，輸入 **Web 應用程式**名稱或採用預設值。 選取**位置**，然後選取 [完成]  。 在幾分鐘後，Azure 入口網站中便會顯示 DevOps Projects 部署概觀。

4. 選取 [移至資源]  以檢視 DevOps 專案儀表板。 在右上角將 [專案]  釘選至儀表板以供快速存取。 Azure DevOps Projects 會自動設定 CI 建置和發行觸發程序。 您的程式碼會保留在 GitHub 存放庫或其他外部存放庫中。 範例應用程式已在 **Azure DevOps 組織**的存放庫中進行設定。 組建會執行，且您的應用程式會部署至 Azure。

   ![DevOps Projects 儀表板檢視](_img/azure-devops-project-github/projectsdashboard.png)

5. 儀表板可顯示您的程式碼存放庫、您的 CI/CD 管線，和您在 Azure 中的應用程式。 從右側 Azure 資源下方選取 [瀏覽]  以檢視執行中應用程式。

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>將變更認可至 GitHub 並自動部署至 Azure

您現在已準備好要使用 CI/CD 程序來與小組共同對應用程式執行作業，並將您最新的工作自動部署至網站。 對 GitHub 存放庫進行的每個變更都會在 Azure DevOps 中起始一個建置，且 CD 管線會對 Azure 執行部署。

1. 從您的 DevOps 專案儀表板中選取 [存放庫]  。 您的 GitHub 存放庫會在新的瀏覽器索引標籤中開啟。變更您的應用程式，然後按一下 [認可變更]  。

2. 不久後，Azure Pipelines 中就會起始一個建置。 您可以在 DevOps Projects 儀表板監視建置狀態，或藉由從專案儀表板中選擇 [建置管線]  ，在 Azure DevOps 組織中監視建置狀態。

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>檢查 Azure Pipelines 的 CI/CD 管線

Azure DevOps Projects 會在 Azure Pipelines 中自動設定 CI/CD 管線。 瀏覽管線，並視需要進行自訂。 若要讓自己熟悉建置和發行管線，請執行下列作業：

1. 在 DevOps Projects 儀表板中選取 [建置管線]  。

2. 當您的 [Azure 管線]  頁面開啟時，您會看到最新建置的記錄，以及每個建置的狀態。

   ![Azure DevOps 管線建置](_img/azure-devops-project-github/pipelinesbuildpage.png)

3. 在 [建置]  頁面的右上角，您會看到用於 [編輯]  目前建置的選項、用於將新建置排入佇列的 [佇列]  選項，以及可開啟功能表來使用更多選項的省略符號 ( **&#8942;** )，請選取 [編輯]  。

4. 建置會執行各種工作，例如從存放庫擷取來源、還原相依性，以及發佈部署的輸出。 在右側的 [名稱]  底下，將建置管線名稱變更為更具描述性的名稱。 選取 [儲存並排入佇列]  ，接著選取 [儲存]  並保留註解，然後再次選取 [儲存]  。

   ![Azure DevOps 建置頁面](_img/azure-devops-project-github/buildpage.png)

5. 若要查看近期建置變更的稽核線索，請選取 [記錄]  索引標籤。Azure DevOps 會追蹤建置管線上的任何變更，且可讓您比較版本。

6. 選取 [觸發程序]  索引標籤。Azure DevOps Project 會自動以一些預設設定建立 CI 觸發程序。 您可以將**啟用連續整合**之類的觸發程序設定為在每次認可程式碼變更時執行建置，或排定特定時間來執行建置。

## <a name="clean-up-resources"></a>清除資源

如果不再需要，您可以刪除在此教學課程中建立的 Azure App 服務和相關資源。 若要這樣做，請使用 DevOps Projects 儀表板上的 [刪除]  功能。

## <a name="next-steps"></a>後續步驟

當您在此教學課程中設定 CI/CD 程序時，系統會自動在 Azure DevOps Projects 中建立建置和發行管線。 您可以修改這些建置和發行管線，以符合小組的需求。 您已了解如何︰

> [!div class="checklist"]
>  * 使用 DevOps Projects 建立 CI/CD 管線
> * 設定對 GitHub 存放庫的存取並選擇架構
> * 設定 Azure DevOps 與 Azure 訂用帳戶
> * 將變更認可至 GitHub 並自動部署至 Azure
> * 檢查 Azure Pipelines 的 CI/CD 管線
> * 清除資源

若要深入了解 CI/CD 管線，請參閱：

> [!div class="nextstepaction"]
> [定義多階段的持續部署 (CD) 管線](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

若要深入了解應用程式監視，請參閱：
  
 > [!div class="nextstepaction"]
 > [Azure 監視器是什麼](https://docs.microsoft.com/azure/azure-monitor/overview)
