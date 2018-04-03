---
title: Azure PowerShell 範例 - 單一區域擴展集 | Microsoft Docs
description: Azure PowerShell 範例
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4e4f6d26c119a113bab0220828c847f438e7bc7a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>使用 PowerShell 建立單一區域虛擬機器擴展集
此指令碼會建立在單一可用性區域中執行 Windows Server 2016 的虛擬機器擴展集。 執行指令碼之後，您可以透過 RDP 存取虛擬機器。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1 "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>清除部署
執行下列命令來移除資源群組、擴展集和所有相關資源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | 建立可定義虛擬網路子網路的組態物件。 |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | 建立虛擬網路和子網路。 |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | 建立靜態公用 IP 位址。 |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | 建立可定義負載平衡器前端 (包括公用 IP 位址) 的組態物件。 |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | 建立可定義負載平衡器後端的組態物件。 |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | 建立以前端和後端組態物件為基礎的負載平衡器。 |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | 建立負載平衡器健康情況探查，以監視 TCP 連接埠 80 上的流量。 如果發生兩次間隔 15 秒的連續失敗，則會將端點視為狀況不良。 |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | 建立可定義負載平衡器規則的組態物件，將 TCP 連接埠 80 上的流量從前端集區散發到後端集區。 |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | 使用健康情況探查和負載平衡器規則來更新負載平衡器。 |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | 建立可定義網路安全性群組規則的組態物件，以允許 TCP 連接埠 80 上的流量。 |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | 建立網路安全性群組和規則。 |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | 更新虛擬網路子網路，以便與網路安全性群組相關聯。 任何連線至子網路的 VM 執行個體都會繼承網路安全性群組規則。 |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | 使用子網路和網路安全性群組組態來更新虛擬網路。 |
| [New-AzureRmVmssIpConfig](/powershell/module/azurerm.compute/new-azurermvmssipconfig) | 建立可定義虛擬機器擴展集 IP 位址資訊的組態物件，以將 VM 執行個體連線到負載平衡器後端集區與輸入的 NAT 集區。
| [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) | 建立可定義擴展集中 VM 執行個體數目、VM 執行個體大小和升級原則模式的組態物件。 |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | 建立可定義 VM 映像的組態物件，以用於 VM 執行個體。 |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | 建立可定義 VM 執行個體名稱和使用者認證的組態設定檔。 |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | 建立可定義每個 VM 執行個體之虛擬網路介面卡的組態物件。 |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | 結合所有組態物件以建立虛擬機器擴展集。 |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | 取得虛擬機器擴展集的相關資訊。 |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | 新增自訂指令碼的 VM 擴充來安裝基本 Web 應用程式。 |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | 更新虛擬機器擴展集模型以套用 VM 擴充。 |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | 取得指派給負載平衡器使用的公用 IP 位址資訊。 |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 移除資源群組及其內含的所有資源。 |

## <a name="next-steps"></a>後續步驟
如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure 虛擬機器擴展集文件](../powershell-samples.md)中找到其他的虛擬機器擴展集 PowerShell 指令碼範例。