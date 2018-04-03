---
title: Azure 虛擬機器擴展集連結資料磁碟 | Microsoft Docs
description: 了解如何搭配使用連線資料磁碟與虛擬機器擴展集
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: ec11a2d66530129fb61d97681e6882b887c8654c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure 虛擬機器擴展集和連結的資料磁碟
若要擴充可用的儲存體，Azure [虛擬機器擴展集](/azure/virtual-machine-scale-sets/)支援 VM 執行個體連結資料磁碟。 您可以在擴展集建立時連結資料磁碟，或將資料磁碟連結至現有擴展集。

> [!NOTE]
>  當您建立具有連結資料磁碟的擴展集時，仍需掛接和格式化 VM 內的磁碟才能加以使用 (就如同獨立 Azure VM)。 完成此程序的方便作法是使用「自訂指令碼擴充功能」，該擴充功能可呼叫指令碼來分割及格式化 VM 上的所有資料磁碟。 如需此操作的範例，請參閱 [Azure CLI 2.0](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks)。


## <a name="create-and-manage-disks-in-a-scale-set"></a>建立及管理擴展集中的磁碟
如需深入了解如何建立具有連結資料磁碟的擴展集、準備和格式化，或新增和移除資料磁碟，請參閱下列其中一個教學課程：

- [Azure CLI 2.0](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

本文的其餘部分將概述特定使用案例，例如需要資料磁碟的 Service Fabric 叢集，或是將包含內容的現有資料磁碟連結至擴展集。


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>使用連結的資料磁碟建立 Service Fabric 叢集
在 Azure 中執行之 [Service Fabric](/azure/service-fabric) 叢集中的每個[節點類型](../service-fabric/service-fabric-cluster-nodetypes.md)都是由虛擬機器擴展集提供支援。  使用 Azure Resource Manager 範本，您可以將資料磁碟連結到構成 Service Fabric 叢集的擴展集。 您可使用[現有範本](https://github.com/Azure-Samples/service-fabric-cluster-templates)作為起點。 在範本中，於 Microsoft.Compute/virtualMachineScaleSets 資源的 storageProfile 中包含 dataDisks 區段並部署範本。 下列範例會連結 128 GB 資料磁碟：

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

您可以在部署叢集時自動分割、格式化及掛接資料磁碟。  將自訂指令碼擴充新增至擴展集之 virtualMachineProfile的 extensionProfile。

若要在 Windows 叢集中自動準備資料磁碟，請新增下列內容：

```json
{
    "name": "customScript",    
    "properties": {    
        "publisher": "Microsoft.Compute",    
        "type": "CustomScriptExtension",    
        "typeHandlerVersion": "1.8",    
        "autoUpgradeMinorVersion": true,    
        "settings": {    
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
若要在 Linux 叢集中自動準備資料磁碟，請新增下列內容：
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>將預先填入的資料磁碟新增至現存的擴展集 
> 當您將磁碟新增至現存的擴展集模型時，依照設計，一律會建立空白的磁碟。 此案例也會包含擴展集所建立的新執行個體。 這是因為擴展集定義具有空的資料磁碟。 若要針對現存的擴展集模型建立預先填入的資料磁碟機，您可以選擇下列其中一個選項：

* 藉由執行自訂指令碼，將資料從執行個體 0 VM 複製到其他 VM 中的資料磁碟。
* 建立具有 OS 磁碟再加上資料磁碟 (包含所需資料) 的受控映像，並使用此映像建立新的擴展集。 如此一來，每個新建立的 VM 都會有擴展集定義中提供的資料磁碟。 因為此定義參考的映像具有已自訂資料的資料磁碟，所以擴展集上的每部虛擬機器都會有這些變更。

> 在此可找到建立自訂映像的方式：[在 Azure 中建立一般化 VM 的受控映像](/azure/virtual-machines/windows/capture-image-resource/) 

> 使用者必須擷取具有所需資料的執行個體 0 VM，然後使用映像定義的該 vhd。


## <a name="additional-notes"></a>其他注意事項
API 版本 [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) 或更新版本的 Microsoft.Compute API 中提供 Azure 受控磁碟和擴展集連結資料磁碟的支援。

在擴展集連結磁碟支援的初始實作中，您無法對擴展集中的個別 VM 附加或卸離資料磁碟。

Azure 入口網站對於擴展集中連結資料磁碟的支援一開始就有限制。 視您的需求而定，您可以使用 Azure 範本、CLI、PowerShell、SDK 和 REST API 來管理連結磁碟。


