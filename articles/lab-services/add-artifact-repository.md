---
title: 將構件儲存機制新增至您在 Azure DevTest Labs 的實驗室 |Microsoft Docs
description: 了解如何將構件儲存機制新增至您在 Azure DevTest labs 的實驗室。
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: c1e74efa9cf99e8510ea17aedc840ce3b0731c3b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916666"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>將構件儲存機制新增至您在 DevTest Labs 的實驗室
DevTest Labs 可讓您指定要加入至 VM，在建立 VM 時或之後建立 VM 構件。 一種工具或您想要在 VM 上安裝的應用程式，可能是此成品。 從 GitHub 或 VSTS Git 存放庫所載入的 JSON 檔案中定義的成品。 

[公用成品儲存機制](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)、 由 DevTest Labs 維護、 適用於 Windows 和 Linux 提供許多常用工具。 此存放庫的連結會自動加入至您的實驗室。 您可以使用公用成品儲存機制中無法使用的特定工具來建立您自己的構件儲存機制。 若要了解如何建立自訂構件，請參閱[建立自訂構件](devtest-lab-artifact-author.md)。

本文章提供有關如何使用 Azure 入口網站、 Azure 資源管理範本和 Azure PowerShell 新增您的自訂構件儲存機制的資訊。 您可以自動化撰寫 PowerShell 或 CLI 指令碼將構件儲存機制加入實驗室。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件
若要將存放庫新增至實驗室，請取得存放庫中的重要資訊。 下列各節說明如何取得所需的資訊裝載的儲存機制**GitHub**或是**Azure DevOps**。

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>取得 GitHub 儲存機制複製 URL 和個人存取權杖

1. 移至包含構件或 Resource Manager 範本定義的 GitHub 存放庫首頁。
2. 選取 [複製或下載] 。
3. 若要將 URL 複製到剪貼簿，請選取 [HTTPS 複製 URL] 按鈕。 儲存 URL 以供稍後使用。
4. 選取 GitHub 右上角的設定檔影像，然後選取 [設定]。
5. 在 **個人設定**左側功能表，選取**開發人員設定**。
6. 選取 **個人存取權杖**左側功能表上。
7. 選取 [產生新的權杖] 。
8. 在 [新增個人存取權杖] 頁面上，於 [權杖描述] 底下輸入描述。 接受 [選取範圍] 底下的預設項目，然後選取 [產生權杖]。
9. 儲存產生的權杖。 您稍後會用到該權杖。
10. 關閉 GitHub。   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>取得 Azure Repos 複製 URL 和個人存取權杖
1. 移至您的小組集合首頁 (例如 https://contoso-web-team.visualstudio.com) ，然後選取專案。
2. 在專案首頁上，選取 [程式碼] 。
3. 若要檢視複製 URL，可在專案 [程式碼] 頁面上，選取 [複製]。
4. 儲存 URL。 您稍後會用到該 URL。
5. 若要建立個人存取權杖，請選取使用者帳戶下拉式功能表中的 [我的設定檔]。
6. 在 [設定檔資訊] 頁面上，選取 [安全性] 。
7. 在 [安全性] 索引標籤上，選取 [新增]。
8. 在 [建立個人存取權杖] 頁面上：
   1. 輸入權杖的 **描述** 。
   2. 在 [到期日] 清單中選取 [180 天]。
   3. 在 [帳戶] 清單中選取 [所有可存取的帳戶]。
   4. 選取 [所有範圍] 選項。
   5. 選擇 [建立權杖]。
9. 新的權杖會出現在 [個人存取權杖] 清單中。 選取 [複製權杖] ，然後儲存權杖值供稍後使用。
10. 繼續 連線您的實驗室的儲存機制 」 一節。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節提供在 Azure 入口網站中對實驗室新增構件儲存機制的步驟。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [更多服務]，然後從服務清單中選取 [DevTest Labs]。
3. 從實驗室清單中選取您的實驗室。 
4. 選取 **組態和原則**左側功能表上。
5. 選取**儲存機制**下方**外部資源**左側功能表上的一節。
6. 選取  **+ 新增**工具列上。

    ![[新增存放庫] 按鈕](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. 在 **存放庫**頁面上，指定下列資訊：
   1. **名稱**。 輸入存放庫的名稱。
   2. **Git Clone URL**。 輸入您先前從 GitHub 或 Azure DevOps Services 複製的 Git HTTPS 複製 URL。
   3. **分支**。 若要取得您的定義，請輸入分支。
   4. **個人存取權杖**。 輸入您先前從 GitHub 或 Azure DevOps Services 取得的個人存取權杖。
   5. **資料夾路徑**。 輸入至少與複製 URL 相關的一個資料夾路徑，其中包含構件或 Resource Manager 範本。 指定子目錄時，請確定您有在資料夾路徑中包含斜線。

        ![存放庫區域](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. 選取 [ **儲存**]。

## <a name="use-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本
Azure Resource Management (Azure Resource Manager) 範本是 JSON 檔案，描述您想要建立的 Azure 中的資源。 如需有關這些範本的詳細資訊，請參閱 <<c0> [ 編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。

本節提供使用 Azure Resource Manager 範本，將構件儲存機制加入實驗室的步驟。  如果不存在，此範本會建立實驗室。 

### <a name="template"></a>範本
這篇文章中所用的範例範本會透過參數收集下列資訊。 大多數的參數執行智慧型預設值，但是有幾個必須指定的值。 您必須指定 實驗室名稱，如 構件儲存機制的 URI 和儲存機制的安全性權杖。 

- 實驗室名稱。
- 構件儲存機制，在 DevTest Labs 使用者介面 (UI) 中的顯示名稱。 預設值是： `Team Repository`。
- 儲存機制的 URI (範例：`https://github.com/<myteam>/<nameofrepo>.git`或`"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`。
- 包含成品的存放庫中的分支。 預設值是： `master`。
- 包含成品的資料夾名稱。 預設值是： `/Artifacts`。
- 存放庫類型。 允許的值為`VsoGit`或`GitHub`。
- 存放庫的存取權杖。 

    ```json
    {
    
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>部署範本
有幾種方式將範本部署至 Azure，並已在資源建立，如果不存在，或更新，如果檔案存在。 如需詳細資訊，請參閱下列文章：

- [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/resource-group-template-deploy.md)
- [使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [使用 Resource Manager 範本與 Azure 入口網站來部署資源](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [使用 Resource Manager 範本和 Resource Manager REST API 部署資源](../azure-resource-manager/resource-group-template-deploy-rest.md)

我們繼續並了解如何部署在 PowerShell 中的範本。 用來部署範本的 Cmdlet 是特定內容，因此會使用目前的租用戶和目前的訂用帳戶。 使用[組 AzContext](/powershell/module/az.accounts/set-azcontext)之前部署此範本中，如有需要若要變更內容。

首先，使用下列方法建立資源群組[新增 AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)。 如果您想要已使用的資源群組存在，請略過此步驟。

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

接下來，建立使用您建立資源群組部署[新增 AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。 此 cmdlet 會適用於 Azure 資源的變更。 數個資源的部署可以對任何指定的資源群組。 如果您要多次部署相同的資源群組，請確定每個部署的名稱是唯一的。

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

新增 AzResourceGroupDeployment 順利執行之後，命令會輸出類似 (應該 succeeded) 的佈建狀態和任何的輸出，如需範本的重要資訊。
 
## <a name="use-azure-powershell"></a>使用 Azure PowerShell 
本節提供可用來將構件儲存機制加入實驗室的範例 PowerShell 指令碼。 如果您沒有 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0)如需詳細指示來安裝它。

### <a name="full-script"></a>完整指令碼
以下是完整的指令碼，包括一些詳細資訊訊息及註解：

**新 DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab. 

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>

 
[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',
    
    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,
    
    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"@

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"
 
Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>執行 PowerShell 指令碼
下列範例會示範如何執行指令碼： 

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>參數
這篇文章中的範例 PowerShell 指令碼會使用下列參數：

| 參數 | 描述 | 
| --------- | ----------- | 
| LabName | 實驗室名稱。 |
| ArtifactRepositoryName | 新的成品存放庫的名稱。 如果未指定，指令碼會建立存放庫的隨機名稱。 |
| ArtifactRepositoryDisplayName | 構件儲存機制的顯示名稱。 這是顯示在 Azure 入口網站的名稱 (https://portal.azure.com)時檢視實驗室的所有構件儲存機制。 |
| RepositoryUri | 儲存機制的 Uri。 範例：`https://github.com/<myteam>/<nameofrepo>.git`或`"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`。| 
| RepositoryBranch | 哪些成品中可以找到檔案的分支。 預設值為 'master'。 | 
| FolderPath | 可以找到成品所在的資料夾。 預設值為 '/' 成品 |
| PersonalAccessToken | 用於存取 GitHub 或 VSOGit 存放庫的安全性權杖。 請參閱 < 必要條件 > 一節的指示，以取得個人存取權杖 |
| SourceType | 成品是否 VSOGit 或 GitHub 存放庫。 |

存放庫本身需要內部名稱識別，也就是不同的 Azure 入口網站中所看到的顯示名稱。 您不會看到使用 Azure 入口網站的內部名稱，但您會看到它時使用 Azure REST Api 或 Azure PowerShell。 指令碼會提供名稱，如果沒有指定我們的指令碼的使用者。

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>指令碼中使用的 PowerShell 命令

| PowerShell 命令 | 注意 |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 此命令用來取得實驗室的詳細資料，例如其位置。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 沒有任何特定的命令，來新增構件儲存機制。 泛型[新增 AzResource](/powershell/module/az.resources/new-azresource)指令程式可以達到目的。 此 cmdlet 需要其中一個**ResourceId**或**ResourceName**並**ResourceType**知道要建立的資源類型的組。 此範例指令碼會使用的資源名稱和資源類型組。 <br/><br/>請注意，在相同的位置，並與實驗室位於相同資源群組下，您要建立成品儲存機制來源。|

指令碼會將新的資源加入至目前的訂用帳戶。 使用[Get AzContext](/powershell/module/az.accounts/get-azcontext)若要查看這項資訊。 使用[組 AzContext](/powershell/module/az.accounts/set-azcontext)設定目前的租用戶和訂用帳戶。

若要探索的資源名稱和資源類型資訊的最佳方式是使用[測試磁碟機 Azure REST Api](https://azure.github.io/projects/apis/)網站。 請參閱[DevTest Labs – 2016年-05-15](https://aka.ms/dtlrestapis)若要查看可用的 REST Api，DevTest Labs 提供者的提供者。 指令碼的使用者下的資源識別碼。 

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```
 
資源類型是所有項目列出之後 'providers' 在 URI 中，大括號中列出的項目除外。 資源名稱是在大括號中看到的所有項目。 如果多個項目應該做為資源名稱，每個項目使用斜線分隔像我們一樣。 

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>後續步驟
- [在 Azure DevTest Labs 中指定必要的成品，您的實驗室](devtest-lab-mandatory-artifacts.md)
- [為您的 DevTest Labs 虛擬機器建立自訂構件](devtest-lab-artifact-author.md)
- [在實驗室中的構件失敗進行診斷](devtest-lab-troubleshoot-artifact-failure.md)

