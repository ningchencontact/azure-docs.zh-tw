---
title: 加入、變更或刪除 Azure 虛擬網路子網路 | Microsoft Docs
description: 了解如何加入、變更或刪除 Azure 中的虛擬網路子網路。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 16ce5aac26abcf2ef2cf7664fb0b9aae600708d4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>加入、變更或刪除虛擬網路子網路

了解如何加入、變更或刪除虛擬網路子網路。 如果您不熟悉虛擬網路，建議在加入、變更或刪除子網路之前，先閱讀 [Azure 虛擬網路概觀](virtual-networks-overview.md)和[建立、變更或刪除虛擬網路](manage-virtual-network.md)。 所有部署到虛擬網路的 Azure 資源都會部署到虛擬網路內的子網路。
 
## <a name="before-you-begin"></a>開始之前

在完成本文任一節的步驟之前，請先完成下列工作︰

- 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果使用入口網站，請開啟 https://portal.azure.com，並使用您的 Azure 帳戶來登入。
- 如果使用 PowerShell 命令來完成這篇文章中的工作，請在 [Azure Cloud Shell](https://shell.azure.com/powershell) \(英文\) 中執行命令，或從您的電腦執行 PowerShell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 本教學課程需要 Azure PowerShell 模組 5.2.0 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzureRmAccount` 以建立與 Azure 的連線。
- 如果使用命令列介面 (CLI) 命令來完成這篇文章中的工作，請在 [Azure Cloud Shell](https://shell.azure.com/bash) \(英文\) 中執行命令，或從您的電腦執行 CLI。 本教學課程需要 Azure CLI 2.0.26 版或更新版本。 執行 `az --version` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。 如果您在本機執行 Azure CLI，則也需要執行 `az login` 以建立與 Azure 的連線。

## <a name="add-a-subnet"></a>新增子網路

1. 在入口網站頂端的搜尋方塊中輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時加以選取。
2. 從虛擬網路清單中，選取您要新增子網路的虛擬網路。
3. 在 [設定] 底下，選取 [子網路]。
4. 選取 [+ 子網路]。
5. 為下列參數輸入值︰
    - **名稱**：此名稱必須是虛擬網路中的唯一名稱。
    - **位址範圍**：此範圍必須是虛擬網路位址空間內的唯一範圍。 此範圍不能與虛擬網路內的其他子網路位址範圍重疊。 位址空間必須以「無類別網域間路由選擇」(CIDR) 標記法來指定。 例如，在位址空間為 10.0.0.0/16 的虛擬網路中，您可以定義 10.0.0.0/24 的子網路位址空間。 您可以指定的最小範圍是 /29，此範圍可提供八個 IP 位址供子網路使用。 為了符合通訊協定的規定，Azure 會保留每個子網路中的第一個和最後一個位址。 Azure 還會保留三個位址供 Azure 服務使用。 因此，以 /29 位址範圍所定義的子網路會在子網路中產生三個可用的 IP 位址。 如果您打算將虛擬網路連線至 VPN 閘道，則必須建立一個閘道子網路。 深入了解[閘道子網路位址範圍的具體考量](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。 若符合特定條件，您可以在子網路加入後變更其位址範圍。 若要了解如何變更子網路的位址範圍，請參閱[變更子網路設定](#change-subnet-settings)。
    - **網路安全性群組**：您可以將零個或一個現有網路安全性群組與子網路建立關聯，以篩選子網路的輸入和輸出網路流量。 網路安全性群組必須與虛擬網路位於相同的訂用帳戶和位置當中。 深入了解[網路安全性群組](security-overview.md)與[如何建立網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)。
    - **路由表︰**您可以將零個或一個現有路由表和子網路建立關聯，以控制路由至其他網路的網路流量。 路由表必須與虛擬網路位於相同的訂用帳戶和位置當中。 深入了解 [Azure 路由](virtual-networks-udr-overview.md)與[如何建立路由表](tutorial-create-route-table-portal.md)
    - **服務端點：**您可為子網路啟用零個或多個服務端點。 若要啟用服務的服務端點，請選取服務或從 [服務] 清單中選取您想要啟用服務端點的服務。 若要移除服務端點，請取消選取您想要移除其服務端點的服務。 若要深入了解服務端點，請參閱[虛擬網路服務端點概觀](virtual-network-service-endpoints-overview.md)。 一旦您啟用服務的服務端點，您也必須為以服務建立的資源啟用子網路的網路存取權。 例如，如果您啟用 *Microsoft.Storage* 的服務端點，您也必須對想要授與網路存取權的所有 Azure 儲存體帳戶啟用網路存取權。 如需如何為已啟用服務端點的子網路啟用網路存取權的詳細資訊，請參閱您啟用其服務端點之個別服務的服務文件。
6. 若要在所選虛擬網路中新增子網路，請選取 [確定]。

**命令**

- Azure CLI：[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- PowerShell：[Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>變更子網路設定

1. 在入口網站頂端的搜尋方塊中輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時加以選取。
2. 在虛擬網路清單中，選取您想要檢視設定且包含子網路的虛擬網路。
3. 在 [設定] 底下，選取 [子網路]。
4. 在子網路清單中，選取您想要變更設定的子網路。 您可以變更下列設定：

    - **位址範圍：**如果子網路內沒有部署資源，您可以變更位址範圍。 如果子網路中有任何資源存在，您必須將資源移至另一個子網路，或先從子網路中刪除。 不同資源的資源移動或刪除步驟也各異。 若要了解如何移動或刪除子網路中的資源，請閱讀您想要移動或刪除之各個資源類型的適用文件。 如需**位址範圍**的條件約束，請參閱步驟 5 的[新增子網路](#add-a-subnet)。
    - **使用者**︰您可以使用內建角色或自有的自訂角色來控制子網路的存取。 若要深入了解如何指派角色和使用者以存取子網路，請參閱[使用角色指派來管理 Azure 資源的存取權](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access)。
    - 如需變更**網路安全性群組**、**路由表**、**使用者**及**服務端點**的詳細資訊，請參閱步驟 5 中的[新增子網路](#add-a-subnet)。
5. 選取 [儲存]。

**命令**

- Azure CLI：[az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell：[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>刪除子網路

您只能刪除未包含任何資源的子網路。 如果子網路中含有資源，您必須先刪除其中的資源才能將它刪除。 不同資源的資源刪除步驟也各異。 若要了解如何刪除子網路中的資源，請閱讀您想要刪除之各個資源類型的適用文件。

1. 在入口網站頂端的搜尋方塊中輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時加以選取。
2. 在虛擬網路清單中，選取虛擬網路，而其中包含您想要刪除的子網路。
3. 在 [設定] 底下，選取 [子網路]。
4. 在子網路清單中，在您想要刪除的子網路右側，選取 [...]
5. 選取 [刪除]，然後選取 [是]。

**命令**

- Azure CLI：[az network vnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell：[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>權限

若要針對子網路執行工作，您的帳戶必須指派為[網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或為已指派下表所列適當權限的[自訂](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

|作業                                                                |   作業名稱                               |
|-----------------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   取得虛擬網路子網路                   |
|Microsoft.Network/virtualNetworks/subnets/write                          |   建立或更新虛擬網路子網路      |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   刪除虛擬網路子網路                |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   加入虛擬網路                         |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   將服務加入子網路                     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   取得虛擬網路子網路虛擬機器  |
