---
title: 持續提供使用 Azure DevOps 的函式程式碼更新
description: 了解如何設定目標 Azure Functions 的 Azure DevOps 管線。
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: 27b5dc9ccee8647d4fbb617063865df18b80bc5d
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990267"
---
# <a name="continuous-delivery-using-azure-devops"></a>使用 Azure DevOps 的持續傳遞

您可使用您建立 Azure 函式應用程式自動部署您的函式[Azure 管線](/azure/devops/pipelines/)。
若要定義您的管線，您可以使用：

- YAML 檔案：這個檔案會描述管線，它可能會有組建步驟 區段中和 釋放 區段。 YAML 檔案應該位於相同的存放庫與應用程式。

- 範本：範本已準備進行建置或部署您的應用程式的工作。

## <a name="yaml-based-pipeline"></a>YAML 管線

### <a name="build-your-app"></a>建置您的應用程式

建置您的應用程式，在 Azure 中的管線，取決於您的應用程式的程式設計語言。
每個語言都有特定的建置步驟，以建立可用來部署您的函式應用程式，在 Azure 中的部署成品。

#### <a name="net"></a>.NET

您可以使用下列範例來建立您的 YAML 檔案，以建置您的.NET 應用程式。

```yaml
jobs:
  - job: Build
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

您可以使用下列範例來建立您的 YAML 檔案，以建置您的 JavaScript 應用程式：

```yaml
jobs:
  - job: Build
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

您可以使用下列範例來建立您的 YAML 檔案，以建置您的 Python 應用程式，適用於 Linux 的 Azure Functions 只支援 Python:

```yaml
jobs:
  - job: Build
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

### <a name="deploy-your-app"></a>部署您的應用程式

根據主機作業系統中，您需要在 YAML 檔案中包含下列 YAML 範例。

#### <a name="windows-function-app"></a>Windows 函式應用程式

下列程式碼片段可用於部署的 Windows 函式應用程式

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
```

#### <a name="linux-function-app"></a>Linux 函式應用程式

下列程式碼片段可以用來部署至 Linux 函式應用程式

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
```

## <a name="template-based-pipeline"></a>範本為基礎的管線

Azure DevOps 中的範本是預先定義的建置或部署應用程式的工作群組。

### <a name="build-your-app"></a>建置您的應用程式

建置您的應用程式，在 Azure 中的管線，取決於您的應用程式的程式設計語言。 每個語言都有特定的建置步驟，以建立部署的成品，可用來更新您在 Azure 中的函式應用程式。
若要使用的內建建置範本，建立新的組建管線時，選擇**使用傳統的編輯器**來建立管線，使用設計工具的範本

![Azure 的管線傳統編輯器](media/functions-how-to-azure-devops/classic-editor.png)

設定您的程式碼的來源之後, 搜尋適用於 Azure Functions 建置範本，然後選擇符合您的應用程式語言的範本。

![Azure Functions 建置範本](media/functions-how-to-azure-devops/build-templates.png)

#### <a name="javascript-apps"></a>JavaScript 應用程式

如果您的 JavaScript 應用程式會有相依性 Windows 原生模組，您必須更新：

- 代理程式集區版本**Hosted VS2017**

  ![變更組建代理程式作業系統](media/functions-how-to-azure-devops/change-agent.png)

- 中的指令碼**建置擴充功能**步驟在範本中 `IF EXIST *.csproj dotnet build extensions.csproj --output ./bin`

  ![變更指令碼](media/functions-how-to-azure-devops/change-script.png)

### <a name="deploy-your-app"></a>部署您的應用程式

當建立新的發行管線，搜尋 Azure Functions 發行範本。

![](media/functions-how-to-azure-devops/release-template.png)

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>建立使用 Azure CLI，Azure 管線

使用`az functionapp devops-pipeline create`[命令](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)，若要建置和發行您的存放庫中變更任何程式碼會建立 Azure 的管線。 此命令會產生新的 YAML 檔案來定義組建和發行管線，並認可至存放庫。
此命令的先決條件取決於您的程式碼的位置：

- 如果您的程式碼是在 GitHub 中：

    - 您必須能夠**寫入**您訂用帳戶的權限。

    - 您會在 Azure DevOps 專案系統管理員。

    - 您有足夠的權限建立 GitHub 個人存取權杖的權限。 [GitHub PAT 權限需求。](https://aka.ms/azure-devops-source-repos)

    - 您必須認可到您的 GitHub 存放庫，來認可自動產生的 YAML 檔案中的主要分支的權限。

- 如果您的程式碼是在 Azure 的存放庫：

    - 您必須能夠**寫入**您訂用帳戶的權限。

    - 您會在 Azure DevOps 專案系統管理員。

## <a name="next-steps"></a>後續步驟

+ [Azure Functions 概觀](functions-overview.md)
+ [Azure 的 DevOps 概觀](/azure/devops/pipelines/)
