---
title: Azure IoT Edge 持續整合與持續部署 | Microsoft Docs
description: Azure IoT Edge 的持續整合與持續部署概觀
author: shizn
manager: ''
ms.author: xshi
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 06dec64a55aaece4cd67ebf0485e34aa206a8936
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633728"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Azure IoT Edge 的持續整合與持續部署

您可以使用[適用於 Azure Pipelines 的 Azure IoT Edge ](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) 或[適用於 Jenkins 的 Azure IoT Edge 外掛程式](https://plugins.jenkins.io/azure-iot-edge)，輕鬆地採用 DevOps 搭配您的 Azure IoT Edge 應用程式。 本文示範如何使用 Azure Pipelines 和 Microsoft Team Foundation Server (TFS) 的持續整合與持續部署功能，快速且有效率地建置、測試應用程式，並且將其部署到您的 Azure IoT Edge。 

在本文中，您將了解如何：
* 建立及簽入範例 IoT Edge 解決方案。
* 為您的 Azure DevOps 安裝 Azure IoT Edge 擴充功能。
* 設定持續整合 (CI)，以建置解決方案。
* 設定持續部署 (CD)，以部署解決方案並檢視回應。

完成本文中的步驟需要 30 分鐘的時間。

![CI 和 CD](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>使用 Visual Studio Code 建立範例 Azure IoT Edge 解決方案

在本節中，您會建立包含單元測試的範例 IoT Edge 解決方案，您可以在建置程序中執行該單元測試。 遵循本節中的指引之前，請完成[使用 Visual Studio Code 中的多個模組來開發 IoT Edge 解決方案](tutorial-multiple-modules-in-vscode.md)中的步驟。

1. 在 VS Code 命令選擇區中，輸入並執行命令 **Azure IoT Edge: New IoT Edge solution**。 然後選取您的工作區資料夾、提供解決方案名稱 (預設名稱是 **EdgeSolution**)，然後建立 C# 模組 (**FilterModule**) 作為此解決方案中的第一個使用者模組。 您也需要為第一個模組指定 Docker 映像存放庫。 預設的映像存放庫會以本機 Docker 登錄 (`localhost:5000/filtermodule`) 為基礎。 您必須將它變更為 Azure Container Registry(`<your container registry address>/filtermodule`) 或 Docker Hub 以進一步進行持續整合。

    ![設定 ACR](./media/how-to-ci-cd/acr.png)

2. VS Code 視窗將載入您的 IoT Edge 方案工作區。 您可以選擇性地輸入及執行 **Azure IoT Edge: Add IoT Edge module** 以新增更多模組。 根資料夾中有 `modules` 資料夾、`.vscode` 資料夾，以及部署資訊清單範本檔案。 所有使用者模組代碼都將是 `modules` 資料夾下的子資料夾。 `deployment.template.json` 是部署資訊清單範本。 此檔案中的部分參數將會從 `module.json` (存在於每個模組資料夾中) 進行剖析。

3. 現在您的範例 IoT Edge 解決方案已準備就緒。 預設 C# 模組會作為管道訊息模組。 在 `deployment.template.json` 中，您將看到此解決方案包含兩個模組。 訊息將會從 `tempSensor` 模組產生，並將透過 `FilterModule` 直接進行管道傳送，然後傳送至您的 IoT 中樞。

4. 儲存這些專案，然後將其簽入您的 Azure Repos 或 TFS 存放庫中。
    
> [!NOTE]
> 如需使用 Azure Repos 的詳細資訊，請參閱[與 Visual Studio 和 Azure Repos 共用程式碼](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)。


## <a name="configure-azure-pipelines-for-continuous-integration"></a>針對持續整合設定 Azure Pipelines
在本節中，您會建立一個組建管線，並將其設定為在您將任何變更簽入範例 IoT Edge 解決方案時自動執行，而且它也會在 Azure Pipelines 中顯示組建記錄。

1. 登入您的 Azure DevOps 組織 (**https://dev.azure.com/{your organization}/**)，然後開啟您簽入範例應用程式所在的專案。

    ![簽入程式碼](./media/how-to-ci-cd/init-project.png)

1. 請造訪 Azure DevOps Marketplace 上的[適用於 Azure Pipelines 的 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) \(英文\)。 按一下 [免費取得]，並依照精靈指示將此擴充功能安裝至您的 Azure DevOps 組織或下載到您的 TFS。

    ![安裝擴充功能](./media/how-to-ci-cd/install-extension.png)

1. 在您的 Azure Pipelines 中開啟 [組建與發行] 中樞，然後在 [組建] 索引標籤上選擇 [+ 新增管線]。 或者，如果您已經有組建管線，請選擇 [+ 新增] 按鈕。

    ![新增管線](./media/how-to-ci-cd/add-new-build.png)

1. 如果出現提示，請選取 [Git] 來源類型。 然後，選取您程式碼所在的專案、存放庫及分支。 選擇 [繼續]。

    ![選取 Git](./media/how-to-ci-cd/select-vsts-git.png)

    在 [選取範本] 視窗中，選擇 [以空白的處理序開始]。

    ![選取範本](./media/how-to-ci-cd/start-with-empty.png)

1. 在管線編輯器中，選擇代理程式集區。 
    
    * 如果您想要在 amd64 平台中建置適用於 Linux 容器的模組，請選擇 [裝載的 Ubuntu 1604]
    * 如果您想要在 amd64 平台中建置適用於 Windows 容器的模組，請選擇 [裝載的 VS2017] 
    * 如果您想在 arm32v7 平台中建置適用於 Linux 容器的模組，您必須按一下 [管理] 按鈕來建立自己的組建代理程式。
    
    ![設定組建代理程式](./media/how-to-ci-cd/configure-env.png)

1. 在代理程式作業中，按一下 [+] 以在組建管線中新增兩個工作。 第一個是來自 **Azure IoT Edge**。 而第二個是來自**發行組建成品**
    
    ![新增工作](./media/how-to-ci-cd/add-tasks.png)

1. 在第一個 **Azure IoT Edge** 工作中，將 [顯示名稱] 更新為**模組建置與推送**，然後在 [動作] 下拉式清單中，選取 [建置與推送]。 在 [Module.json 檔案] 文字方塊中，新增以下路徑。 然後選擇 [容器登錄類型]，確定您設定及選取與程式碼 (module.json) 中相同的登錄。 這項工作會建置及推送解決方案中的所有模組，並且發佈至您指定的容器登錄。 如果您的模組會推送至不同的登錄，您可以有多個**模組建置及推送**工作。 如果 IoT Edge 解決方案不在您程式碼存放庫的根目錄下，您可以在組建定義中指定 **Edge 解決方案根目錄的路徑**。
    
    ![建置與推送](./media/how-to-ci-cd/build-and-push.png)

1. 在 [發行組建成品] 工作中，您要指定建置工作所產生的部署檔案。 將 [要發行的路徑] 設定為 "config/deployment.json"。 如果您在上個工作中設定 **Edge 解決方案根目錄的路徑**，您必須在此處加入根目錄路徑。 例如，如果 Edge 解決方案根目錄的路徑是 "./edgesolution"，則 [要發行的路徑] 應該是 "./edgesolution/config/deployment.json"。 `deployment.json` 檔案是在建置階段時產生，因此您可以放心地忽略文字方塊中的紅色錯誤程式行。 

    ![發行成品](./media/how-to-ci-cd/publish-build-artifacts.png)

1. 開啟 [觸發程序] 索引標籤，然後開啟 [持續整合] 觸發程序。 請確定已納入包含程式碼的分支。

    ![設定觸發程序](./media/how-to-ci-cd/configure-trigger.png)

    儲存新的組建管線。 按一下 [儲存]  按鈕。


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>針對持續部署設定 Azure Pipelines
在本節中，您會建立一個組建管線，並將其設定為在您的組建管線置放成品時自動執行，而且它也會在 Azure Pipelines 中顯示部署記錄。

1. 在 [發行] 索引標籤中，選擇 [+ 新增管線]。 或者，如果您已經有組建管線，請選擇 [+ 新增] 按鈕。  

    ![新增發行管線](./media/how-to-ci-cd/add-release-pipeline.png)

    在 [選取範本] 視窗中，選擇 [從空白作業開始]。

    ![從空白作業開始](./media/how-to-ci-cd/start-with-empty-job.png)

2. 接著，發行管線會以一個階段初始化：**階段 1**。 將**階段 1** 重新命名為 **QA**，並將它視為測試環境。 在常見的持續部署管線中，它通常存在多個階段，您可以根據您的 DevOps 做法來建立更多階段。

    ![建立階段](./media/how-to-ci-cd/QA-env.png)

3. 將發行連結到組建成品。 在成品區域中按一下 [新增]。

    ![新增成品](./media/how-to-ci-cd/add-artifacts.png)  
    
    在 [新增成品] 頁面中，選擇來源類型 [組建]。 接著選取您建立的專案和組建管線。 然後按一下 [新增]。

    ![新增構件](./media/how-to-ci-cd/add-an-artifact.png)

    開啟持續部署觸發程序，如此每當有新的組建可用時，便會建立新的發行。

    ![設定觸發程序](./media/how-to-ci-cd/add-a-trigger.png)

4. 瀏覽至 **QA 階段**，並設定此階段中的工作。

    ![設定 QA 工作](./media/how-to-ci-cd/view-stage-tasks.png)

   部署工作是不區分大小寫的平台，這表示您可以選擇 [代理程式集區] 中的 [裝載的 VS2017] 或 [裝載的 Ubuntu 1604] (或您自己管理的任何其他代理程式)。 按一下 [+] 並新增一個工作。

    ![新增 QA 的工作](./media/how-to-ci-cd/add-task-qa.png)

5. 在 Azure IoT Edge 工作中瀏覽至 [動作] 下拉式清單，選取 [部署至 IoT Edge 裝置]。 選取您的 **Azure 訂用帳戶**並且輸入您的 **IoT 中樞名稱**。 您可以指定 IoT Edge **部署識別碼**和部署**優先順序**。 您也可以選擇要部署到單一或多個裝置。 如果您要部署到**多個裝置**，您必須指定裝置**目標條件**。 目標條件會在 IoT 中樞內篩選出符合的一組 Edge 裝置。 如果您想使用裝置標記作為條件，您必須使用 IoT 中樞裝置對應項更新對應的裝置標記。 假設您有數個 IoT Edge 裝置已標記為 'qa'，則工作設定將會如下列螢幕擷取畫面所示。 

    ![部署至 QA](./media/how-to-ci-cd/deploy-to-qa.png)

    儲存新的發行管線。 按一下 [儲存]  按鈕。 然後，按一下 [管線] 以回到管線。

6. 第二個階段是針對生產環境。 若要新增新的階段 "PROD"，您可以直接複製階段 "QA"，並將複製的階段重新命名為 **PROD**。

    ![複製階段](./media/how-to-ci-cd/clone-stage.png)

7. 針對生產環境設定工作。 假設您有數個 IoT Edge 裝置已標記為 'prod'，在工作設定中，請將 [目標條件] 更新為 "prod"，然後將部署識別碼設為 "deploy-prod"。 按一下 [儲存]  按鈕。 然後，按一下 [管線] 以回到管線。
    
    ![部署至生產環境](./media/how-to-ci-cd/deploy-to-prod.png)

7. 目前，我們的組建成品會在 **QA** 階段上持續觸發，然後進入 **PROD** 階段。 但大多時候您需要整合一些在 QA 裝置上的測試案例，並手動核准那些部分。 稍後，那些部分將會部署到 PROD 環境。 依下列所示在 PROD 階段中設定核准。

    1. 開啟 [部署前的條件] 設定面板。

        ![開啟部署前的條件](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. 在 [部署前核准] 中設定 [啟用]。 然後填寫 [核准者] 輸入。 然後按一下 [儲存] 。
    
        ![設定條件](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. 現在您的發行管線已設定如下所示。

    ![發行管線](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>使用組建和發行管線驗證 IoT Edge CI/CD

在本節中，您將觸發組建來使 CI/CD 管線運作。 接著驗證部署是否成功。

1. 若要觸發組建作業，您可以推送認可至原始程式碼存放庫，或是手動觸發。 您可以按一下 [佇列] 按鈕來觸發組建管線中的組建作業，如下列螢幕擷取畫面所示。

    ![手動觸發](./media/how-to-ci-cd/manual-trigger.png)

2. 如果順利完成組建管線，將會觸發發行至 **QA** 階段。 瀏覽至組建管線記錄，您應該會看到下列內容。

    ![組建記錄檔](./media/how-to-ci-cd/build-logs.png)

3. 成功部署到 **QA** 階段將會觸發核准者的通知。 瀏覽至發行管線，您可以看到下列內容。 

    ![等待核准](./media/how-to-ci-cd/pending-approval.png)


4. 在核准者核准這項變更之後，它就可以部署到 **PROD**。

    ![部署到 PROD](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>後續步驟

* 在[了解單一裝置或大規模的 IoT Edge 部署](module-deployment-monitoring.md)中，了解 IoT Edge 部署。
* 逐步解說[大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md)中，用來建立、更新或刪除部署的步驟。
