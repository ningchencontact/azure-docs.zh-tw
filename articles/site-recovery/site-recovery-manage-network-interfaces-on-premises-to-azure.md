---
title: "管理 Azure Site Recovery 中的網路介面，內部部署至 Azure 的案例 |Microsoft 文件"
description: "描述如何管理內部部署與 Azure Site Recovery 的 Azure 案例的網路介面"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: manayar
ms.openlocfilehash: b60c746ae6c243d2b448056f8768df3baaed4cc1
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2017
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-to-azure-scenarios"></a>管理虛擬機器網路介面的內部部署至 Azure 的案例

Azure 中的虛擬機器必須至少一個網路介面連接到它，而且可以有許多網路介面附加到它為 VM 大小支援。 根據預設，第一個連接至 Azure 的虛擬機器的網路介面定義為主要網路介面。 虛擬機器中的所有其他網路介面都是次要網路介面。 根據預設，從虛擬機器的所有輸出流量會送出指派給主要 IP 設定的主要網路介面的 IP 位址。

在內部部署環境中，虛擬機器/伺服器可以有不同的網路環境中的多個網路介面。 不同的網路，通常用於執行特定作業，例如升級、 維護、 網際網路存取權等等。當移轉或容錯移轉到 Azure 從內部部署環境，請記住，在相同的虛擬機器的網路介面必須所有連接到相同虛擬網路。

根據預設，站台復原將會建立有許多網路在 Azure 虛擬機器上的介面為連線到內部部署伺服器。 您可以避免在移轉或容錯移轉期間建立備援網路介面，藉由編輯在複寫的虛擬機器的設定下的網路介面設定。

## <a name="select-the-target-network"></a>選取目標網路

VMware & 實體機器，以及 HYPER-V （無 VMM) 虛擬機器，您可以指定個別的虛擬機器的目標虛擬網路。 VMM 中的受管理的 HYPER-V 虛擬機器的[網路對應](site-recovery-network-mapping.md)用來對應來源 VMM 伺服器上的 VM 網路與目標 Azure 網路。

1. '複寫項目] 下 [復原服務保存庫中，按一下任何複寫的項目，來存取設定，該複寫的項目。

2. 按一下 「 運算和網路 」 存取複寫的項目的網路設定 索引標籤上。

3. 在 網路內容，選擇 從清單中可用的網路介面的虛擬網路。

    ![計算和網路](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

修改目標網路時，會影響該特定的虛擬機器的所有網路介面。

若為 VMM 雲端中，修改網路對應會影響所有虛擬機器和其網路介面。

## <a name="select-the-target-interface-type"></a>選取目標介面類型

'網路介面' 區段下的 '運算和網路' 刀鋒視窗中，您可以檢視和編輯網路介面設定，並指定目標的網路介面類型。

- A**主要**網路介面是必要的容錯移轉。
- 所有選取的網路介面，是**次要**網路介面。
- 選取**不要使用**来排除在容錯移轉建立的網路介面。

根據預設，當選取 啟用複寫，站台復原時全部已偵測到在內部部署伺服器上，將一個當做 'Primary'，而其他所有項目一個標記為 「 次要 」 的網路介面的網路介面。 在內部部署伺服器上，新增任何後續介面都會標示 '請勿使用' 預設值。 當新增更多的網路介面，確定選取正確的 Azure 虛擬機器目標大小為容納所有必要的網路介面。

## <a name="modifying-network-interface-settings"></a>修改網路介面設定

您可以修改子網路和 IP 的複寫的項目的網路介面。 如果未指定 IP，站台復原指派下一個可用的 IP 子網路容錯移轉的網路介面。

1. 按一下以開啟 [網路介面設定] 刀鋒視窗的任何可用的網路介面。

2. 從可用的子網路清單中選擇所需的子網路。

3. 輸入所需的 IP （視需要）。

    ![網路介面設定](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. 按一下 確定 完成 編輯並返回 '運算和網路' 刀鋒視窗中。

5. 重複步驟 1-4 的其他網路介面。

6. 按一下 [儲存] 以儲存所有變更。

## <a name="next-steps"></a>後續步驟
  [進一步了解](../virtual-network/virtual-network-network-interface-vm.md)有關 Azure 虛擬機器的網路介面。
