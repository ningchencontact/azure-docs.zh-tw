---
title: 使用 Azure 管線來建置和部署的 HPC 解決方案-Azure Batch |Microsoft Docs
description: 了解如何部署 Azure Batch 上執行的 HPC 應用程式的建置/發行管線。
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.openlocfilehash: 5b7c44d3ea3394ff728adfb9d9fd72293138fb2e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59494639"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>使用 Azure 管線來建置和部署的 HPC 解決方案

Azure 的 DevOps 服務提供一系列建置自訂的應用程式時，開發小組所使用的工具。 提供的 Azure DevOps 工具可能會轉變成自動化建置及測試的高效能運算解決方案。 這篇文章會示範如何設定持續整合 (CI) 和持續部署 (CD) 使用 Azure 管線，以獲得高效能計算部署在 Azure Batch 的解決方案。

Azure 的管線會提供一系列新式 CI/CD 程序，建置、 部署、 測試及監視軟體。 這些程序來加速您的軟體傳遞，可讓您專注於您的程式碼，而不是支援基礎結構和操作。

## <a name="create-an-azure-pipeline"></a>建立 Azure 的管線

在此範例中，我們會建立組建和發行管線來部署 Azure Batch 基礎結構，並釋放應用程式封裝。 假設在本機開發的程式碼，這是一般的部署流程︰

![在我們的管線中顯示的部署流程的圖表](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>設定

若要遵循這篇文章中的步驟，您需要 Azure DevOps 的組織與 team 專案。

* [建立 Azure DevOps 的組織](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [建立 Azure DevOps 專案](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>您的環境的原始檔控制

原始檔控制可讓小組追蹤程式碼基底所做的變更，並檢查舊版程式碼。

一般而言，原始檔控制會想到手盈餘與軟體程式碼。 如何在基礎結構？ 這讓基礎結構即程式碼，其中我們將使用 Azure Resource Manager 範本或其他開放原始碼替代方案來以宣告方式定義我們的基礎結構。

此範例重度依賴的 Resource Manager 範本 （JSON 文件） 和現有的二進位檔的數目。 您可以將這些範例複製到您的存放庫，並將其推送至 Azure 的 DevOps。

此範例中使用的程式碼基底結構類似於下列作業：

* **Arm 範本**資料夾，其中包含許多 Azure Resource Manager 範本。 範本會在本文中說明。
* A**用戶端應用程式**資料夾，這是複本的[ffmpeg 處理的 Azure Batch.NET 檔案](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial)範例。 這不是需要這篇文章。
* **Hpc 應用程式**資料夾，也就是 Windows 64 位元版本的[ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)。
* A**管線**資料夾。 這包含大綱我們建置程序的 YAML 檔案。 這篇文章中討論。

本節假設您已熟悉使用版本控制和設計 Resource Manager 範本。 如果您不熟悉這些概念，請參閱下列頁面，如需詳細資訊。

* [什麼是原始檔控制？](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [了解 Azure Resource Manager 範本的的結構和語法](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

此範例會利用多個的 Resource Manager 範本來部署解決方案。 若要這樣做，我們使用的一些功能範本 （類似於單元或模組） 實作特定功能。 我們也會使用端對端解決方案範本負責將這些基本功能在一起。 有幾個這種方法的好處：

* 基礎功能的範本可以是個別的單元測試。
* 基礎功能的範本可以定義為在組織內的標準，並重複用於多個解決方案。

此範例中，還有部署三個範本的端對端解決方案範本 (deployment.json)。 基礎是功能範本，負責部署解決方案的特定層面。

![使用 Azure Resource Manager 範本連結範本結構範例](media/batch-ci-cd/ARMTemplateHierarchy.png)

我們將探討的第一個範本是 Azure 儲存體帳戶。 我們的解決方案需要儲存體帳戶部署至我們的 Batch 帳戶的應用程式。 值得留意[Microsoft.Storage 資源類型的 Resource Manager 範本參考指南](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions)儲存體帳戶中建立 Resource Manager 範本時。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

接下來，我們將探討 Azure Batch 帳戶的範本。 Azure Batch 帳戶做為平台來執行多種應用程式集區 （機器分組）。 值得留意[Microsoft.Batch 資源類型的 Resource Manager 範本參考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)的 Batch 帳戶中建立 Resource Manager 範本時。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

下一步 範本會示範建立 Azure Batch 集區 （後端機器來處理我們的應用程式）。 值得留意[Microsoft.Batch 資源類型的 Resource Manager 範本參考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)Resource Manager 範本建置 Batch 帳戶集區時。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

最後，我們還會有作用類似於協調器的範本。 此範本會負責部署功能的範本。

您也可以找到更多關於[建立連結的 Azure Resource Manager 範本](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md)在個別的文章。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>HPC 解決方案

可以定義為程式碼和共置於相同的儲存機制的基礎結構和軟體。

針對此解決方案，ffmpeg 做為應用程式封裝。 Ffmpeg 套件可下載[此處](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)。

![範例 Git 存放庫結構](media/batch-ci-cd/git-repository.jpg)

有四個主要的部分到此存放庫：

* **Arm 範本**儲存我們的基礎結構即程式碼資料夾。
* **Hpc 應用程式**ffmpeg 的二進位檔所在的資料夾
* **管線**包含我們建置的管線定義的資料夾。
* **選用**：**用戶端應用程式**會儲存為.NET 應用程式的程式碼的資料夾。 我們不會將這在範例中，但在您自己的專案中，您可能想要執行的 HPC 批次應用程式透過用戶端應用程式。

> [!NOTE]
> 這是結構的一個範例程式碼基底。 這種方法用於便於示範，會將應用程式、 基礎結構，以及管線程式碼儲存在相同的儲存機制。

原始程式碼是設定好，我們可以開始在第一個組建。

## <a name="continuous-integration"></a>持續整合

[Azure 的管線](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)，在 Azure 的 DevOps 服務，可協助您實作您的應用程式的建置、 測試和部署管線。

在這個階段您的管線中，測試通常可以用來驗證程式碼，以及建置軟體的適當項目。 測試，以及您所執行的任何其他工作的類型與數量將取決於您的更多組建和發行策略。

## <a name="preparing-the-hpc-application"></a>正在準備 HPC 應用程式

在此範例中，我們將著重**hpc 應用程式**資料夾。 **Hpc 應用程式**資料夾是在 Azure Batch 帳戶內將會執行 ffmpeg 軟體。

1. 瀏覽至 Azure 管線的 Azure DevOps 組織中的組建一節。 建立**新的管線**。

    ![建立新的組建管線](media/batch-ci-cd/new-build-pipeline.jpg)

1. 您有兩個選項來建立組建管線：

    a. [使用視覺化設計工具](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav)。 若要使用這種情況，請按一下 [使用視覺化設計工具]**新的管線**頁面。

    b. [使用 YAML 組建](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops)。 您可以建立新的 YAML 管線，按一下 Azure 儲存機制或新的 管線 頁面上的 GitHub 選項。 或者，您可以在原始檔控制中儲存下列範例中的，並視覺化設計工具上，按一下，然後使用 YAML 範本參考現有的 YAML 檔案。

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. 組建設定之後視需要選取**儲存與佇列**。 如果您有啟用持續整合 (在**觸發程序**一節)、 進行新的認可至儲存機制時，會自動觸發組建、 組建中符合條件設定。

    ![現有的組建管線的範例](media/batch-ci-cd/existing-build-pipeline.jpg)

1. 瀏覽至您的組建，Azure DevOps 中的進度檢視即時更新**建置**Azure 管線區段。 從您的組建定義中，選取適當的組建。

    ![檢視即時的輸出，從您的組建](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> 如果您使用用戶端應用程式來執行 HPC Batch 應用程式時，您需要建立該應用程式的另一個組建定義。 您可以找到使用說明指南中的數字[Azure 管線](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops)文件。

## <a name="continuous-deployment"></a>連續部署

Azure 的管線也會用來部署您的應用程式和基礎結構。 [發行管線](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops)是元件，可啟用持續部署，並可自動化發行程序。

### <a name="deploying-your-application-and-underlying-infrastructure"></a>部署您的應用程式和基礎結構

有幾個步驟中部署的基礎結構。 因為我們使用了[連結範本](../azure-resource-manager/resource-group-linked-templates.md)，這些範本必須可從公用端點 （HTTP 或 HTTPS）。 這可能是在 GitHub 上的存放庫或 Azure Blob 儲存體帳戶或另一個儲存位置。 將範本上傳的成品可以保持安全，因為它們可以保留在私用模式，但使用某種形式的共用的存取簽章 (SAS) 權杖來存取。 下列範例示範如何部署範本，從 Azure 儲存體 blob 與基礎結構。

1. 建立**新的發行定義**，然後選取 空白的定義。 然後，我們需要將新建立的環境重新命名為我們的管線相關的項目。

    ![初始發行管線](media/batch-ci-cd/Release-0.jpg)

1. 建立相依性上建置的管線，以取得我們的 HPC 應用程式的輸出。

    > [!NOTE]
    > 同樣地，請注意**來源別名**，因為這必要的工作建立發行定義內時。

    ![建立適當的組建管線中 HPCApplicationPackage 成品連結](media/batch-ci-cd/Release-1.jpg)

1. 建立連結至另一個成品，此期間，Azure 儲存機制。 如此才能存取儲存在您的存放庫中的 Resource Manager 範本。 Resource Manager 範本不需要編譯，因為您不需要將其推送到建置管線。

    > [!NOTE]
    > 同樣地，請注意**來源別名**，因為這必要的工作建立發行定義內時。

    ![建立 Azure 儲存機制成品連結](media/batch-ci-cd/Release-2.jpg)

1. 瀏覽至**變數**一節。 建議在您的管線中建立一些變數，讓您不輸入相同的資訊，為多個工作。 這些是在此範例中，和它們如何影響部署中所使用的變數。

    * **applicationStorageAccountName**:儲存體帳戶，以存放 HPC 應用程式二進位檔名稱
    * **batchAccountApplicationName**:在 Azure Batch 帳戶中的應用程式名稱
    * **batchAccountName**:Azure Batch 帳戶的名稱
    * **batchAccountPoolName**:進行處理的 Vm 的集區的名稱
    * **batchApplicationId**:Azure Batch 應用程式的唯一識別碼
    * **batchApplicationVersion**:語意版本的 batch 應用程式 （也就是 ffmpeg 二進位檔）
    * **location**：部署 Azure 資源的位置
    * **resourceGroupName**:要建立的資源群組名稱，並部署您的資源
    * **storageAccountName**：連結 Resource Manager 範本來保存的儲存體帳戶的名稱。

    ![設定 Azure 管線版本變數的範例](media/batch-ci-cd/Release-4.jpg)

1. 瀏覽至開發環境的工作。 在下列快照集，您可以看到六個工作。 這些工作將會： 下載的 ffmpeg zip 壓縮的檔案，部署裝載的巢狀的 Resource Manager 範本、 將這些 Resource Manager 範本複製到儲存體帳戶、 部署必要的相依性與 batch 帳戶、 建立應用程式中的儲存體帳戶Azure Batch 帳戶和上傳至 Azure Batch 帳戶的應用程式封裝。

    ![用來釋放 HPC 應用程式至 Azure Batch 工作的範例](media/batch-ci-cd/Release-3.jpg)

1. 新增**下載 （預覽） 的管線成品**工作，並設定下列屬性：
    * **顯示名稱：** ApplicationPackage 下載至代理程式
    * **若要下載之成品的名稱：** hpc 應用程式
    * **若要下載到路徑**: $ （system.defaultworkingdirectory)

1. 建立儲存體帳戶來儲存您的成品。 可以使用方案的現有儲存體帳戶，但針對自封式的範例和隔離的內容中，我們正在進行專用的儲存體帳戶，我們的成品 （特別是 Resource Manager 範本）。

    新增**Azure 資源群組部署**工作，並設定下列屬性：
    * **顯示名稱：** Resource Manager 範本部署儲存體帳戶
    * **Azure 訂用帳戶：** 選取適當的 Azure 訂用帳戶
    * **動作**：建立或更新資源群組
    * **資源群組**: resourcegroupname
    * **位置**: $(location)
    * **範本**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **覆寫範本參數**:-accountName $(storageAccountName)

1. 將從原始檔控制成品上傳到儲存體帳戶。 沒有執行此作業的 Azure 管線工作。 這項工作的一部分，儲存體帳戶容器 URL 和 SAS 權杖可以被輸出到 Azure 管線中的變數。 這表示可以在此代理程式階段重複使用。

    新增**Azure 檔案複製**工作，並設定下列屬性：
    * **來源：** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates /
    * **Azure 的連線類型**:Azure Resource Manager
    * **Azure 訂用帳戶：** 選取適當的 Azure 訂用帳戶
    * **目的型別**:Azure Blob
    * **RM 儲存體帳戶**: $(storageAccountName)
    * **容器名稱**： 範本
    * **儲存體容器 URI**: templateContainerUri
    * **儲存體容器 SAS 權杖**: templateContainerSasToken

1. 部署 [orchestrator] 範本。 還記得稍早的 orchestrator 範本，您會注意到沒有儲存體帳戶容器 URL，除了 SAS 權杖的參數。 您應該會注意到 Resource Manager 範本中所需的變數可以保留在發行定義中的變數區段中，或從另一項 Azure 管線工作 （例如 Azure Blob 複製工作的一部分） 所設定。

    新增**Azure 資源群組部署**工作，並設定下列屬性：
    * **顯示名稱：** 部署 Azure 批次
    * **Azure 訂用帳戶：** 選取適當的 Azure 訂用帳戶
    * **動作**：建立或更新資源群組
    * **資源群組**: resourcegroupname
    * **位置**: $(location)
    * **範本**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **覆寫範本參數**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

常見的做法是使用 Azure 金鑰保存庫的工作。 如果服務主體 （連線到您 Azure 訂用帳戶） 已設定適當的存取原則，它可以從 Azure Key Vault 祕密，並作為您的管線中的變數。 密碼的名稱將與相關聯的值。 比方說，sshPassword 的祕密無法參考 $(sshPassword) 發行定義中使用。

1. 接下來的步驟會呼叫 Azure CLI。 第一個用來在 Azure Batch 中建立應用程式。 並上傳相關聯的套件。

    新增**Azure CLI**工作，並設定下列屬性：
    * **顯示名稱：** 在 Azure Batch 帳戶中建立應用程式
    * **Azure 訂用帳戶：** 選取適當的 Azure 訂用帳戶
    * **指令碼位置**:內嵌指令碼
    * **內嵌指令碼**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. 第二個步驟用來上傳至應用程式的相關聯的套件。 在本例中的 ffmpeg 檔案。

    新增**Azure CLI**工作，並設定下列屬性：
    * **顯示名稱：** 封裝上傳至 Azure Batch 帳戶
    * **Azure 訂用帳戶：** 選取適當的 Azure 訂用帳戶
    * **指令碼位置**:內嵌指令碼
    * **內嵌指令碼**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > 應用程式套件的版本號碼是設定為變數。 如果覆寫舊版套件適用於您，而且如果您想要以手動方式控制封裝推送至 Azure Batch 的版本號碼，這是很方便。

1. 選取建立新的發行**版本 > 建立新的發行**。 一旦觸發，選取連結至您新的版本，以檢視狀態。

1. 您可以選取，以檢視代理程式的即時輸出**記錄檔**您的環境下的按鈕。

    ![檢視您的發行狀態](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>測試環境

環境設定完畢後，請確認下列測試可以順利完成。

連接至新的 Azure Batch 帳戶，使用 Azure CLI，從 PowerShell 命令提示字元。

* 登入 Azure 帳戶`az login`並遵循指示來進行驗證。
* 現在來進行驗證的 Batch 帳戶︰ `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>列出可用的應用程式

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>檢查集區有效

```azurecli
az batch pool list
```

請記下的值`currentDedicatedNodes`從這個命令的輸出。 這個值會在下一個測試中調整。

#### <a name="resize-the-pool"></a>調整集區的大小

調整集區的大小，讓計算節點適用於工作和測試工作，請檢查與集區清單命令，以查看目前的狀態，直到調整大小已完成，而且有可用的節點

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>後續步驟

除了本文中，有兩個教學課程會使用 ffmpeg，使用.NET 和 Python。 這些教學課程，如需詳細資訊，請參閱如何與 Batch 帳戶，透過簡單的應用程式互動。

* [透過使用 Python API 的 Azure Batch 執行平行工作負載](tutorial-parallel-python.md)
* [透過使用.NET API 的 Azure Batch 執行平行工作負載](tutorial-parallel-dotnet.md)
