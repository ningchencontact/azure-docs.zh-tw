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
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: 94e5f5b29e93409df2373cf6c56e8185dc5373a2
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312969"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中指定實驗室虛擬機器的資源群組
身為實驗室擁有者，您可以將實驗室虛擬機器設定在特定資源群組中建立。 此功能可在下列情況中協助您： 

- 透過訂用帳戶中的實驗室建立的資源群組較少。
- 讓您的實驗室在您所設定的資源群組內操作
- 解決在 Azure 訂用帳戶內建立資源群組所需的限制和核准。
- 彙總單一資源群組中的所有實驗室資源，以簡化對這些資源的追蹤，並套用[原則](../governance/policy/overview.md)在資源群組層級對其進行管理。

使用此功能，您可以使用指令碼為所有的實驗室 VM 指定 Azure 訂用帳戶內全新或現有的資源群組。 目前，DevTest Labs 透過 API 支援此功能。 

## <a name="api-to-configure-a-resource-group-for-lab-virtual-machines"></a>針對實驗室虛擬機器設定資源群組的 API
現在，讓我們逐步解說身為實驗室擁有者在使用此 API 時有哪些選項： 

- 您可以為所有虛擬機器選擇**實驗室的資源群組**。
- 您可以為所有虛擬機器選擇實驗室資源群組以外的**現有資源群組**。
- 您可以為所有虛擬機器輸入**新的資源群組**名稱。
- 您可以繼續使用現有的行為，也就是為實驗室中的每個 VM 建立資源群組。
 
此設定適用於實驗室中建立的新虛擬機器。 在您實驗室中以自身資源組建立的較舊 VM 會繼續運作而不受影響。 在實驗室中建立的環境會繼續保留在自己的資源群組中。

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
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>後續步驟
請參閱下列文章： 

- [設定實驗室的原則](devtest-lab-get-started-with-lab-policies.md)
- [常見問題集](devtest-lab-faq.md)
