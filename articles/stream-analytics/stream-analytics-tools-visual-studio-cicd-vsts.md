---
title: 使用 VSTS 教學課程部署具有 CI/CD 的 Azure 串流分析作業
description: 本文說明如何使用 VSTS 部署具有 CI/CD 的串流分析作業。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: d4f1e188a1a145ba3be5fb45d2b0ea4d0bfd57a7
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2018
ms.locfileid: "41918186"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-vsts"></a>教學課程：使用 VSTS 部署具有 CI/CD 的 Azure 串流分析作業
本教學課程說明如何使用 Visual Studio Team Services (VSTS) 設定 Azure 串流分析作業的持續整合和部署。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 將原始檔控制新增至專案
> * 在 Team Services 中建立組建定義
> * 在 Team Services 中建立發行定義
> * 自動部署和升級應用程式

## <a name="prerequisites"></a>必要條件
開始之前，請確定您具有下列項目：

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 安裝 [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 和 **Azure 開發**或**資料儲存和處理**工作負載。
* 建立 [Visual Studio 中的串流分析專案](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs)。
* 建立 [Visual Studio Team Services](https://visualstudio.microsoft.com/team-services/) 帳戶。

## <a name="configure-nuget-package-dependency"></a>設定 NuGet 套件相依性
若要在任意電腦上自動建置和自動部署，您必須使用 NuGet 套件 `Microsoft.Azure.StreamAnalytics.CICD`。 它提供的本機執行與部署工具 MSBuild，可針對串流分析 Visual Studio 專案，支援連續整合及部署程序。 如需詳細資訊，請參閱[串流分析 CI/CD 工具](stream-analytics-tools-for-visual-studio-cicd.md)。

將 **packages.config** 新增到您的專案目錄中。

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>向新的 Team Services Git 儲存機制共用 Visual Studio 解決方案
向 Team Services 中的小組專案共用應用程式原始檔，以便產生組建。  

1. 在 Visual Studio 右下角的狀態列上，依序選取 [新增至原始檔控制]、[Git]，建立專案的新本機 Git 存放庫。 

2. 在 [Team Explorer] 的 [同步處理] 檢視中，選取 [推送至 Visual Studio Team Services] 下的 [發佈 Git 存放庫] 按鈕。

   ![推送 Git 存放庫](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. 確認您的電子郵件，並在 [Team Services 網域] 下拉式清單選取您的帳戶。 輸入您的儲存機制名稱，並選取 [發佈儲存機制]。

   ![推送 Git 儲存機制](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    發佈儲存機制將在您的帳戶中建立與本機儲存機制名稱相同的新 Team 專案。 若要在現有的 Team 專案中建立存放庫，請按一下**存放庫名稱**旁邊的 [進階]，並選取 Team 專案。 您可以選取 [在網路上檢視]，在瀏覽器上檢視您的程式碼。
 
## <a name="configure-continuous-delivery-with-vsts"></a>設定 VSTS 的持續傳遞
Team Services 組建定義描述了由循序執行組建步驟所組成的工作流程。 深入了解 [Team Services 組建定義](https://www.visualstudio.com/docs/build/define/create)。 

Team Services 發行定義描述將應用程式封裝部署到叢集的工作流程。 一起使用時，組建定義和發行定義可以執行整個工作流程；從來源檔案開始，並以叢集中的執行中應用程式結束。 深入了解 Team Services [發行定義](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)。

### <a name="create-a-build-definition"></a>建立組建定義
請開啟網頁瀏覽器，並瀏覽至您剛剛在 [Visual Studio Team Services](https://app.vsaex.visualstudio.com/) 中建立的 Team 專案。 

1. 在 [組建與版本] 索引標籤底下，選取 [組建]，然後選取 [+新增]。  選取 [VSTS Git]，然後選取 [繼續]。
    
    ![選取來源](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. 在 [選取範本] 中，按一下 [空白流程] 從空白定義開始。
    
    ![選擇組建範本](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. 在 [觸發程序] 下方，透過核取 [啟用持續整合] 觸發程序狀態來啟用持續整合。  選取 [儲存並加入佇列] 以手動啟動組建。 
    
    ![觸發程序狀態](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. 推送或簽入時也會觸發組建。 若要檢查組建進度，請切換到 [組建] 索引標籤。一旦確認組建執行成功，您必須定義將應用程式部署至叢集的發行定義。 以滑鼠右鍵按一下組建定義旁邊的省略符號，然後選取 [編輯]。

5.  在 [工作] 中，輸入 "Hosted" 作為**代理程式佇列**。
    
    ![選取代理程式佇列](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. 在 [第 1 階段] 中，按一下 [+]，然後新增 **NuGet** 工作。
    
    ![新增 NuGet 工作](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. 展開 [進階]，然後將 `$(Build.SourcesDirectory)\packages` 新增至**目的地目錄**中。 保留其餘的預設 NuGet 組態值。

   ![設定 NuGet 工作](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. 在 [第 1 階段] 中，按一下 [+]，然後新增 **MSBuild** 工作。

   ![新增 MSBuild 工作](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. 將 **MSBuild 引數**變更如下：

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![設定 MSBuild 工作](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. 在 [第 1 階段] 中，按一下 [+]，然後新增 [Azure 資源群組部署] 工作。 
    
    ![新增 [Azure 資源群組部署] 工作](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. 展開 [Azure 詳細資料] 並以下列值填入組態：
    
    |**設定**  |**建議的值**  |
    |---------|---------|
    |訂用帳戶  |  選擇您的訂用帳戶。   |
    |動作  |  建立或更新資源群組   |
    |資源群組  |  輸入資源群組名稱。   |
    |範本  | [您的解決方案路徑]\bin\Debug\Deploy\\[您的專案名稱].JobTemplate.json   |
    |範本參數  | [您的解決方案路徑]\bin\Debug\Deploy\\[您的專案名稱].JobTemplate.parameters.json   |
    |覆寫範本參數  | 鍵入要在文字方塊中覆寫的範本參數。 例如，–storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre)。 這個屬性是選擇性的，但是如果不覆寫金鑰參數，則您的組建將會導致錯誤。    |
    
    ![設定屬性](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. 按一下 [儲存並排入佇列] 以測試組建定義。
    
    ![設定覆寫參數](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>失敗的建置流程
如果您未在組建定義的 **Azure 資源群組部署**工作中覆寫範本參數，則您會收到 Null 部署參數的錯誤訊息。 請返回組建定義，並覆寫 Null 參數以解決此錯誤。

   ![建置流程失敗](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>認可並推送變更以觸發發行程序
將某些程式碼變更簽入到 Team Services，以確認持續整合管線正常運作。    

您撰寫程式碼時，Visual Studio 會自動追蹤您的變更。 藉由從右下方的狀態列選取暫止變更圖示，認可本機 Git 存放庫的變更。

1. 在 Team Explorer 的**變更**檢視中，加入描述更新的訊息，並認可變更。

    ![認可並推送變更](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. 選取未發行的變更狀態列圖示或 Team Explorer 中的 [同步] 檢視。 選取 [推送] 更新 Team Services/TFS 中的程式碼。

    ![認可並推送變更](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

自動將變更推送至 Team Services 觸發組建。  組建定義成功完成時，版本就會自動建立，並開始更新叢集上的作業。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、串流作業和所有相關資源。 刪除作業可避免因為作業使用串流單位而產生費用。 如果您計劃在未來使用該作業，您可以將其停止並在之後需要時重新啟動。 如果您不再繼續使用此作業，請使用下列步驟，刪除本教學課程所建立的所有資源：

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。  
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

若要深入了解使用適用於 Visual Studio 的 Azure 串流分析工具，以設定持續整合和部署程序，請繼續前往設定 CI/CD 管線的文章：

> [!div class="nextstepaction"]
> [使用串流分析工具持續進行整合及開發](stream-analytics-tools-for-visual-studio-cicd.md)