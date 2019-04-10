---
title: 在 Azure Site Recovery 中對應兩個 Azure 區域間的虛擬網路 | Microsoft Docs
description: Azure Site Recovery 可協調虛擬機器和實體伺服器的複寫、容錯移轉及復原作業。 了解如何容錯移轉到 Azure 或次要資料中心。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: ecaabe0cf2e9e55bf02f8e12244d55fc2bef830b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359821"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>為 VNet 設定網路對應和 IP 位址

本文說明如何彼此對應位於不同 Azure 區域之 Azure 虛擬網路 (VNet) 的兩個執行個體，以及如何設定網路之間的 IP 位址。 網路對應會提供目標網路選取項目在啟用複寫時，根據來源網路中的預設行為。

## <a name="prerequisites"></a>必要條件

在網路對應之前，您應該在來源和目標 Azure 區域中擁有 [Azure VNet](../virtual-network/virtual-networks-overview.md)。 

## <a name="set-up-network-mapping-manually-optional"></a>設定網路對應以手動方式 （選擇性）

對應網路，如下所示：

1. 在 **Site Recovery 基礎架構**中，按一下 [+網路對應]。

    ![ 建立網路對應](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. 在 [新增網路對應] 中，選取來源和目標位置。 在我們的範例中，來源 VM 在東亞地區中執行，且會複寫至東南亞地區。

    ![選取來源和目標](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. 現在在相反的目錄中建立網路對應。 在我們的範例中，來源現在將是東南亞，目標將是東亞。

    ![[新增網路對應] 窗格 - 選取目標網路的來源和目標位置](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>啟用複寫時對應網路

如果在為 Azure VM 設定災害復原之前尚未準備網路對應，則可以在[設定並啟用複寫](azure-to-azure-how-to-enable-replication.md)時指定目標網路。 執行此動作時，會發生下列情況：

- Site Recovery 會根據您選取的目標，自動建立從來源到目標區域以及從目標到來源區域的網路對應。
- 根據預設，Site Recovery 會在目標區域中建立與來源網路完全相同的網路。 Site Recovery 會新增 **-asr** 作為來源網路的名稱尾碼。 您可以自訂目標頁面。
- 如果已經發生來源網路的網路對應，對應的目標網路一律為預設值時啟用更多的 vm 的複寫。 您可以選擇從下拉式清單中選擇其他可用的選項來變更目標虛擬網路。 
- 若要變更新複寫的預設目標虛擬網路，您需要修改現有的網路對應。
- 如果您想要修改從區域 A 到區域 B 的網路對應，請確定您先刪除網路對應從區域 B 到區域 a。在反向對應刪除後，請修改從區域 A 到區域 B 的網路對應，然後再建立 相關的反向對應。

>[!NOTE]
>* 修改網路對應時，才會變更新的 VM 複寫的預設值。 它不會影響現有的複寫的目標虛擬網路選取項目。 
>* 如果您想要修改現有的複寫的目標網路，請移至計算和網路設定的複寫的項目。

## <a name="specify-a-subnet"></a>指定子網路

目標 VM 的子網路會根據來源 VM 的子網路名稱來選取。

- 如果目標網路中可用的子網路具備與來源 VM 子網路相同的名稱，則會針對目標 VM 設定該子網路。
- 如果目標網路中沒有相同名稱的子網路，則會依字母順序設定第一個子網路作為目標子網路。
- 執行此操作時，您可以修改 VM 的一個**計算與網路**設定。

    ![[計算與網路] 計算屬性視窗](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>設定目標 VM 的 IP 位址

目標虛擬機器上每個 NIC 的 IP 位址設定，如下所示：

- **DHCP**：如果來源 VM 的 NIC 使用 DHCP，則目標 VM 的 NIC 也會設定為使用 DHCP。
- **靜態 IP 位址**：如果來源 VM 的 NIC 使用靜態 IP 位址，則目標 VM NIC 也會使用靜態 IP 位址。


## <a name="ip-address-assignment-during-failover"></a>容錯移轉期間的 IP 位址指派

**來源和目標子網路** | **詳細資料**
--- | ---
相同的位址空間 | 來源 VM NIC 的 IP 位址設為目標 VM 的 NIC IP 位址。<br/><br/> 如果該位址無法使用，則會設定下一個可用的 IP 位址作為目標。

不同的位址空間<br/><br/> 目標子網路中的下一個可用 IP 位址將設為目標 VM NIC 位址。



## <a name="ip-address-assignment-during-test-failover"></a>測試容錯移轉期間的 IP 位址指派

**目標網路** | **詳細資料**
--- | ---
目標網路是容錯移轉 VNet | - 目標 IP 位址是靜態的，但與為容錯移轉保留的 IP 位址不同。<br/><br/>  - 指派的 IP 位址是從子網路範圍結尾開始的下一個可用位址。<br/><br/> 例如︰如果來源 IP 位址為 10.0.0.19 且容錯移轉網路使用範圍 10.0.0.0/24，則指派給目標 VM 的下一個 IP 位址為 10.0.0.254。
目標網路不是容錯移轉 VNet | - 目標 IP 位址將是靜態的，具有為容錯移轉保留的相同 IP 位址。<br/><br/>  - 如果已指派相同的 IP 位址，則 IP 位址是子網路範圍的下一個可用 IP 位址。<br/><br/> 例如︰如果來源靜態 IP 位址為 10.0.0.19 且容錯移轉位於不是容錯移轉網路的網路上 (範圍為 10.0.0.0/24)，則目標靜態 IP 位址將為 10.0.0.0.19 (如果可用)，否則將是 10.0.0.254。

- 容錯移轉 VNet 是您在設定災害復原時選取的目標網路。
- 我們建議您一律使用非實際執行網路進行測試容錯移轉。
- 您可以在 VM 的**計算與網路**設定中修改目標 IP 位址。


## <a name="next-steps"></a>後續步驟

- 檢閱 Azure VM 災害復原的[網路指引](site-recovery-azure-to-azure-networking-guidance.md)。
- [深入了解](site-recovery-retain-ip-azure-vm-failover.md)有關在容錯移轉之後保留 IP 位址。

如果選擇的目標網路是容錯移轉 vnet，且第 2 點說「如果選擇的目標網路與容錯移轉 vnet 不同但具有與容錯移轉 vnet 相同的子網路範圍」