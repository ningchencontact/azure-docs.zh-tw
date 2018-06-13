---
title: 使用 Site Recovery 將複寫的 Hyper-V VM 容錯移轉及容錯回復至 Azure | Microsoft Docs
description: 了解如何使用 Site Recovery，將 Hyper-V VM 容錯移轉至 Azure，然後容錯回復至內部部署網站
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/8/2018
ms.author: raynew
ms.openlocfilehash: 7863feb29fbb04f643aa3b7e1984209f44cdbe9a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852892"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>將 Hyper-V VM 容錯移轉及容錯回復至 Azure

本教學課程說明如何將 Hyper-V VM 容錯移轉至 Azure。 在容錯移轉之後，您可以容錯回復至可用的內部部署網站。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 確認要檢查的 Hyper-V VM 屬性符合 Azure 需求
> * 執行容錯移轉到 Azure
> * 將 Azure VM 放到內部部署網站重新保護
> * 從 Azure 容錯回復至內部部署
> * 重新保護內部部署 VM，再次開始複寫至 Azure

這是本系列的第五個教學課程。 本教學課程假設您已經完成了先前教學課程中的工作。

1. [準備 Azure](tutorial-prepare-azure.md)
2. [準備內部部署 VMware](tutorial-prepare-on-premises-hyper-v.md)
3. 設定適用於 [Hyper-V VM](tutorial-hyper-v-to-azure.md)，或適用於 [System Center VMM 雲端中所管理的 Hyper-V VM](tutorial-hyper-v-vmm-to-azure.md) 的災害復原
4. [執行災害復原演練](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>準備容錯移轉和容錯回復

請確認 VM 上有沒有快照集，且在重新保護期間已將內部部署 VM 關閉。 這有助於確保資料在複寫期間的一致性。 重新保護完成後，請勿開啟 VM。 

容錯移轉和容錯回復有四個階段：

1. **容錯移轉至 Azure**：將機器從內部部署網站容錯移轉至 Azure。
2. **重新保護 Azure VM**：重新保護 Azure VM，使其開始複寫回到內部部署 Hyper-V VM。
3. **容錯移轉至內部部署**：當可用時，執行從 Azure 容錯移轉到您的內部部署網站。
4. **重新保護內部部署 VM**：在資料容錯回復之後，請重新保護內部部署 VM，開始將其複寫至 Azure。

## <a name="verify-vm-properties"></a>驗證 VM 屬性

驗證 VM 屬性，並確定 VM 符合 [Azure 需求](hyper-v-azure-support-matrix.md#replicated-vms)。

1. 在 [受保護的項目] 中，按一下 [複寫的項目] > <VM-name>。

2. 在 [複寫的項目] 窗格中，檢閱 VM 資訊、健康情況狀態，以及最新可用復原點。 如需檢視詳細資訊，請按一下 [屬性]。
     - 在 [計算與網路] 中，您可以修改 VM 設定和網路設定，包括 Azure VM 中的網路/子網路。 受控磁碟不支援從 Azure 容錯回復到 Hyper-V。
   將在容錯移轉之後找到，且會將 IP 位址指派給它。
    - 在 [磁碟] 中，您可以看見 VM 上作業系統和資料磁碟的相關資訊。

## <a name="fail-over-to-azure"></a>容錯移轉至 Azure

1. 在 [設定] > [複寫的項目] 中，按一下 VM > [容錯移轉]。
2. 在 [容錯移轉] 中，選取 [最新的] 復原點。 
3. 選取 [Shut down machine before beginning failover] \(先將機器關機再開始容錯移轉)。 Site Recovery 會嘗試先關閉來源 VM 後，再觸發容錯移轉。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業] 頁面上追蹤容錯移轉進度。
4. 驗證容錯移轉之後，請按一下 [認可]。 這會刪除所有可用的復原點。

> [!WARNING]
> **不要取消正在進行的容錯移轉**：在啟動容錯移轉之前，已停止 VM 複寫。 如果您在進行中取消，容錯移轉會停止，但 VM 不會再次複寫。

## <a name="reprotect-azure-vms"></a>重新保護 Azure VM

1. 在 [AzureVMVault] > [已複寫的項目] 中，以滑鼠右鍵按一下已容錯移轉的 VM，然後選取 [重新保護]。
2. 確認保護方向設定為 [Azure 到內部部署]。
3. 內部部署 VM 會在重新保護期間關閉，以協助確保資料一致性。 重新保護完成後，請勿將它開啟。
4. 在重新保護程序之後，在 VM 會開始從 Azure 複寫到內部部署網站。



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>從 Azure 容錯移轉，然後重新保護內部部署 VM

從 Azure 容錯移轉至內部部署網站，並開始將 VM 從內部部署網站複寫至 Azure。

1. 在 [設定] > [複寫的項目] 中，按一下 VM > [規劃的容錯移轉]。
2. 在 [確認規劃的容錯移轉] 中，確認容錯移轉方向 (從 Azure)，並選取來源和目標位置。
3. 選取 [在容錯移轉前同步處理資料 (僅同步處理差異變更)]。 此選項會降低 VM 停機時間，因為它會在不關閉 VM 的情況下同步處理。
4. 起始容錯移轉。 您可以在 [工作]  索引標籤上追蹤容錯移轉進度。
5. 在完成初始資料同步處理且您已經準備好關閉 Azure VM 之後，請按一下 [作業] > 規劃的容錯移轉作業名稱 > [完成容錯移轉]。 這會將 Azure VM 關機、將最新變更轉送至內部部署，然後啟動內部部署 VM。
6. 登入內部部署 VM 以檢查它如預期般可用。
7. 內部部署 VM 現在處於「認可擱置」狀態。 按一下 [認可]。 這會刪除 Azure VM 和它的磁碟，並準備內部部署 VM 來反轉複寫方向。
若要開始將內部部署 VM 複寫至 Azure，請啟用 [反向複寫]。 這會觸發複寫關閉 Azure VM 之後所發生的差異變更。  
