---
title: 使用 Azure DevOps Projects 的 CI/CD 管線 - Azure IoT Edge | Microsoft Docs
description: Azure DevOps Projects 可讓您輕鬆地開始使用 Azure。 它可協助您透過幾個簡單的步驟啟動您所選擇的 Azure IoT Edge 應用程式。
author: shizn
manager: ''
ms.author: xshi
ms.date: 12/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ebb7e515f9d9205f364d50b3d686c68a2988f86a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074209"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>使用 Azure DevOps Projects 建立適用於 IoT Edge 的 CI/CD 管線 (預覽)

使用 DevOps Projects 為您的 IoT Edge 應用程式設定持續整合 (CI) 與持續傳遞 (CD)。 DevOps Projects 可簡化 Azure Pipelines 中建置與發行管線的初始設定。

如果您沒有使用中的 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

DevOps Projects 會在 Azure DevOps 中建立 CI/CD 管線。 您可以建立新的 Azure DevOps 組織或使用現有組織。 DevOps Projects 也會在您選擇的 Azure 訂用帳戶中建立 Azure 資源。

1. 登入 [Microsoft Azure 入口網站](https://portal.azure.com)。

1. 在左側窗格中，選取左側瀏覽列中的 [建立資源] 圖示，然後搜尋 [DevOps Projects]。  

1.  選取 [建立] 。

## <a name="select-a-sample-application-and-azure-service"></a>選取應用程式範例和 Azure 服務

1. 您的 Azure IoT Edge 模組可使用 [C#](tutorial-csharp-module.md)、[Node.js](tutorial-node-module.md)、[Python](tutorial-python-module.md)、[C](tutorial-c-module.md) 和 [Java](tutorial-java-module.md) 來撰寫。 選取您的慣用語言來啟動新的應用程式。 同樣地，您可以選取 **.NET**、**Node.js**、**Python**、**C** 或 **Java**，然後按 [下一步]。

    ![選取用來新建應用程式的語言](./media/how-to-devops-project/select-language.png)

2. 選取 [Simple IoT (預覽)]，然後按 [下一步]。

    ![選取 Simple IoT 架構](media/how-to-devops-project/select-iot.png)

3. 選取 [IoT Edge]，然後按 [下一步]。

    ![選取 IoT Edge 服務](media/how-to-devops-project/select-iot-edge.png)

## <a name="configure-azure-devops-and-an-azure-subscription"></a>設定 Azure DevOps 與 Azure 訂用帳戶

1. 免費建立新的 Azure DevOps 組織或選擇現有組織。

    a. 選擇專案的名稱。 

    b. 選取 Azure 訂用帳戶和位置、選擇應用程式名稱，然後選取 [完成]。  

    ![命令和建立應用程式](media/how-to-devops-project/select-devops.png)

1. 在幾分鐘後，Azure 入口網站中便會顯示 DevOps Projects 儀表板。 系統會在您 Azure DevOps 組織的存放庫中設定範例 IoT Edge 應用程式、執行建置，然後將您的應用程式部署到 IoT Edge 裝置。 此儀表板可顯示您的程式碼存放庫、CI/CD 管線，和您在 Azure 中的應用程式。

    ![在 DevOps 入口網站中檢視應用程式](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>認可程式碼變更並執行 CI/CD

DevOps Projects 會在 Azure Repos 或 GitHub 中建立 Git 存放庫。 若要檢視存放庫並變更您應用程式的程式碼，請執行下列步驟：

1. 在 DevOps Project 儀表板的左側，選取您**主要**分支的連結。  
此連結會開啟新建立 Git 存放庫的檢視。

1. 若要檢視存放庫複製 URL，請從瀏覽器右上方選取 [複製]。 您可以在 VS Code 或您慣用的其他工具中複製 Git 存放庫。 在接下來的幾個步驟中，您會使用網頁瀏覽器直接對主要分支進行程式碼變更，並加以認可。

    ![複製 Git 存放庫](media/how-to-devops-project/clone.png)

1. 在瀏覽器左側，移至 **modules/FilterModule/module.json** 檔案。

1. 選取 [編輯]，並在 `"tag"` 下對 `"version"` 進行變更。 例如，您可以將其更新為 `"version": "${BUILD_BUILDID}"`，以使用 [Azure DevOps 組建變數](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables)作為 Azure IoT Edge 模組映像標記的一部分。

    ![編輯版本以接受組建變數](media/how-to-devops-project/update-module-json.png)

1. 選取 [認可]，然後儲存您的變更。

1. 在瀏覽器中，移至 [Azure DevOps Projects 儀表板]。  您現在應該會看到正在進行中的組建。 您所做的變更會透過 CI/CD 管線自動建置及部署。

    ![檢視進行中狀態](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>檢查 CI/CD 管線

在先前的步驟中，Azure DevOps Projects 已自動為您的 IoT Edge 應用程式設定完整的 CI/CD 管線。 瀏覽管線，並視需要進行自訂。 請採取下列步驟，讓您自己熟悉 Azure DevOps 建置和發行管線。

1. 在 DevOps Projects 儀表板頂端選取 [建置管線]。  
此連結會開啟瀏覽器索引標籤和新專案的 Azure DevOps 建置管線。

1. 選取 [編輯]。

    ![編輯組建管線](media/how-to-devops-project/click-edit-button.png)

1. 在此窗格中，您可以檢查建置管線的各種工作。 組建會執行各種工作，例如從 Git 存放庫提取來源、建置 IoT Edge 模組映像、推送 IoT Edge 模組，以及發佈用於部署的輸出。 若要深入了解適用於 CI 的 Azure IoT Edge 工作，您可以瀏覽[針對持續整合設定 Azure Pipelines](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-integration)。

    ![檢視持續整合工作](media/how-to-devops-project/ci.png)

1. 在建置管線的頂端，選取建置管線名稱。

1. 將建置管線的名稱變更成較具描述性的名稱，並選取 [儲存並排入佇列]，然後選取 [儲存]。

1. 在建置管線名稱下，選取 [記錄]。   
在 [記錄] 窗格中，您可以看到近期建置變更的稽核線索。  Azure Pipelines 會追蹤對建置管線進行的任何變更，且可讓您比較版本。

1. 選取 [觸發程序]。 DevOps Projects 已自動建立 CI 觸發程序，且每次對存放庫的認可都會啟動新的建置。  您可以選擇性地選擇要在 CI 程序中包含還是排除分支。

1. 選取 [保留期]。 根據案例，您可以指定原則來保留或移除特定數目的組建。

1. 選取 [管線] 下方的 [發行]。 DevOps Projects 會建立發行管線來管理對 Azure IoT Edge 的部署。

    ![檢視發行管線](media/how-to-devops-project/release-pipeline.png)

1. 選取 [編輯]。 發行管線中包含管線，用來定義發行程序。  

1. 在 [成品] 下，選取 [置放]。 您在先前步驟中檢查的建置管線會產生用於成品的輸出。 

1. 在 [置放] 圖示旁邊，選取 [持續部署觸發程序]。  
這個發行管線已啟用 CD 觸發程序，每次有新的建置成品可用時，它就會執行部署。 您可以選擇性地停用觸發程序，因此需要手動執行部署。  

1. 從左側選取 [工作]。 工作是您部署程序所執行的活動。 在此範例中，您已建立一項將模組映像部署到 Azure IoT Edge 的工作。 若要深入了解適用於 CD 的 Azure IoT Edge 工作，您可以瀏覽[針對持續部署設定 Azure Pipelines](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-deployment)。

    ![檢視持續部署工作](media/how-to-devops-project/dev-release.png)

1. 從右側選取 [檢視版本]。 此檢視會顯示版本的歷程記錄。

1. 選取其中一個版本旁的省略符號 (...)，然後選取 [開啟]。  
您可以瀏覽數個功能表，例如版本摘要、相關聯的工作項目及測試。

1. 選取 [認可]。 此檢視會顯示與特定部署相關聯的程式碼認可。 

1. 選取 [記錄]。 記錄包含關於部署程序的實用資訊。 您可以在部署期間和部署之後加以檢視。


## <a name="clean-up-resources"></a>清除資源

當您不再需要所建立的 Azure App Service 和其他相關資源時，可將其刪除。 請使用 DevOps Projects 儀表板上的 [刪除] 功能。

## <a name="next-steps"></a>後續步驟
* 在 [Azure IoT Edge 的持續整合與持續部署](how-to-ci-cd.md)中了解 Azure DevOps 上的 Azure IoT Edge 工作
* 在[了解單一裝置或大規模的 IoT Edge 部署](module-deployment-monitoring.md)中，了解 IoT Edge 部署。
* 逐步解說[大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md)中，用來建立、更新或刪除部署的步驟。
