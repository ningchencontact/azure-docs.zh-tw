---
title: Azure PowerShell 指令碼範例 - 更新 RDP 使用者名稱和密碼 | Microsoft Docs
description: Azure PowerShell 指令碼範例 - 針對特定節點類型的所有 Service Fabric 叢集節點，更新 RDP 使用者名稱和密碼。
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: a5c1e7786f02b7ebc42f96e4ed12b2fb5729bb41
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498004"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>為叢集中的 VM 更新管理員使用者名稱和密碼

Service Fabric 叢集中的每個[節點類型](../service-fabric-cluster-nodetypes.md)都是一個虛擬機器擴展集。 此指令碼範例會針對特定節點類型的叢集虛擬機器，更新管理員使用者名稱和密碼。  管理員密碼是不可修改的擴展集屬性，因此請在擴展集內新增 VMAccessAgent 擴充功能。  使用者名稱和密碼的變更會套用至擴展集內的所有節點。 視需要自訂參數。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

您可以視需要使用 [Azure PowerShell 指南 (英文)](/powershell/azure/overview) 中的指示來安裝 Azure PowerShell。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令：下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | 取得叢集節點類型 (虛擬機器擴展集) 的屬性。   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| 在虛擬機器擴展集內新增擴充功能。|
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss)|將虛擬機器擴展集的狀態更新為本機 VMSS 物件的狀態。|

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure PowerShell 範例](../service-fabric-powershell-samples.md)中找到適用於 Azure Service Fabric 的其他 Azure PowerShell 範例。
