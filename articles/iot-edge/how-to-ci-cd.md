---
title: 持續整合與持續部署 - Azure IoT Edge | Microsoft Docs
description: 設定持續整合和持續部署 - Azure IoT Edge 搭配 Azure DevOps、Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a714cec5ce05473887f9f06d47c75563bf878081
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386820"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Azure IoT Edge 的持續整合與持續部署

您可以使用 Azure Pipelines 中內建的 Azure IoT Edge 工作或 Jenkins 伺服器上[適用於 Jenkins 的 Azure IoT Edge 外掛程式](https://plugins.jenkins.io/azure-iot-edge)，輕鬆地採用 DevOps 搭配您的 Azure IoT Edge 應用程式。 此文章示範如何使用 Azure Pipelines 的持續整合與持續部署功能，快速且有效率地建置、測試應用程式，並且將其部署到您的 Azure IoT Edge。 

在本文中，您將了解如何：
* 建立及簽入範例 IoT Edge 解決方案。
* 設定持續整合 (CI)，以建置解決方案。
* 設定持續部署 (CD)，以部署解決方案並檢視回應。

![圖表 - 開發和生產的 CI 和 CD 分支](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>使用 Visual Studio Code 建立範例 Azure IoT Edge 解決方案

在本節中，您會建立包含單元測試的範例 IoT Edge 解決方案，您可以在建置程序中執行該單元測試。 遵循本節中的指引之前，請完成[使用 Visual Studio Code 中的多個模組來開發 IoT Edge 解決方案](how-to-develop-multiple-modules-vscode.md)中的步驟。

1. 在 VS Code 命令選擇區中，輸入並執行命令 **Azure IoT Edge:新增 IoT Edge 解決方案**。 然後選取您的工作區資料夾、提供解決方案名稱 (預設名稱是 **EdgeSolution**)，然後建立 C# 模組 (**FilterModule**) 作為此解決方案中的第一個使用者模組。 您也需要為第一個模組指定 Docker 映像存放庫。 預設的映像存放庫會以本機 Docker 登錄 (`localhost:5000/filtermodule`) 為基礎。 將它變更為 Azure Container Registry(`<your container registry address>/filtermodule`) 或 Docker Hub 以進一步進行持續整合。

    ![設定 Azure Container Registry](./media/how-to-ci-cd/acr.png)

2. VS Code 視窗將載入您的 IoT Edge 方案工作區。 您可以選擇輸入並執行 **Azure IoT Edge:Add IoT Edge module** 以新增更多模組。 根資料夾中有 `modules` 資料夾、`.vscode` 資料夾，以及部署資訊清單範本檔案。 所有使用者模組代碼都將是 `modules` 資料夾下的子資料夾。 `deployment.template.json` 是部署資訊清單範本。 此檔案中的部分參數將會從 `module.json` (存在於每個模組資料夾中) 進行剖析。

3. 現在您的範例 IoT Edge 解決方案已準備就緒。 預設 C# 模組會作為管道訊息模組。 在 `deployment.template.json` 中，您將看到此解決方案包含兩個模組。 訊息將會從 `tempSensor` 模組產生，並將透過 `FilterModule` 直接進行管道傳送，然後傳送至您的 IoT 中樞。

4. 儲存這些專案，然後將其認可到您的 Azure Repos 中。
    
> [!NOTE]
> 如需使用 Azure Repos 的詳細資訊，請參閱[與 Visual Studio 和 Azure Repos 共用程式碼](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)。


## <a name="configure-azure-pipelines-for-continuous-integration"></a>針對持續整合設定 Azure Pipelines
在本節中，您會建立一個組建管線，並將其設定為在您將任何變更簽入範例 IoT Edge 解決方案時自動執行，而且它也會在 Azure Pipelines 中顯示組建記錄。

1. 登入您的 Azure DevOps 組織 (**https://dev.azure.com/{your organization}/**)，然後開啟您簽入範例應用程式所在的專案。

    ![Azure Pipelines 的簽入程式碼](./media/how-to-ci-cd/init-project.png)

1. 在您的 Azure Pipelines 中開啟 [組建] 索引標籤，然後選擇 [+ 新增管線]。 或者，如果您已經有組建管線，請選擇 [+ 新增] 按鈕。 接著，選取 [新增組建管線]。

    ![建立新的組建管線](./media/how-to-ci-cd/add-new-build.png)

1. 如果出現提示，請選取 Azure Repos 做為您的來源。 然後，選取您程式碼所在的專案、存放庫及分支。 選擇 [繼續]。

    ![選取 Azure Repos Git](./media/how-to-ci-cd/select-vsts-git.png)

    在 [選取範本] 視窗中，選擇 [以空白的處理序開始]。

    ![以空白的處理序開始](./media/how-to-ci-cd/start-with-empty.png)

1. 在管線編輯器中，選擇代理程式集區。 
    
    * 如果您想要在 amd64 平台中建置適用於 Linux 容器的模組，請選擇 [裝載的 Ubuntu 1604]
    * 如果您想要在 amd64 平台中建置適用於 Windows 容器的模組，請選擇 [裝載的 VS2017] 
    * 如果您想在 arm32v7 平台中建置適用於 Linux 容器的模組，您必須按一下 [管理] 按鈕來建立自己的組建代理程式。
    
    ![設定組建代理程式集區](./media/how-to-ci-cd/configure-env.png)

1. 在代理程式作業中，按一下 [+]，在組建管線中新增三個工作。 前兩個來自 **Azure IoT Edge**。 而第三個則來自**發行組建成品**
    
    ![將工作新增至組建管線](./media/how-to-ci-cd/add-tasks.png)

1. 在第一個 **Azure IoT Edge** 工作中，將 [顯示名稱] 更新為 **Azure IoT Edge - 建置模組映像**，然後在 [動作] 下拉式清單中，選取 [建置模組映像]。 在 [.template.json 檔案] 控制項中，選取可描述您 IoT Edge 解決方案的 **deployment.template.json** 檔案。 接著，選擇 [預設平台]，確定您選取與 IoT Edge 裝置相同的平台。 此工作將會在解決方案中，使用您指定的目標平台建置所有模組。 此外，也會產生 **deployment.json** 檔案，您可以在 [輸出變數] 中找到檔案路徑。 將此變數的別名設為 `edge`。
    
    ![設定建置模組映像工作](./media/how-to-ci-cd/build-and-push.png)

1. 在第二個 **Azure IoT Edge** 工作中，將 [顯示名稱] 更新為 **Azure IoT Edge - 推送模組映像**，然後在 [動作] 下拉式清單中，選取 [推送模組映像]。 選擇 [容器登錄類型]，確定您設定及選取與程式碼 (module.json) 中相同的登錄。 在 [.template.json 檔案] 控制項中，選取可描述您 IoT Edge 解決方案的 **deployment.template.json** 檔案。 接著，選擇 [預設平台]，確定您為已建置的模組映像選取相同的平台。 此工作會將所有模組映像推送到您選取的容器登錄中。 此外，也在 **deployment.json** 檔案中新增容器登錄認證，您可以在 [輸出變數] 中找到檔案路徑。 將此變數的別名設為 `edge`。 如果您有多個容器登錄可裝載您的模組映像，您需要複製此工作、選取不同的容器登錄，然後使用進階設定中的 [略過模組]，略過不適用於此特定登錄的映像。

    ![設定推送模組映像工作](./media/how-to-ci-cd/push.png)

1. 在 [發行組建成品] 工作中，您要指定建置工作所產生的部署檔案。 將 [要發行的路徑] 設為 `$(edge.DEPLOYMENT_FILE_PATH)`。

    ![設定發佈成品工作](./media/how-to-ci-cd/publish-build-artifacts.png)

1. 開啟 [觸發程序] 索引標籤，然後開啟 [持續整合] 觸發程序。 請確定已納入包含程式碼的分支。

    ![開啟持續整合觸發程序](./media/how-to-ci-cd/configure-trigger.png)

    儲存新的組建管線。 按一下 [儲存]  按鈕。


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>針對持續部署設定 Azure Pipelines
在本節中，您會建立一個組建管線，並將其設定為在您的組建管線置放成品時自動執行，而且它也會在 Azure Pipelines 中顯示部署記錄。

1. 在 [發行] 索引標籤中，選擇 [+ 新增管線]。 或者，如果您已經有組建管線，請選擇 [+ 新增] 按鈕，並按一下 [+ 新增發行管線]。  

    ![新增發行管線](./media/how-to-ci-cd/add-release-pipeline.png)

    在 [選取範本] 視窗中，選擇 [從空白作業開始]。

    ![從空白作業開始](./media/how-to-ci-cd/start-with-empty-job.png)

2. 接著，發行管線會以一個階段初始化：**階段 1**。 將**階段 1** 重新命名為 **QA**，並將它視為測試環境。 在常見的持續部署管線中，它通常存在多個階段，您可以根據您的 DevOps 做法來建立更多階段。

    ![建立測試環境階段](./media/how-to-ci-cd/QA-env.png)

3. 將發行連結到組建成品。 在成品區域中按一下 [新增]。

    ![新增成品](./media/how-to-ci-cd/add-artifacts.png)  
    
    在 [新增成品] 頁面中，選擇來源類型 [組建]。 接著選取您建立的專案和組建管線。 然後按一下 [新增]。

    ![新增組建成品](./media/how-to-ci-cd/add-an-artifact.png)

    開啟持續部署觸發程序，如此每當有新的組建可用時，便會建立新的發行。

    ![設定連續部署觸發程序](./media/how-to-ci-cd/add-a-trigger.png)

4. 瀏覽至 **QA 階段**，並設定此階段中的工作。

    ![設定 QA 工作](./media/how-to-ci-cd/view-stage-tasks.png)

   部署工作是不區分大小寫的平台，這表示您可以選擇 [代理程式集區] 中的 [裝載的 VS2017] 或 [裝載的 Ubuntu 1604] (或您自己管理的任何其他代理程式)。 按一下 [+] 並新增一個工作。

    ![新增 QA 的工作](./media/how-to-ci-cd/add-task-qa.png)

5. 在 Azure IoT Edge 工作中瀏覽至 [動作] 下拉式清單，選取 [部署至 IoT Edge 裝置]。 選取您的 **Azure 訂用帳戶**並且輸入您的 **IoT 中樞名稱**。 您可以選擇要部署到單一或多個裝置。 如果您要部署到**多個裝置**，您必須指定裝置**目標條件**。 目標條件會在 IoT 中樞內篩選出符合的一組 Edge 裝置。 如果您想使用裝置標記作為條件，您必須使用 IoT 中樞裝置對應項更新對應的裝置標記。 將進階設定中的 [IoT Edge 部署識別碼] 更新為 "deploy-qa"。 假設您有數個 IoT Edge 裝置已標記為 'qa'，則工作設定將會如下列螢幕擷取畫面所示。 

    ![部署至 QA](./media/how-to-ci-cd/deploy-to-qa.png)

    儲存新的發行管線。 按一下 [儲存]  按鈕。 然後，按一下 [管線] 以回到管線。

6. 第二個階段是針對生產環境。 若要新增新的階段 "PROD"，您可以複製階段 "QA"，並將複製的階段重新命名為 **PROD**。

    ![複製階段](./media/how-to-ci-cd/clone-stage.png)

7. 針對生產環境設定工作。 假設您有數個 IoT Edge 裝置已標記為 'prod'，在工作設定中，請將 [目標條件] 更新為 "prod"，然後將進階設定中的部署識別碼設為 "deploy-prod"。 按一下 [儲存]  按鈕。 然後，按一下 [管線] 以回到管線。
    
    ![部署至生產環境](./media/how-to-ci-cd/deploy-to-prod.png)

7. 目前，我們的組建成品會在 **QA** 階段上持續觸發，然後進入 **PROD** 階段。 但大多時候您需要整合一些在 QA 裝置上的測試案例，並手動核准那些部分。 稍後，那些部分將會部署到 PROD 環境。 依下列螢幕擷取畫面所示在 PROD 階段中設定核准。

    1. 開啟 [部署前的條件] 設定面板。

        ![開啟部署前的條件](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. 在 [部署前核准] 中設定 [啟用]。 然後填寫 [核准者] 輸入。 然後按一下 [儲存] 。
    
        ![設定條件](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. 現在您的發行管線已設定如下列螢幕擷取畫面所示。

    ![發行管線](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>使用組建和發行管線驗證 IoT Edge CI/CD

在本節中，您將觸發組建來使 CI/CD 管線運作。 接著驗證部署是否成功。

1. 若要觸發組建作業，您可以推送認可至原始程式碼存放庫，或是手動觸發。 您可以按一下 [佇列] 按鈕來觸發組建管線中的組建作業，如下列螢幕擷取畫面所示。

    ![手動觸發](./media/how-to-ci-cd/manual-trigger.png)

2. 如果順利完成組建管線，將會觸發發行至 **QA** 階段。 瀏覽至組建管線記錄，您應該會看到下列螢幕擷取畫面。

    ![組建記錄檔](./media/how-to-ci-cd/build-logs.png)

3. 成功部署到 **QA** 階段將會觸發核准者的通知。 瀏覽至發行管線，您可以看到下列螢幕擷取畫面。 

    ![等待核准](./media/how-to-ci-cd/pending-approval.png)


4. 在核准者核准這項變更之後，它就可以部署到 **PROD**。

    ![部署到 PROD](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>後續步驟

* 在[了解單一裝置或大規模的 IoT Edge 部署](module-deployment-monitoring.md)中，了解 IoT Edge 部署。
* 逐步解說[大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md)中，用來建立、更新或刪除部署的步驟。
