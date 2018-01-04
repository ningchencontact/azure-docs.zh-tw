---
title: "使用 Azure Site Recovery 的 VMM 雲端中設定內部部署 HYPER-V Vm 的災害復原 |Microsoft 文件"
description: "了解如何在 Azure 中，與 Azure Site Recovery 服務的 System Center VMM 雲端中設定內部部署 HYPER-V Vm 的災害復原。"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: 5f364c6f8a88ad53c12909f0e9f10afcce5ef8af
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>設定內部部署 HYPER-V Vm 的災害復原至 Azure 的 VMM 雲端中

[Azure Site Recovery](site-recovery-overview.md) 服務可藉由管理及協調內部部署電腦與 Azure 虛擬機器 (VM) 的複寫、容錯移轉及容錯回復，為您的災害復原策略做出貢獻。

本教學課程說明如何設定內部部署 Hyper-V VM 至 Azure 的災害復原。 本教學課程會由 System Center Virtual Machine Manager (VMM) 管理的 HYPER-V Vm 與相關項目。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 選取您的複寫來源和目標。
> * 設定來源複寫環境，包括在內部部署復原站台元件，以及目標複寫環境。
> * 設定網路對應，以 VMM VM 網路與 Azure 虛擬網路之間進行對應。
> * 建立複寫原則
> * 啟用 VM 複寫

這是本系列的第三個教學課程。 本教學課程假設您已經完成了先前教學課程中的工作：

1. [準備 Azure](tutorial-prepare-azure.md)
2. [準備內部部署 HYPER-V](tutorial-prepare-on-premises-hyper-v.md)

開始之前，最好先[檢閱架構](concepts-hyper-v-to-azure-architecture.md)此嚴重損壞修復案例。



## <a name="select-a-replication-goal"></a>選取複寫目標

1. 在**所有服務** > **復原服務保存庫**，按一下您在這些教學課程中，我們使用保存庫名稱**ContosoVMVault**。
2. 在**入門**，按一下  **Site Recovery**。 然後按一下 **準備基礎結構**
3. 在**保護目標** > **所在位置位於您機器**，選取**內部**。
4. 在**您要在其中複寫您的機器**，選取**To Azure**。
5. 在**虛擬化您機器**，選取**是的含 HYPER-V**。
6. 在**您使用 System Center VMM**，選取**是**。 然後按一下 [確定] 。

    ![複寫目標](./media/tutorial-hyper-v-vmm-to-azure/replication-goal.png)



## <a name="set-up-the-source-environment"></a>設定來源環境

設定來源環境時，您需要安裝 Azure Site Recovery Provider 和 Azure 復原服務代理程式，並在保存庫中註冊內部部署伺服器。 

1. 在 [準備基礎結構] 中，按一下 [來源]。
2. 在 [準備來源] 中，按一下 [+ VMM] 以新增 VMM 伺服器。 在 [新增伺服器] 中，檢查 [System Center VMM 伺服器] 是否出現在 [伺服器類型] 中。
3. 下載 Microsoft Azure Site Recovery provider 安裝程式。
4. 下載保存庫註冊金鑰。 您執行提供者安裝程式時需要此金鑰。 該金鑰在產生後會維持 5 天有效。
5. 下載的復原服務代理程式。

    ![下載](./media/tutorial-hyper-v-vmm-to-azure/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>在 VMM 伺服器上將提供者解除安裝

1. 在 Azure Site Recovery Provider 安裝精靈 > **Microsoft Update**，選擇要使用 Microsoft Update 來檢查提供者更新。
2. 在**安裝**，接受預設安裝位置的提供者，然後按一下**安裝**。 
3. 安裝完成後，在 Microsoft Azure Site Recovery 註冊精靈 >**保存庫設定**，按一下 **瀏覽**，然後在**金鑰檔案**，選取保存庫金鑰檔，您下載。
4. 指定 Azure Site Recovery 訂用帳戶和保存庫名稱 (**ContosoVMVault**)。 指定 VMM 伺服器，以識別保存庫中的易記名稱。
5. 在**Proxy 設定**，選取**直接連線到不使用 proxy 的 Azure Site Recovery**。
6. 接受預設位置用來加密資料的憑證。 當您容錯移轉時，將會解密加密的資料。
7. 在**同步處理雲端中繼資料**，選取**同步處理雲端中繼資料至 Site Recovery 入口網站**。 這個動作只需要在每個伺服器上進行一次。 然後按一下 **註冊**。
8. 在保存庫中註冊伺服器之後，請按一下 [完成]。

註冊完成之後，Azure Site Recovery 會取出來自伺服器的中繼資料，而該 VMM 伺服器會顯示在 [Site Recovery 基礎結構] 中。

### <a name="install-the-recovery-services-agent"></a>安裝復原服務代理程式

每個包含您想要複寫的 Vm 的 HYPER-V 主機上安裝代理程式。

1. 在 Microsoft Azure Recovery Services Agent 安裝精靈中 >**必要條件檢查**，按一下 **下一步**。 將自動安裝遺失的必要條件。
2. 在**安裝設定**，接受安裝位置，並快取位置。 快取磁碟機至少需要 5 GB 的儲存體。 我們建議磁碟機要有 600 GB 或更多的可用空間。 然後按一下 [安裝] 。
3. 在 [安裝] 中，當安裝完成時，請按一下 [關閉] 以完成精靈。

    ![安裝代理程式](./media/tutorial-hyper-v-vmm-to-azure/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>設定目標環境

1. 按一下 [準備基礎結構] > [目標]。
2. 選取的訂用帳戶和資源群組 (**ContosoRG**) 中的 Azure Vm 將會建立容錯移轉之後。
3. 選取**資源管理員 」**部署模型。

Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。


## <a name="configure-network-mapping"></a>設定網路對應

1. 在 [Site Recovery 基礎結構]  >  [網路對應]  >  [網路對應]中，按一下 [+網路對應] 圖示。
2. 在 [新增網路對應] 中，選取來源 VMM 伺服器。 選取 [Azure] 作為目標。
3. 在容錯移轉後確認訂用帳戶和部署模型。
4. 在 [來源網路] 中，選取來源內部部署 VM 網路。
5. 在 [目標網路] 中，選取 Azure 網路來放置容錯移轉之後建立的複本 Azure VM。 然後按一下 [確定] 。

    ![網路對應](./media/tutorial-hyper-v-vmm-to-azure/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>設定複寫原則

1. 按一下 [準備基礎結構] > [複寫設定] > [+建立並產生關聯]。
2. 在**建立及關聯的原則**，指定原則名稱， **ContosoReplicationPolicy**。
3. 保留預設設定，再按**確定**。
    - **複製頻率**指出該差異 （在初始複寫） 之後的資料會複寫每隔五分鐘。
    - **復原點保留**指出每個復原點保留時間將兩個兩小時。
    - **應用程式一致快照的頻率**表示每小時將會建立包含應用程式一致快照的復原點。
    - **初始複寫開始時間**，表示會立即開始初始複寫。
    - **加密資料儲存在 Azure 上**-預設**關閉**設定指出靜止不加密 Azure 中的資料。
4. 建立原則之後，請按一下**確定**。 當您建立新的原則時，該原則會自動與 VMM 雲端產生關聯。

## <a name="enable-replication"></a>啟用複寫

1. 在**複寫應用程式**，按一下 **來源**。 
2. 在**來源**，選取 VMM 雲端。 然後按一下 [確定] 。
3. 在**目標**，確認 Azure 為目標，保存庫的訂用帳戶，然後選取**資源管理員**模型。
4. 選取**contosovmsacct1910171607**儲存體帳戶和**ContosoASRnet** Azure 網路。
5. 在**虛擬機器** > **選取**，選取您想要複寫的 VM。 然後按一下 [確定] 。

 您可以在 [作業] > [Site Recovery 作業] 中，追蹤 [啟用保護] 動作的進度。 在**完成保護**作業完成後，即完成初始複寫，且 VM 已可進行容錯移轉。


## <a name="next-steps"></a>後續步驟
[執行災害復原演練](tutorial-dr-drill-azure.md)
