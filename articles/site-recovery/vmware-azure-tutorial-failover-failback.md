---
title: 在災害復原期間使用 Site Recovery 將 VMware VM 和實體伺服器容錯移轉及容錯回復至 Azure | Microsoft Docs
description: 了解如何在 Azure 的災害復原期間使用 Site Recovery 將 VMware VM 和實體伺服器容錯移轉至 Azure，以及如何容錯回復至內部部署網站。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7a089b3e4d7b8a38f2bf88c8ccf6e269331589be
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966291"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>容錯移轉和容錯回復 VMware VM

本文說明如何將內部部署 VMware 虛擬機器 (VM) 容錯移轉至 [Azure Site Recovery](site-recovery-overview.md)。

這是一系列中的第五個教學課程，說明如何為內部部署機器設定 Azure 的災害復原。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 確認 VMware VM 屬性符合 Azure 需求。
> * 執行容錯移轉至 Azure 的作業。

> [!NOTE]
> 這些教學課程示範案例的最簡單部署路徑。 在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 如果您想要詳細了解容錯移轉，請參閱[對 VM 和實體伺服器進行容錯移轉](site-recovery-failover.md)。

## <a name="before-you-start"></a>開始之前

完成先前的教學課程：

1. 確保您已[設定 Azure](tutorial-prepare-azure.md)，以便將內部部署 VMware VM、Hyper-V VM 和實體機器災害復原至 Azure。
2. 準備內部部署 [VMware](vmware-azure-tutorial-prepare-on-premises.md) 或 [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) 環境以便進行災害復原。 如果您要設定實體伺服器的災害復原，請檢閱[支援矩陣](vmware-physical-secondary-support-matrix.md)。
3. 為 [VMware VM](vmware-azure-tutorial.md)、[Hyper-V VM](hyper-v-azure-tutorial.md) 或[實體機器](physical-azure-disaster-recovery.md)設定災害復原。
4. 執行[災害復原演練](tutorial-dr-drill-azure.md)，以確定一切如預期般運作。

## <a name="failover-and-failback"></a>容錯移轉和容錯回復

容錯移轉和容錯回復有四個階段：

1. **容錯移轉至 Azure：** 當您的內部部署主要網站關閉時，請將機器容錯移轉至 Azure。 容錯移轉之後，系統會從複寫的資料建立 Azure VM。
2. **重新保護 Azure VM：** 在 Azure 中重新保護 Azure VM，使其開始複寫回內部部署 VMware VM。 內部部署 VM 會在重新保護期間關閉，以協助確保資料一致性。
3. **容錯移轉至內部部署：** 當您的內部部署網站已啟動並執行時，請執行容錯移轉以從 Azure 容錯回復。
4. **重新保護內部部署 VM：** 在資料容錯回復之後，請重新保護作為容錯回復目標的內部部署 VM，使其開始複寫至 Azure。

## <a name="verify-vm-properties"></a>驗證 VM 屬性

執行容錯移轉之前，請先檢查 VM 屬性以確定 VM 符合 [Azure 需求](vmware-physical-azure-support-matrix.md#replicated-machines)。

如下所示，驗證屬性：

1. 在 [受保護的項目]  中選取 [複寫的項目]  ，然後選取您要驗證的 VM。

2. 在 [複寫的項目]  窗格中，將會呈現 VM 資訊、健康情況狀態，以及最新可用復原點的摘要。 選取 [屬性]  以檢視詳細資料。

3. 在 [計算和網路]  中，您可以視需要修改下列屬性：
    * Azure 名稱
    * 資源群組
    * 目標大小
    * [可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)
    * 受控磁碟設定

4. 您可以檢視和修改網路設定，包括：

    * Azure VM 在容錯移轉之後所在的網路和子網路。
    * 將指派給 VM 的 IP 位址。

5. 在 [磁碟]  中，您可以看見 VM 上作業系統和資料磁碟的相關資訊。

## <a name="run-a-failover-to-azure"></a>執行容錯移轉到 Azure

1. 在 [設定]   > [複寫的項目]  中，選取要容錯移轉的 VM，然後選取 [容錯移轉]  。
2. 在 [容錯移轉]  中，選取要容錯移轉的目標**復原點**。 您可以使用下列其中一個選項：
   * **最新**：此選項會先處理所有傳送至 Site Recovery 的資料。 它會提供最低的復原點目標 (RPO)，因為在容錯移轉後建立的 Azure VM 具有在觸發容錯移轉時複寫到 Site Recovery 的所有資料。
   * **最近處理**：此選項會將 VM 容錯移轉至 Site Recovery 所處理的最新復原點。 此選項提供低 RTO (復原時間目標)，因為無須花費時間處理未處理的資料。
   * **最新應用程式一致**：此選項會將 VM 容錯移轉至 Site Recovery 所處理的最新應用程式一致復原點。
   * **自訂**：此選項可讓您指定復原點。

3. 選取 [先將機器關機再開始容錯移轉]  ，以嘗試在觸發容錯移轉之前將來源 VM 關機。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業]  頁面上追蹤容錯移轉進度。

在某些情況下，容錯移轉需要額外的處理，這大約需要 8 到 10 分鐘才會完成。 您可能會發現下列各項的測試容錯移轉時間比較久：

* 執行的行動服務版本早於 9.8 的 VMware VM。
* 實體伺服器。
* VMware Linux VM。
* 視同實體伺服器受到保護的 Hyper-V VM。
* 未啟用 DHCP 服務的 VMware VM。
* 沒有下列開機驅動程式的 VMware VM：storvsc、vmbus、storflt、intelide、atapi。

> [!WARNING]
> 請勿取消正在進行中的容錯移轉。 在啟動容錯移轉之前，已停止 VM 複寫。 如果您取消正在進行的容錯移轉，容錯移轉會停止，但 VM 不會再次複寫。

## <a name="connect-to-failed-over-vm"></a>連線至容錯移轉的 VM

1. 如果您在容錯移轉後想要使用遠端桌面通訊協定 (RDP) 和安全殼層 (SSH) 連線至 Azure VM，請[確認已符合需求](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。
2. 容錯移轉之後，請移至 VM 並[連線](../virtual-machines/windows/connect-logon.md)至該 VM 進行驗證。
3. 如果您想要在容錯移轉後使用不同的復原點，請使用 [變更復原點]  。 在下一個步驟中認可容錯移轉之後，將無法再使用此選項。
4. 在驗證之後，選取 [認可]  以完成容錯移轉後的 VM 復原點。
5. 在認可之後，系統會刪除所有其他可用的復原點。 此步驟會完成容錯移轉作業。

>[!TIP]
> 如果您在容錯移轉後遇到任何連線問題，請遵循[疑難排解指南](site-recovery-failover-to-azure-troubleshoot.md)。

## <a name="next-steps"></a>後續步驟

在容錯移轉之後，在內部部署環境中重新保護 Azure VM。 然後，在 VM 受到重新保護並複寫到內部部署網站後，就會在您準備就緒時從 Azure 容錯回復。

> [!div class="nextstepaction"]
> [重新保護 Azure VM](vmware-azure-reprotect.md)
> [從 Azure 容錯回復](vmware-azure-failback.md)
