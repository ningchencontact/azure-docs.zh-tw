---
title: 在 DevTest Labs 中使用 Azure PowerShell 建立虛擬機器 |Microsoft Docs
description: 了解如何使用 Azure DevTest Labs 來建立和使用 Azure PowerShell 管理虛擬機器。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 0e68958070e9c35e12dd9446b351f880dfea6f69
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793244"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>使用 DevTest Labs 使用 Azure PowerShell 建立虛擬機器
這篇文章會示範如何使用 Azure PowerShell 建立 Azure DevTest Labs 中的虛擬機器。 若要自動建立在 Azure DevTest Labs 中對實驗室中的虛擬機器，您可以使用 PowerShell 指令碼。 

## <a name="prerequisites"></a>必要條件
開始之前：

- [建立實驗室](devtest-lab-create-lab.md)若不想使用現有的實驗室測試這篇文章中的指令碼或命令。 
- [安裝 Azure PowerShell](/powershell/azure/azurerm/other-install)或使用 Azure Cloud Shell 整合到 Azure 入口網站。 

## <a name="powershell-script"></a>PowerShell 指令碼
在本節中的範例指令碼會使用[Invoke-azurermresourceaction](/powershell/module/azurerm.resources/invoke-azurermresourceaction) cmdlet。  此 cmdlet 會採用實驗室的資源 ID、 執行動作的名稱 (`createEnvironment`)，以及所需的參數執行該動作。 所包含的所有虛擬機器描述屬性的雜湊表中的參數。 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzureRmContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzureRmResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzureRmResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzureRmResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzureRmResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

上述指令碼中的虛擬機器的屬性可讓我們建立的虛擬機器以做為作業系統的 Windows Server 2016 DataCenter。 對於每個虛擬機器類型，這些屬性會稍有不同。 [定義虛擬機器](#define-virtual-machine)區段會顯示如何判斷要使用此指令碼中的屬性。

下列命令會提供執行指令碼儲存在檔案名稱的範例：建立-LabVirtualMachine.ps1。 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>定義虛擬機器
本節將說明如何取得您想要建立的虛擬機器的類型所特有的屬性。 

### <a name="use-azure-portal"></a>使用 Azure 入口網站
您可以在 Azure 入口網站中建立 VM 時，產生的 Azure Resource Manager 範本。 您不需要完成建立 VM 的程序。 直到您看到範本，只需遵循的步驟。 這是最佳的方式取得所需的 JSON 描述，如果您還沒有建立 VM 的實驗室。 

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
2. 選取 **所有服務**左側的導覽功能表上。
3. 搜尋並選取**DevTest Labs**從服務清單。 
4. 在  **DevTest Labs**頁面上，在實驗室清單中選取您的實驗室。
5. 在您的實驗室的首頁上，選取 **+ 新增**工具列上。 
6. 選取 **基底映像**vm。 
7. 選取 [**自動化選項**底部的頁面上方**送出**] 按鈕。 
8. 您會看到**Azure Resource Manager 範本**建立虛擬機器。 
9. 中的 JSON 區段**資源**區段具有您先前選取的映像類型的定義。 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

在此範例中，您會看到如何取得 Azure Market Place 映像的定義。 您可以在相同的方式取得自訂映像、 公式或環境的定義。 新增所需的虛擬機器，任何成品，並設定任何所需的進階的設定。 之前選取 [必要的欄位，和任何選用的欄位提供值之後,**自動化選項**] 按鈕。

### <a name="use-azure-rest-api"></a>使用 Azure REST API
下列程序可讓您使用 REST API 取得的映像屬性的步驟：這些步驟只適用於在實驗室中現有的 VM。 

1. 瀏覽至[虛擬機器-列出](/rest/api/dtl/virtualmachines/list)頁面上，選取**試試** 按鈕。 
2. 選取您的 **Azure 訂用帳戶**。
3. 請輸入**實驗室的資源群組**。
4. 請輸入**的 實驗室名稱**。 
5. 選取 [執行]。
6. 您會看到**映像內容**根據建立 VM。 



## <a name="next-steps"></a>後續步驟
請參閱下列內容：[Azure DevTest labs 的 azure PowerShell 文件](/powershell/module/az.devtestlabs/)
