---
title: 持續整合與持續部署 - Azure IoT Edge | Microsoft Docs
description: 設定持續整合和持續部署 - Azure IoT Edge 搭配 Azure DevOps、Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 85f77d1132af63681ee92cfd2bde82a71d8ed999
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457235"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Azure IoT Edge 的持續整合與持續部署

您可以使用 Azure Pipelines 中內建的 Azure IoT Edge 工作，輕鬆地搭配您的 Azure IoT Edge 應用程式採用 DevOps。 此文章示範如何使用 Azure Pipelines 的持續整合與持續部署功能，快速且有效率地建置、測試應用程式，並且將其部署到您的 Azure IoT Edge。 

![圖表 - 開發和生產的 CI 和 CD 分支](./media/how-to-ci-cd/cd.png)

在本文中，您會了解如何使用 Azure Pipelines 的內建 Azure IoT Edge 工作，為您的 IoT Edge 解決方案建立兩個管線。 There are four actions can be used in the Azure IoT Edge tasks.
   - **Azure IoT Edge - Build Module images** takes your IoT Edge solution code and builds the container images.
   - **Azure IoT Edge - Push Module images** pushes module images to the container registry you specified.
   - **Azure IoT Edge - Generate Deployment Manifest** takes a deployment.template.json file and the variables, then generates the final IoT Edge deployment manifest file.
   - **Azure IoT Edge - Deploy to IoT Edge devices** helps create IoT Edge deployments to single/multiple IoT Edge devices.

## <a name="prerequisites"></a>必要條件

* Azure Repos 存放庫。 如果您沒有存放庫，可以[在專案中建立新的 Git 存放庫](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav) \(英文\)。
* 已認可並推送至您存放庫的 IoT Edge 解決方案。 若要建立用於測試本文的新範例解決方案，請遵循[在 Visual Studio Code 中針對模組進行開發與偵錯](how-to-vs-code-develop-module.md)，或[在 Visual Studio Code 中對 C# 模組進行開發與偵錯](how-to-visual-studio-develop-csharp-module.md)中的步驟。
   * 針對本文，您只需要在 Visual Studio Code 或 Visual Studio 中由 IoT Edge 範本所建立的解決方案資料夾。 在進行之前，您並不需要針對此程式碼進行建置、推送、部署或偵錯。 您將在 Azure Pipelines 中設定那些程序。 
   * 若您是建立新的解決方案，請先在本機複製您的存放庫。 然後，當您建立解決方案時，您可以選擇直接在存放庫資料夾中建立它。 您可以輕鬆地從該處對新檔案進行認可和推送。 
* 可對它推送模組映像的容器登錄。 您可以使用 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 或協力廠商登錄。 
* 至少具有 IoT Edge 裝置的作用中 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)，以用來測試個別的測試和生產部署階段。 您可以遵循快速入門文章來在 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 上建立 IoT Edge 裝置


如需使用 Azure Repos 的詳細資訊，請參閱[與 Visual Studio 和 Azure Repos 共用程式碼](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts) \(英文\)

## <a name="configure-continuous-integration"></a>設定持續整合
在本節中，您會建立新的建置管線。 設定管線以在將任何變更簽入範例 IoT Edge 解決方案時自動執行，並發行組建記錄。

>[!NOTE]
>本文會使用 Azure DevOps 視覺化設計工具。 在遵循本節中的步驟之前，請關閉新的 YAML 管線建立體驗的預覽功能。 
>1. 在 Azure DevOps 中，選取您的設定檔圖示，然後選取 [預覽功能]。
>2. 將 [新的 YAML 管線建立體驗] 關閉。 
>
>如需詳細資訊，請參閱[建立建置管線](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline) \(英文\)。

1. Sign into your Azure DevOps organization (**https:\//dev.azure.com/{your organization}/** ) and open the project that contains your IoT Edge solution repository.

   針對本文，我們建立了名為 **IoTEdgeRepo** 的存放庫。 該存放庫包含 **IoTEdgeSolution**，其中包含名為 **filtermodule** 的模組的程式碼。 

   ![開啟您的 DevOps 專案](./media/how-to-ci-cd/init-project.png)

2. 瀏覽至專案中的 Azure Pipelines。 開啟 [組建] 索引標籤，然後選取 [新增管線]。 或者，如果您已經有建置管線，請選取 [新增] 按鈕。 然後選擇 [新增建置管線]。

    ![建立新的組建管線](./media/how-to-ci-cd/add-new-build.png)

3. 遵循提示以建立管線。 

   1. 提供新建置管線的來源資訊。 選取 [Azure Repos Git] 作為來源，然後選取 IoT Edge 解決方案程式碼所在的專案、存放庫及分支。 然後，選取 [繼續]。 

      ![選取您的管線來源](./media/how-to-ci-cd/pipeline-source.png)

   2. 選取 [空白作業]，而非範本。 

      ![以空白的處理序開始](./media/how-to-ci-cd/start-with-empty.png)

4. 建立管線之後，系統會將您引導至管線編輯器。 在管線描述中，根據目標平台選擇正確的代理程式集區： 
    
   * 如果您想要在 amd64 平台中建置適用於 Linux 容器的模組，請選擇 [裝載的 Ubuntu 1604]

   * 如果您想要在 amd64 平台中建置適用於 Windows 1809 容器的模組，您必須[在 Windows 上設定自我裝載的代理程式](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts) \(英文\)。

   * If you would like to build your modules in platform arm32v7 or arm64 for Linux containers, you need to [set up self-hosted agent on Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![設定組建代理程式集區](./media/how-to-ci-cd/configure-env.png)

5. 您的管線已預先設定名為**代理程式作業 1** 的作業。 Select the plus sign ( **+** ) to add three tasks to the job: **Azure IoT Edge** twice, **Copy Files** once and **Publish Build Artifacts** once. (將滑鼠暫留在每個工作的名稱上以顯示 [新增] 按鈕)。

   ![新增 Azure IoT Edge 工作](./media/how-to-ci-cd/add-iot-edge-task.png)

   When all four tasks are added, your Agent job looks like the following example:
    
   ![建置管線中的三個工作](./media/how-to-ci-cd/add-tasks.png)

6. 選取第一個 [Azure IoT Edge] 工作來編輯它。 This task builds all modules in the solution with the target platform that you specify.

   * **Display name**: Accept the default **Azure IoT Edge - Build module images**.
   * **Action**: Accept the default **Build module images**. 
   * **.template.json file**: Select the ellipsis ( **...** ) and navigate to the **deployment.template.json** file in the repository that contains your IoT Edge solution. 
   * **Default platform**: Select the appropriate platform for your modules based on your target IoT Edge device. 
   * **Output variables**: The output variables include a reference name that you can use to configure the file path where your deployment.json file will be generated. 將參考名稱設定為易記的名稱，例如 **edge**。 

7. 選取第二個 [Azure IoT Edge] 工作來編輯它。 此工作會將所有模組映像推送到您選取的容器登錄。

   * **Display name**: The display name is automatically updated when the action field changes. 
   * **Action**: Use the dropdown list to select **Push module images**. 
   * **Container registry type**: Select the type of container registry that you use to store your module images. 表單會根據您所選擇的登錄類型而變更。 如果您選擇 [Azure Container Registry]，請使用下拉式清單來選取 Azure 訂用帳戶和容器登錄的名稱。 如果您選擇 [泛型容器登錄]，請選取 [新增] 以建立登錄服務連線。 
   * **.template.json file**: Select the ellipsis ( **...** ) and navigate to the **deployment.template.json** file in the repository that contains your IoT Edge solution. 
   * **Default platform**: Select the same platform as your built module images.

   如果您有多個容器登錄可裝載您的模組映像，您需要複製此工作、選取不同的容器登錄，然後使用進階設定中的 [略過模組]，略過不適用於此特定登錄的映像。

8. Select the **Copy Files** task to edit it. Use this task to copy files to the artifact staging directory.

   * **Display name**: Copy Files to: Drop folder.
   * **Contents**: Put two lines in this section, `deployment.template.json` and `**/module.json`. These two types of files are the inputs to generate IoT Edge deployment manifest. Need to be copied to the artifact staging folder and published for release pipeline.
   * **Target Folder**: Put the variable `$(Build.ArtifactStagingDirectory)`. See [Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) to learn about the description.

9. 選取 [發行組建成品] 工作來編輯它。 Provide artifact staging directory path to the task so that the path can be published to release pipeline.
   
   * **Display name**: Publish Artifact: drop.
   * **Path to publish**: Put the variable `$(Build.ArtifactStagingDirectory)`. See [Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) to learn about the description.
   * **Artifact name**: drop.
   * **Artifact publish location**: Azure Pipelines.


10. 開啟 [觸發程序] 索引標籤，然後核取 [啟用持續整合] 方塊。 請確定已納入包含程式碼的分支。

    ![開啟持續整合觸發程序](./media/how-to-ci-cd/configure-trigger.png)

11. 使用 [儲存] 按鈕儲存新的組建管線。

此管線現在已設定為會在將新程式碼推送至存放庫時自動執行。 最後一個工作「發行管線成品」則會觸發發行管線。 繼續進行下一節以建置發行管線。 

## <a name="configure-continuous-deployment"></a>設定連續部署
在本節中，您會建立發行管線，並將其設定為會在建置管線置放成品時自動執行，且會在 Azure Pipelines 中顯示部署記錄。

Create a new pipeline, and add a new stage 

1. 在 [發行] 索引標籤中，選擇 [+ 新增管線]。 或者，如果您已經有發行管線，請選擇 [+ 新增] 按鈕，然後選取 [+ 新增發行管線]。  

    ![新增發行管線](./media/how-to-ci-cd/add-release-pipeline.png)

2. 當系統提示您選取範本時，選擇以 [空白作業] 開始。

    ![從空白作業開始](./media/how-to-ci-cd/start-with-empty-job.png)

3. 您的新發行管線會以單一階段初始化，其名為**階段 1**。 Rename Stage 1 to **dev** and treat it as a test environment. Usually, continuous deployment pipelines have multiple stages including **dev**, **staging** and **prod**. You can create more based on your DevOps practice. 重新命名後，請關閉階段詳細資料視窗。 

4. 將版本連結到由建置管線發佈的組建成品。 在成品區域中按一下 [新增]。

   ![新增成品](./media/how-to-ci-cd/add-artifacts.png)  
    
5. 在 [新增成品] 頁面中，選取來源類型 [組建]。 接著選取您所建立的專案和建置管線。 然後選取 [新增]。

   ![新增組建成品](./media/how-to-ci-cd/add-an-artifact.png)

6. 開啟成品觸發程序，並選取切換以啟用持續部署觸發程序。 現在，每次有新的可用組建時，系統都會建立新的版本。

   ![設定連續部署觸發程序](./media/how-to-ci-cd/add-a-trigger.png)

7. The **dev** stage is preconfigured with one job and zero tasks. From the pipeline menu, select **Tasks** then choose the **dev** stage.  選取作業和工作計數，以設定這個階段中的工作。

    ![Configure dev tasks](./media/how-to-ci-cd/view-stage-tasks.png)

8. In the **dev** stage, you should see a default **Agent job**. 您可以設定有關代理程式作業的詳細資料，但部署工作並不會區分平台，因此您可以使用 [代理程式集區] 中的 [裝載的 VS2017] 或 [裝載的 Ubuntu 1604] (或您自己管理的任何其他代理程式)。 

9. Select the plus sign ( **+** ) to add two task. Search for and add **Azure IoT Edge** twice.

    ![Add tasks for dev](./media/how-to-ci-cd/add-task-qa.png)

10. Select the first **Azure IoT Edge** task and configure it with the following values:

    * **Display name**: The display name is automatically updated when the action field changes. 
    * **Action**: Use the dropdown list to select **Generate deployment manifest**. 變更動作值時，系統也會更新工作顯示名稱以使其相符。
    * **.template.json file**: Put the path `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`. The path is published from build pipeline.
    * **Default platform**: Choose the same value when building the module images.
    * **Output path**: Put the path `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`. This path is the final IoT Edge deployment manifest file.

    These configurations helps replace the module image URLs in the `deployment.template.json` file. The **Generate deployment manifest** also helps replace the variables with the exact value you defined in the `deployment.template.json` file. In VS/VS Code, you are specifying the actual value in a `.env` file. In Azure Pipelines, you set the value in Release Pipeline Variables tab. Move to Variables tab and configure the Name and Value as following.

    * **ACR_ADDRESS**: Your Azure Container Registry address. 
    * **ACR_PASSWORD**: Your Azure Container Registry password.
    * **ACR_USER**: Your Azure Container Registry username.

    If you have other variables in your project, you can specify the name and value in this tab. The **Generate deployment manifest** can only recognize the variables are in `${VARIABLE}` flavor, make sure you are using this in your `*.template.json` files.

    ![Configure variables for release pipeline](./media/how-to-ci-cd/configure-variables.png)

10. Select the second **Azure IoT Edge** task and configure it with the following values:

    * **Display name**: The display name is automatically updated when the action field changes. 
    * **Action**: Use the dropdown list to select **Deploy to IoT Edge devices**. 變更動作值時，系統也會更新工作顯示名稱以使其相符。
    * **Azure subscription**: Select the subscription that contains your IoT Hub.
    * **IoT Hub name**: Select your IoT hub. 
    * **Choose single/multiple device**: Choose whether you want the release pipeline to deploy to one device or multiple devices. 
      * 如果部署到單一裝置，請輸入 **IoT Edge 裝置識別碼**。 
      * 如果要部署到多個裝置，請指定裝置**目標條件**。 The target condition is a filter to match a set of IoT Edge devices in IoT Hub. 如果您想使用裝置標記作為條件，您必須使用 IoT 中樞裝置對應項更新對應的裝置標記。 在進階設定中更新 [IoT Edge 部署識別碼] 和 [IoT Edge 部署優先順序]。 如需為多個裝置建立部署的詳細資訊，請參閱[了解 IoT Edge 自動部署](module-deployment-monitoring.md)。
    * Expand Advanced Settings, select **IoT Edge deployment ID**, put the variable `$(System.TeamProject)-$(Release.EnvironmentName)`. This maps the project and release name with your IoT Edge deployment ID.

11. 選取 [儲存] 以將變更儲存至新的發行管線。 從功能表中選取 [管線] 以返回管線檢視。 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>使用組建和發行管線驗證 IoT Edge CI/CD

若要觸發組建作業，您可以推送認可至原始程式碼存放庫，或是手動觸發。 在本節中，您會手動觸發 CI/CD 管線以測試它是否有效。 接著驗證部署是否成功。

1. 瀏覽至您在本文開頭所建立的建置管線。 

2. 您可以選取 [佇列] 按鈕來觸發組建管線中的組建作業，如下列螢幕擷取畫面所示。

    ![手動觸發](./media/how-to-ci-cd/manual-trigger.png)

3. 選取建置作業以查看其進度。 If the build pipeline is completed successfully, it triggers a release to **dev** stage. 

    ![組建記錄](./media/how-to-ci-cd/build-logs.png)

4. The successful **dev** release creates IoT Edge deployment to target IoT Edge devices.

    ![Release to dev](./media/how-to-ci-cd/pending-approval.png)

5. Click **dev** stage to see release logs.

    ![發行記錄](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>後續步驟
* IoT Edge DevOps best practices sample in [Azure DevOps Project for IoT Edge](how-to-devops-project.md)
* 在[了解單一裝置或大規模的 IoT Edge 部署](module-deployment-monitoring.md)中，了解 IoT Edge 部署。
* 逐步解說[大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md)中，用來建立、更新或刪除部署的步驟。
