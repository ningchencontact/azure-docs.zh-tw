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
ms.date: 02/05/2019
ms.author: spelluru
ms.openlocfilehash: ddda9ef2b9bb716f7cdd33aa8fe9233f6c7d8e82
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55748995"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中指定實驗室虛擬機器的資源群組
身為實驗室擁有者，您可以將實驗室虛擬機器設定在特定資源群組中建立。 使用此功能，您就不會達到 Azure 訂用帳戶的資源群組限制。 這項功能也可讓您將所有實驗室資源合併在單一資源群組中。 它也可簡化追蹤那些資源，並套用[原則](../governance/policy/overview.md)以在資源群組層級管理它們。

使用此功能，您可以使用指令碼為所有的實驗室 VM 指定 Azure 訂用帳戶內全新或現有的資源群組。 目前，DevTest Labs 透過 API 支援此功能。 

## <a name="api-to-configure-a-resource-group-for-labs-vms"></a>針對實驗室 VM 設定資源群組的 API
現在，讓我們逐步解說身為實驗室擁有者在使用此 API 時有哪些選項： 

- 您可以為所有虛擬機器選擇**實驗室的資源群組**。
- 您可以為所有虛擬機器選擇實驗室資源群組以外的**現有資源群組**。
- 您可以為所有虛擬機器輸入**新的資源群組**名稱。
- 您可以繼續使用現有的行為，也就是為實驗室中的每個 VM 建立資源群組。
 
此設定適用於實驗室中建立的新虛擬機器。 在您實驗室中以自身資源組建立的較舊 VM 會繼續運作而不受影響。 不過，您可以將這些虛擬機器從個別的資源群組移轉到通用資源群組，如此一來所有的實驗室虛擬機器都會在一個通用資源群組中。 如需詳細資訊，請參閱[將資源移至新的資源群組](../azure-resource-manager/resource-group-move-resources.md)。 在實驗室中建立的環境會繼續保留在自己的資源群組中。

### <a name="how-to-use-this-api"></a>如何使用此 API：
- 使用此 API 時，請使用 API **2018_10_15_preview** 版。 
- 如果您指定新的資源群組，確定您在訂用帳戶內擁有**對資源群組的寫入權限**。 若沒有寫入權限，在指定的資源群組中建立新的虛擬機器時會導致失敗。 
- 在使用 API 時，請傳入**完整的資源群組識別碼**。 例如： `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>` 。 確定資源群組與該實驗室位在相同的訂用帳戶中。 

## <a name="use-powershell"></a>使用 PowerShell 
下列範例說明如何使用 PowerShell 指令碼，在新的資源群組中建立所有的實驗室虛擬機器。

```PowerShell
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

使用下列命令叫用指令碼 (ResourceGroup.ps1 是包含上述指令碼的檔案)： 

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本
如果您使用 Azure Resource Manager 範本建立實驗室，請在 Resource Manager 範本中的實驗室屬性區段使用 **vmCreationResourceGroupId** 屬性，如下列範例所示：

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
                "uniqueIdentifier": "6e6f668f-992b-435c-bac3-d328b745cd25"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>後續步驟
請參閱下列文章： 

- [設定實驗室的原則](devtest-lab-get-started-with-lab-policies.md)
- [常見問題集](devtest-lab-faq.md)
