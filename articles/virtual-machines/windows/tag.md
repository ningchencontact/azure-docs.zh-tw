---
title: 如何標記 Azure 中的 Windows VM 資源 | Microsoft Docs
description: 了解如何標記 Azure 中使用資源管理員部署模型所建立的 Windows 虛擬機器
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 26ee777f7db05ca1850e2a01c1716810624906c0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709843"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>如何在 Azure 中標記 Windows 虛擬機器
本文說明在 Azure 中透過 Resource Manager 部署模型標記 Windows 虛擬機器的各種不同方式。 標記是使用者定義的成對「索引鍵/值」，可直接置於資源或資源群組。 Azure 目前對每一個資源和資源群組最多支援 15 個標記。 標記可在建立或加入至現有資源時置於資源上。 請注意，標記只支援透過 Resource Manager 部署模型建立的資源。 如果想要標記 Linux 虛擬機器，請參閱 [如何在 Azure 中標記 Linux 虛擬機器](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>使用 PowerShell 來標記
若要透過 PowerShell 建立、新增和刪除標記，您必須先設定 [PowerShell 環境與 Azure 資源管理員][PowerShell environment with Azure Resource Manager]。 完成設定之後，您可以在建立標記時或在透過 PowerShell 建立資源之後，將標記置於運算、網路和儲存體上。 本文將專注於檢視/編輯置於虛擬機器上的標記。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

首先，透過 `Get-AzVM` Cmdlet 瀏覽至虛擬機器。

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

如果虛擬機器已經包含標記，您則會看到資源上的所有標記：

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

如果要透過 PowerShell 新增標記，您可以使用 `Set-AzResource` 命令。 請注意，透過 PowerShell 標記更新時，標記會整體進行更新。 所以，如果您將一個標記新增至已有標記的資源，您必須包含想要置於資源上的所有標記。 以下是如何透過 PowerShell Cmdlet 將其他標記新增至資源的範例。

第一個 Cmdlet 會使用 `Get-AzResource` 和 `Tags` 屬性，將置於 MyTestVM  上的所有標記設為 $tags  變數。

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

第二個命令會顯示指定變數的標記。

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

第三個命令會將一個額外標記新增至 $tags  變數。 請注意，可使用 **+=** 將新的索引鍵/值組附加至 $tags  清單。

        PS C:\> $tags += @{Location="MyLocation"}

第四個命令會將 $tags  變數中定義的所有標記設定為指定的資源。 本例中是 MyTestVM。

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

第五個命令顯示資源上的所有標記。 如您所見，Location  現已定義為具有 MyLocation  值的標記。

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

若要深入了解如何透過 PowerShell 進行標記，請參閱 [Azure 資源 Cmdlet][Azure Resource Cmdlets]。

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>後續步驟
* 若要深入了解如何標記您的 Azure 資源，請參閱[Azure Resource Manager 概觀][Azure Resource Manager Overview] and [Using Tags to organize your Azure Resources][Using Tags to organize your Azure Resources]。
* 若要查看標記如何協助您管理 Azure 資源的使用，請參閱[了解 Azure 帳單][Understanding your Azure Bill] and [Gain insights into your Microsoft Azure resource consumption][Gain insights into your Microsoft Azure resource consumption]。

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
