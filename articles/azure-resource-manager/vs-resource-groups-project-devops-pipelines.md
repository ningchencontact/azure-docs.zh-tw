---
title: 搭配 Azure 管線和 Resource Manager 範本的 CI/CD
description: 描述如何設定 Azure 管線中的持續整合，在 Visual Studio 中使用 Azure 資源群組部署專案，來部署 Resource Manager 範本。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: article
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: b70b38904c0373c53c3731dd0442511116d9c4de
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191456"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>整合 Azure 管線中的 Resource Manager 範本

Visual Studio 提供建立範本，並將其部署到您的 Azure 訂用帳戶的 Azure 資源群組專案。 您可以整合 Azure 管線這個專案的持續整合與持續部署 (CI/CD)。

有兩種方式可使用 Azure 管線部署範本：

* **將執行 Azure PowerShell 指令碼工作加入**。 此選項的優點是能夠提供整個開發生命週期的一致性，因為您是使用相同的指令碼隨附於 Visual Studio 專案 (Deploy-AzureResourceGroup.ps1) 中。 指令碼階段成品從您的專案可以存取 Resource Manager 的儲存體帳戶。 成品是在您的專案，例如連結的範本、 指令碼和應用程式二進位檔中的項目。 接著，指令碼部署範本。

* **將工作新增至複製並部署工作**。 此選項可提供專案指令碼的方便替代。 您可以設定兩個工作在管線中。 一個工作階段的成品，另一個工作會將範本部署。

本文章說明這兩種方法。

## <a name="prepare-your-project"></a>準備您的專案

本文假設您的 Visual Studio 專案及 Azure DevOps 的組織準備好建立管線。 下列步驟示範如何確定您已準備好：

* 您有 Azure DevOps 的組織。 如果您沒有帳戶，請[免費建立一個](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)。 如果您的小組已經有 Azure DevOps 的組織，請確定您是系統管理員的您想要使用 Azure DevOps 專案。

* 您已設定[服務連線](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)您 Azure 訂用帳戶。 在管線中的工作執行的服務主體的身分識別之下。 如需建立連線的步驟，請參閱[建立 DevOps 專案](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project)。

* 您有 Visual Studio 專案中建立**Azure 資源群組**入門範本。 如需建立該類型的專案資訊，請參閱[建立和部署 Azure 資源群組，透過 Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

* 您的 Visual Studio 專案[連接到 Azure DevOps 專案](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)。

## <a name="create-pipeline"></a>建立管線

1. 如果您先前沒有加入管線，您需要建立新的管線。 從您 Azure DevOps 的組織，選取**管線**並**新管線**。

   ![新增新的管線](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. 指定儲存您的程式碼的位置。 下圖顯示選取**Azure 儲存機制的 Git**。

   ![選取程式碼來源](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. 從該來源中，選取具有您專案的程式碼存放庫。

   ![選取存放庫](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. 選取管線，以建立的類型。 您可以選取**入門管線**。

   ![選取管線](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

您即可新增 Azure PowerShell 工作或複製檔案，並部署工作。

## <a name="azure-powershell-task"></a>Azure PowerShell 工作

本節說明如何設定持續部署所使用的單一工作，在您的專案中執行 PowerShell 指令碼。 下列 YAML 檔案會建立[Azure PowerShell 工作](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' 
    azurePowerShellVersion: LatestVersion
```

當您將工作設`AzurePowerShell@3`，管線會使用從 AzureRM 模組的命令來驗證連線。 根據預設，Visual Studio 專案中的 PowerShell 指令碼會使用 AzureRM 模組。 如果您已更新您要使用的指令碼[Az 模組](/powershell/azure/new-azureps-module-az)，將工作`AzurePowerShell@4`。

```yaml
steps:
- task: AzurePowerShell@4
```

針對`azureSubscription`，提供您所建立的服務連接的名稱。

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

針對`scriptPath`，提供您的指令碼從管線檔案的相對路徑。 您可以查看您的儲存機制，可以看到的路徑。

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

如果您不需要階段成品，只傳遞的名稱和要用於部署的資源群組的位置。 如果不存在，Visual Studio 指令碼會建立資源群組。

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

如果您要在現有的儲存體帳戶的階段成品時，請使用：

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

現在，您了解如何建立工作，讓我們瀏覽編輯管線的步驟。

1. 開啟您的管線，並取代內容您 YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. 選取 [ **儲存**]。

   ![儲存管線](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. 提供的訊息進行認可，並直接認可至**主要**。

1. 當您選取**儲存**，組建管線會自動執行。 返回您的組建管線的摘要，並監看狀態。

   ![檢視結果](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

您可以選取目前正在執行的管線，以查看工作的相關詳細資料。 完成後，您會看到每個步驟的結果。

## <a name="copy-and-deploy-tasks"></a>複製並部署工作

本節說明如何使用兩個工作階段的成品，然後部署範本中設定連續部署。 

下列 YAML 示範[Azure 檔案複製工作](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

有幾個部分，以便為您的環境修改此工作。 `SourcePath`表示相對於管線檔案成品的位置。 在此範例中，檔案會位於名為的資料夾`AzureResourceGroup1`是專案的名稱。

```yaml
SourcePath: '<path-to-artifacts>'
```

針對`azureSubscription`，提供您所建立的服務連接的名稱。

```yaml
azureSubscription: '<your-connection-name>'
```

針對儲存體和容器名稱，提供儲存體帳戶和您想要用於儲存成品的容器的名稱。 儲存體帳戶必須存在。

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

下列 YAML 示範[Azure 資源群組部署工作](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

有幾個部分，以便為您的環境修改此工作。 針對`azureSubscription`，提供您所建立的服務連接的名稱。

```yaml
azureSubscription: '<your-connection-name>'
```

針對`resourceGroupName`和`location`，提供您想要部署至資源群組的位置與名稱。 如果不存在，此工作會建立資源群組。

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

部署工作連結至範本名為`WebSite.json`和參數檔案，名為 「 WebSite.parameters.json。 使用範本和參數檔案的名稱。

現在，您了解如何建立工作，讓我們瀏覽編輯管線的步驟。

1. 開啟您的管線，並取代內容您 YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
   - task: AzureResourceGroupDeployment@2
     displayName: 'Deploy template'
     inputs:
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. 選取 [ **儲存**]。

1. 提供的訊息進行認可，並直接認可至**主要**。

1. 當您選取**儲存**，組建管線會自動執行。 返回您的組建管線的摘要，並監看狀態。

   ![檢視結果](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

您可以選取目前正在執行的管線，以查看工作的相關詳細資料。 完成後，您會看到每個步驟的結果。

## <a name="next-steps"></a>後續步驟

如需 Resource Manager 範本搭配使用 Azure 管線的逐步程序，請參閱[教學課程：持續整合的 Azure Resource Manager 範本搭配 Azure 管線](resource-manager-tutorial-use-azure-pipelines.md)。
