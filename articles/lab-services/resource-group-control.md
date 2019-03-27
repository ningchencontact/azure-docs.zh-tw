---
title: 在 Azure DevTest Labs 中指定 VM 的資源群組 | Microsoft Docs
description: 了解如何在 Azure DevTest Labs 中指定實驗室內 VM 的資源群組。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: 1001e6aec7ba2f6ce62eb267d218149296048bb9
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485878"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中指定實驗室虛擬機器的資源群組

身為實驗室擁有者，您可以將實驗室虛擬機器設定在特定資源群組中建立。 此功能可在下列情況中協助您：

- 透過訂用帳戶中的實驗室建立的資源群組較少。
- 有一組固定的您所設定的資源群組內操作實驗室。
- 解決在 Azure 訂用帳戶內建立資源群組所需的限制和核准。
- 彙總內的單一資源群組，以簡化追蹤這些資源和套用所有實驗室資源[原則](../governance/policy/overview.md)來管理資源群組層級的資源。

使用此功能，您可以使用指令碼來指定您 Azure 訂用帳戶內的新的或現有的資源群組，為所有實驗室 Vm。 目前，Azure DevTest Labs 支援透過 API 這項功能。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
請遵循下列步驟來指定適用於實驗室中建立的所有 Vm 的資源群組。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 **所有服務**左側的導覽功能表上。 
3. 選取  **DevTest Labs**從清單中。
4. 從實驗室清單中，選取您**實驗室**。  
5. 選取 **組態和原則**中**設定**左側功能表上的一節。 
6. 選取 **實驗室設定**左側功能表上。 
7. 選取 **一個資源群組中的所有虛擬機器**。 
8. 選取現有的資源群組中的下拉式清單 （或） 選取**新建**，輸入**名稱**資源群組，然後選取**確定**。 

    ![選取的資源群組的所有實驗室 Vm](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>使用 PowerShell 
下列範例示範如何使用 PowerShell 指令碼來建立新的資源群組中的所有實驗室虛擬機器。

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

使用下列命令來叫用指令碼。 ResourceGroup.ps1 是包含上述指令碼的檔案：

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本
如果您使用 Azure Resource Manager 範本建立實驗室，使用**vmCreationResourceGroupId**實驗室屬性區段中的範本，如下列範例所示的屬性：

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>設定實驗室 Vm 的資源群組的 API
使用此 API 時，您可以有為實驗室擁有者的下列選項：

- 選擇**實驗室的資源群組**的所有虛擬機器。
- 選擇**現有的資源群組**以外的所有虛擬機器的實驗室的資源群組。
- 請輸入**新的資源群組**所有虛擬機器的名稱。
- 繼續使用現有的行為，在實驗室中的每個 VM 會建立資源群組。
 
此設定適用於實驗室中建立的新虛擬機器。 在自己的資源群組中建立較舊的 Vm 在實驗室中不受影響。 在您的實驗室中建立的環境會繼續保留在自己的資源群組中。

如何使用此 API：
- 使用 API 版本**2018_10_15_preview**。
- 如果您指定新的資源群組，請確定您已**資源群組寫入權限**您訂用帳戶中。 如果您沒有寫入權限，在指定的資源群組中建立新的虛擬機器將會失敗。
- 在使用 API 時，請傳入**完整的資源群組識別碼**。 例如： `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>` 。 請確定資源群組是實驗室位於相同訂用帳戶。 


## <a name="next-steps"></a>後續步驟
請參閱下列文章： 

- [設定實驗室的原則](devtest-lab-get-started-with-lab-policies.md)
- [常見問題集](devtest-lab-faq.md)
