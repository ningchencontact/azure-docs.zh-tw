---
title: Azure PowerShell 指令碼範例 - 變更 RDP 連接埠範圍 | Microsoft Docs
description: Azure PowerShell 指令碼範例 - 變更已部署之叢集的 RDP 連接埠範圍。
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
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
ms.openlocfilehash: 001574cef8f0ea074b65fdc94f6d8b255f98bff5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66159951"
---
# <a name="update-the-rdp-port-range-values"></a>更新 RDP 連接埠範圍值

此指令碼範例會在叢集已部署完成之後，變更叢集節點 VM 上的 RDP 連接埠範圍值。  為了讓基礎 VM 不會循環，所以使用 Azure PowerShell。  此指令碼會取得叢集之資源群組中的 `Microsoft.Network/loadBalancers` 資源，並更新 `inboundNatPools.frontendPortRangeStart` 和 `inboundNatPools.frontendPortRangeEnd` 值。 視需要自訂參數。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

您可以視需要使用 [Azure PowerShell 指南 (英文)](/powershell/azure/overview) 中的指示來安裝 Azure PowerShell。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 取得 `Microsoft.Network/loadBalancers` 資源。 |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|更新 `Microsoft.Network/loadBalancers` 資源。|

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure PowerShell 範例](../service-fabric-powershell-samples.md)中找到適用於 Azure Service Fabric 的其他 Azure PowerShell 範例。
