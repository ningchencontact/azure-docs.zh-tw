---
title: "使用 Azure Site Recovery 將內部部署機器移轉至 Azure | Microsoft Docs"
description: "本文說明如何使用 Azure Site Recovery 將內部部署機器移轉至 Azure。"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/18/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 406f0890da1ef4123b16082e7371d67f6328ea2c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="migrate-on-premises-machines-to-azure"></a>將內部部署機器移轉至 Azure

除了針對商務持續性和災害復原 (BCDR) 使用 [Azure Site Recovery](site-recovery-overview.md) 服務來管理和協調內部部署電腦和 Azure VM 的災害復原之外，您也可以使用 Site Recovery 來管理內部部署機器到 Azure 的移轉。


本教學課程說明如何將內部部署 VM 和實體伺服器移轉至 Azure。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 選取複寫目標
> * 設定來源和目標環境
> * 設定複寫原則
> * 啟用複寫
> * 執行測試移轉，確定一切都沒問題
> * 執行一次性容錯移轉至 Azure

這是本系列的第三個教學課程。 本教學課程假設您已經完成了先前教學課程中的工作：

1. [準備 Azure](tutorial-prepare-azure.md)
2. 準備內部部署 [VMware](tutorial-prepare-on-premises-vmware.md) 或 Hyper-V 伺服器。

開始之前，最好先針對災害復原檢閱 [VMware](concepts-vmware-to-azure-architecture.md) 或 [Hyper-V](concepts-hyper-v-to-azure-architecture.md) 架構。


## <a name="prerequisites"></a>先決條件

不支援並行虛擬驅動程式所匯出的裝置。


## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

1. 登入 [Azure 入口網站](https://portal.azure.com) > [復原服務]。
2. 按一下 [建立群組] > [監視和管理] > [備份和 Site Recovery]。
3. 在 [名稱] 中，指定易記名稱 [ContosoVMVault]。 如果您有多個訂用帳戶，請選取適當的一個。
4. 建立資源群組 **ContosoRG**。
5. 指定 Azure 區域。 若要查看支援的區域，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
6. 若要從儀表板快速存取保存庫，請按一下 [釘選到儀表板]，然後按一下 [建立]。

   ![新增保存庫](./media/tutorial-migrate-on-premises-to-azure/onprem-to-azure-vault.png)

新的保存庫會新增到主要 [復原服務保存庫] 頁面上 [所有資源] 之下的 [儀表板]。



## <a name="select-a-replication-goal"></a>選取複寫目標

選取您要複寫的項目以及您要複寫到的位置。
1. 按一下 [復原服務保存庫] > 保存庫。
2. 在 [資源功能表] 中，按一下 [Site Recovery] > [準備基礎結構] > [保護目標]。
3. 在 [保護目標] 中，選取您需要移轉的項目。
    - **VMware**：選取 [至 Azure] > [es, with VMWare vSphere Hypervisor] \(是，使用 VMware vSphere Hypervisor)。
    - **實體機器**：選取 [至 Azure] > [未虛擬化/其他]。
    - **Hyper-V**：選取 [至 Azure] > [Yes, with Hyper-V] \(是，使用 Hyper-V)。 如果 Hyper-V VM 是由 VMM 管理，請選取 [是]。


## <a name="set-up-the-source-environment"></a>設定來源環境

- [設定](tutorial-vmware-to-azure.md#set-up-the-source-environment) VMware VM 的來源環境。
- [設定](tutorial-physical-to-azure.md#set-up-the-source-environment)實體伺服器的來源環境。
- [設定](hyper-v-azure-tutorial.md#set-up-the-source-environment) Hyper-V VM 的來源環境。

## <a name="set-up-the-target-environment"></a>設定目標環境

選取並確認目標資源。

1. 按一下 [準備基礎結構] > [目標]，然後選取您要使用的 Azure 訂用帳戶。
2. 指定 Resource Manager 部署模型。
3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

## <a name="set-up-a-replication-policy"></a>設定複寫原則

- [設定 VMware VM 的複寫原則](tutorial-vmware-to-azure.md#create-a-replication-policy)。
- [設定實體伺服器的複寫原則](tutorial-physical-to-azure.md#create-a-replication-policy)。
- [設定 Hyper-V VM 的複寫原則](hyper-v-azure-tutorial.md#set-up-a-replication-policy)。


## <a name="enable-replication"></a>啟用複寫

- [啟用 VMware VM 的複寫](tutorial-vmware-to-azure.md#enable-replication)。
- [啟用實體伺服器的複寫功能](tutorial-physical-to-azure.md#enable-replication)。
- [啟用 Hyper-V VM 的複寫](hyper-v-azure-tutorial.md#enable-replication)。


## <a name="run-a-test-migration"></a>執行測試移轉

執行[測試容錯移轉](tutorial-dr-drill-azure.md)至 Azure，確定一切都沒問題。


## <a name="migrate-to-azure"></a>移轉至 Azure

針對您要移轉的機器執行容錯移轉。

1. 在 [設定] > [複寫的項目] 中，按一下機器 > [容錯移轉]。
2. 在 [容錯移轉] 中，選取要容錯移轉的目標**復原點**。 選取最新的復原點。
3. 加密金鑰設定不適用於此案例。
4. 選取 [Shut down machine before beginning failover] \(先將機器關機再開始容錯移轉)。 Site Recovery 在觸發容錯移轉之前，會嘗試將來源虛擬機器關機。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業] 頁面上追蹤容錯移轉進度。
5. 確認 Azure VM 如預期般出現在 Azure 中。
6. 在 [複寫的項目] 中，以滑鼠右鍵按一下 VM > [完成移轉]。 這會完成移轉程序、停止 VM 的複寫功能，並停止 VM 的 Site Recovery 計費。

    ![完成移轉](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **不要取消正在進行的容錯移轉**：在啟動容錯移轉之前，已停止 VM 複寫。 如果您取消正在進行的容錯移轉，容錯移轉會停止，但 VM 不會再次複寫。

在某些情況下，容錯移轉需要額外的處理，這會耗費約 8 到 10 分鐘的時間來完成。 您可能注意到下列項目的測試容錯移轉時間較久：實體伺服器、VMware Linux 機器、未啟用 DHCP 服務的 VMware VM，以及沒有下列開機驅動程式的 VMware VM：storvsc、vmbus、storflt、intelide、atapi。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將內部部署 VM 移轉至 Azure VM。 您現在可以設定 Azure VM 的災害復原。

> [!div class="nextstepaction"]
> 從內部部署站台移轉之後，[設定 Azure VM 的災害復原](site-recovery-azure-to-azure-after-migration.md)。
