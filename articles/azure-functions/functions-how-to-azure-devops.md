---
title: 持續傳遞函式程式碼更新使用 Azure DevOps-Azure Functions
description: 了解如何設定目標 Azure Functions 的 Azure DevOps 管線。
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 0fdad0caa2deef0d7d55b30a85632f72f4ff0ecc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594467"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>使用 Azure DevOps 的持續傳遞

您可以使用自動部署您的函式至 Azure Functions 應用程式[Azure 管線](/azure/devops/pipelines/)。

您有兩個選項來定義您的管線：

- **YAML 檔案**:YAML 檔案會描述管線。 檔案可能會有組建步驟區段和 [釋放] 區段。 YAML 檔案必須位於相同的存放庫與應用程式。
- **範本**:範本會建置或部署您的應用程式的現成工作。

## <a name="yaml-based-pipeline"></a>YAML 管線

若要建立 YAML 管線，第一次建置您的應用程式，然後再部署應用程式。

### <a name="build-your-app"></a>建置您的應用程式

如何建置您的應用程式，在 Azure 中的管線，取決於您的應用程式的程式設計語言。 每個語言都有特定的建置步驟所建立的部署成品。 部署成品用來部署您的函式應用程式，在 Azure 中。

#### <a name="net"></a>.NET

若要建立 YAML 檔案來建置.NET 應用程式，您可以使用下列範例：

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

若要建立 YAML 檔案來建置的 JavaScript 應用程式，您可以使用下列範例：

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

您可以使用下列範例建立 YAML 檔案來建置 Python 應用程式。 只適用於 Linux Azure Functions 支援 Python。

```yaml
pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

您可以使用下列範例來建立 YAML 檔案來封裝 PowerShell 應用程式。 只適用於 Windows Azure Functions 支援 PowerShell。

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>部署您的應用程式

您必須將下列 YAML 範例的其中一個包含在 YAML 檔案中，視主機的作業系統而定。

#### <a name="windows-function-app"></a>Windows 函式應用程式

若要部署的 Windows 函式應用程式，您可以使用下列程式碼片段：

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux 函式應用程式

部署 Linux 函數應用程式，您可以使用下列程式碼片段：

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>範本為基礎的管線

Azure DevOps 中的範本是預先定義的建置或部署應用程式的工作群組。

### <a name="build-your-app"></a>建置您的應用程式

如何建置您的應用程式，在 Azure 中的管線，取決於您的應用程式的程式設計語言。 每個語言都有特定的建置步驟所建立的部署成品。 部署成品來更新您在 Azure 中的函式應用程式。

若要建立新的組建管線時，請使用內建建置範本，請選取**使用傳統的編輯器**使用設計工具的範本建立管線。

![選取 Azure 管線傳統編輯器](media/functions-how-to-azure-devops/classic-editor.png)

設定您的程式碼的來源之後，搜尋 Azure Functions 建置範本。 選取符合您的應用程式語言的範本。

![選取 Azure Functions 建置範本](media/functions-how-to-azure-devops/build-templates.png)

在某些情況下，組建成品會有特定的資料夾結構。 您可能需要選取**前面加上根資料夾名稱，在封存路徑**核取方塊。

![若要在前面加上根資料夾名稱選項](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript 應用程式

如果您的 JavaScript 應用程式具有相依於 Windows 的原生模組，您必須更新代理程式集區版本**Hosted VS2017**。

![更新代理程式集區版本](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>部署您的應用程式

當您建立新的發行管線時，搜尋 Azure Functions 發行範本。

![搜尋 Azure Functions 發行範本](media/functions-how-to-azure-devops/release-template.png)

在發行範本中不支援部署到部署位置。

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>使用 Azure CLI 建立組建管線

若要在 Azure 中建立建置管線，使用`az functionapp devops-pipeline create`[命令](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)。 建置管線用來建置和發行您的存放庫中的任何程式碼變更。 此命令會產生新的 YAML 檔案會定義組建和發行管線，然後將它認可至存放庫。 此命令的先決條件取決於您的程式碼的位置。

- 如果您的程式碼是在 GitHub 中：

    - 您必須擁有**寫入**訂用帳戶的權限。

    - 您必須是 Azure DevOps 專案系統管理員。

    - 您必須建立 GitHub 個人存取權杖 (PAT) 具有足夠權限的權限。 如需詳細資訊，請參閱[GitHub PAT 權限需求。](https://aka.ms/azure-devops-source-repos)

    - 您必須認可到 GitHub 存放庫中的主要分支，因此您就可以認可自動產生 YAML 檔案的權限。

- 如果您的程式碼是在 Azure 的存放庫：

    - 您必須擁有**寫入**訂用帳戶的權限。

    - 您必須是 Azure DevOps 專案系統管理員。

## <a name="next-steps"></a>後續步驟

- 檢閱[Azure Functions 概觀](functions-overview.md)。
- 檢閱[Azure DevOps 概觀](/azure/devops/pipelines/)。
