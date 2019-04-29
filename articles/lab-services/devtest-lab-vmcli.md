---
title: 使用 Azure CLI 在 DevTest Labs 中建立和管理虛擬機器 | Microsoft Docs
description: 了解如何使用 Azure CLI 在 Azure DevTest Labs 中建立及管理虛擬機器
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
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622315"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>使用 Azure CLI 在 DevTest Labs 中建立和管理虛擬機器
本快速入門會引導您建立、 啟動、 連線、 更新和清除測試環境中的開發電腦。 

開始之前：

* 若尚未建立實驗室，請參閱[這裡](devtest-lab-create-lab.md)的指示。

* [安裝 Azure CLI](/cli/azure/install-azure-cli)。 若要開始，請執行 az login 建立 Azure 連線。 

## <a name="create-and-verify-the-virtual-machine"></a>建立並確認虛擬機器 
在執行 DevTest Labs 相關的命令之前，請透過設定適當的 Azure 內容`az account set`命令：

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

建立虛擬機器的命令是： `az lab vm create`。 實驗室、 實驗室名稱和虛擬機器名稱的資源群組是所有必要的。 引數的其餘部分是根據虛擬機器的類型而有所不同。

下列命令會建立以 Windows 為基礎的映像從 Azure Market Place。 建立虛擬機器使用 Azure 入口網站時，您會看到映像的名稱都是相同的。 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

下列命令會建立虛擬機器，以在實驗室中，您可以使用自訂映像：

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**映像類型**引數已經從**圖庫**來**自訂**。 映像的名稱會符合您看到 Azure 入口網站中建立虛擬機器。

下列命令來建立 VM 的 marketplace 映像從 ssh 驗證：

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

您也可以建立公式為基礎所設定的虛擬機器**映像類型**參數來**公式**。 如果您需要選擇您的虛擬機器的特定虛擬網路，使用**vnet 名稱**並**子網路**參數。 如需詳細資訊，請參閱 < [az lab vm 建立](/cli/azure/lab/vm#az-lab-vm-create)。

## <a name="verify-that-the-vm-is-available"></a>確認有可用的 VM。
使用`az lab vm show`命令來確認 VM 是否可用，開始之前先連線到它。 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>啟動並連接至虛擬機器
下列範例命令會啟動 VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

連接到 VM:[SSH](../virtual-machines/linux/mac-create-ssh-keys.md)或是[遠端桌面](../virtual-machines/windows/connect-logon.md)。
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>更新虛擬機器
下列範例命令會套用至 VM 的構件：

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

列出實驗室中可用的構件。
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>停止並刪除虛擬機器    
下列範例命令會停止 VM。

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

刪除 VM。
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>後續步驟
請參閱下列內容：[Azure CLI 文件，適用於 Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest)。 