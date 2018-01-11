---
title: "使用 Site Recovery 將複寫的 Hyper-V VM 容錯移轉及容錯回復至 Azure | Microsoft Docs"
description: "了解如何使用 Site Recovery，將 Hyper-V VM 容錯移轉至 Azure，然後容錯回復至內部部署網站"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: 390fe98bc718da4fe07f580bbf1dcbffbf8fc1ba
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>將 Hyper-V VM 容錯移轉及容錯回復至 Azure

本教學課程說明如何將 Hyper-V VM 容錯移轉至 Azure。 在容錯移轉之後，您可以容錯回復至可用的內部部署網站。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 確認 HYPER-V 虛擬機器內容 來檢查符合 Azure 需求
> * 執行容錯移轉到 Azure
> * 將 Azure VM 放到內部部署網站重新保護
> * 從 Azure 容錯回復至內部部署
> * 重新保護內部部署 VM，再次開始複寫至 Azure

這是本系列的第五個教學課程。 本教學課程假設您已經完成了先前教學課程中的工作。

1. [準備 Azure](tutorial-prepare-azure.md)
2. [準備內部部署 VMware](tutorial-prepare-on-premises-hyper-v.md)
3. 設定災害復原[HYPER-V Vm](tutorial-hyper-v-to-azure.md)，或如[在 System Center VMM 雲端中管理的 HYPER-V Vm](tutorial-hyper-v-vmm-to-azure.md)
4. [執行災害復原演練](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>準備容錯移轉和容錯回復

請確認 VM 上有沒有快照集，並在內部部署 VM 期間重新保護已關閉。 這有助於確保資料在複寫期間的一致性。 重新保護完成後，請勿開啟 VM。 

容錯移轉和容錯回復有四個階段：

1. **容錯移轉至 Azure**：將機器從內部部署網站容錯移轉至 Azure。
2. **重新保護 Azure Vm**： 重新保護 Azure Vm 中，好讓它們開始複寫回內部部署 HYPER-V Vm。
3. **容錯移轉至內部部署**： 執行容錯移轉從 Azure，您在內部部署站台，可供使用。
4. **重新保護內部部署 Vm**： 資料回失敗之後，重新保護內部部署 Vm，才能開始複寫到 Azure。

## <a name="verify-vm-properties"></a>驗證 VM 屬性

驗證 VM 屬性，並確定 VM 符合 [Azure 需求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

1. 在**受保護項目**，按一下 **複寫的項目**>< VM 名稱 >。

2. 在**複寫項目** 窗格中，檢閱 VM 資訊、 健全狀況狀態，以及最新可用的復原點。 如需檢視詳細資訊，請按一下 [屬性]。
     - 在**計算與網路**，您可以修改 VM 設定，和網路設定，包括網路/子網路中的 Azure VM。 受管理的磁碟不支援從 Azure 容錯回復到 HYPER-V。
   即將放置在容錯移轉，並將指派給它的 IP 位址之後。
    - 在 [磁碟] 中，您可以看見 VM 上作業系統和資料磁碟的相關資訊。

## <a name="fail-over-to-azure"></a>容錯移轉至 Azure

1. 在 [設定] > [複寫的項目] 中，按一下 VM > [容錯移轉]。
2. 在**容錯移轉**選取**最新**復原點。 
3. 選取**機器關機再開始容錯移轉**。 站台復原會嘗試執行來源 Vm 關機之前觸發容錯移轉。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業] 頁面上追蹤容錯移轉進度。
4. 確認容錯移轉按一下之後**認可**。 這會刪除所有可用的復原點。

> [!WARNING]
> **不要取消正在進行的容錯移轉**：在啟動容錯移轉之前，已停止 VM 複寫。 如果您取消進行中，容錯移轉就會停止，但不會再次複寫 VM。

## <a name="reprotect-azure-vms"></a>重新保護 Azure VM

1. 在**AzureVMVault** > **複寫項目**，已容錯移轉，在 VM 上按一下滑鼠右鍵，然後選取**重新保護**。
2. 確認保護方向會設定為**Azure 到內部**。
3. 在內部部署 VM 關閉期間保護，以協助確保資料一致性。 不要啟動它之後重新保護已完成。
4. 在重新保護程序之後, 在 VM 開始複寫從 Azure 到內部部署站台。



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>從 Azure 容錯移轉，然後重新保護內部部署 VM

從 Azure 容錯移轉至內部部署網站，並開始將 VM 從內部部署網站複寫至 Azure。

1. 在**設定** > **複寫項目**，按一下 VM >**計劃的容錯移轉**。
2. 在 [確認規劃的容錯移轉] 中，確認容錯移轉方向 (從 Azure)，並選取來源和目標位置。
3. 選取**容錯移轉前同步處理資料 （同步處理僅差異變更）**。 此選項會降低 VM 停機時間，因為它會同步處理而不會關閉 VM。
4. 起始容錯移轉。 您可以在 [工作]  索引標籤上追蹤容錯移轉進度。
5. 初始資料之後進行同步處理，您準備好要關閉 Azure Vm 中，按一下**作業**> 計劃的容錯移轉作業的名稱 >**完成容錯移轉**。 這會將 Azure VM 關機、將最新變更轉送至內部部署，然後啟動內部部署 VM。
6. 登入內部部署 VM 以檢查它如預期般可用。
7. 在內部部署 VM 處於現在**認可擱置中**狀態。 按一下**認可**。 這會刪除 Azure VM 和它的磁碟，並準備內部部署 VM 來反轉複寫方向。
若要開始將內部部署 VM 複寫至 Azure，請啟用 [反向複寫]。 這會觸發複寫差異變更已關閉 Azure VM 之後發生。  
