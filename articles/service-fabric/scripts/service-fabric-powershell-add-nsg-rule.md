---
title: Azure PowerShell 指令碼範例 - 新增網路安全性群組規則 | Microsoft Docs
description: Azure PowerShell 指令碼範例 - 新增網路安全性群組，以便允許特定連接埠上的輸入流量。
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
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 92efd6c4e63959b6098829892c5cbbaeeabe9f83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66159988"
---
# <a name="add-an-inbound-network-security-group-rule"></a>新增輸入網路安全性群組規則

本範例指令碼會建立網路安全性群組規則，以允許連接埠 8081 上的輸入流量。  指令碼會取得叢集所在的 `Microsoft.Network/networkSecurityGroups` 資源、建立新的網路安全性設定規則，並更新網路安全性群組。 視需要自訂參數。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/overview)中的指示來安裝 Azure PowerShell。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 取得 `Microsoft.Network/networkSecurityGroups` 資源。 |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| 依名稱取得網路安全性群組。|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 將網路安全性規則設定新增到網路安全性群組中。 |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| 設定網路安全性群組的目標狀態。|

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。
