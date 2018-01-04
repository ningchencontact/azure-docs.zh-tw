---
title: "設定內部部署 HYPER-V Vm （無 VMM) 與 Azure Site Recovery 的 Azure 災害復原 |Microsoft 文件"
description: "Azure 了解如何設定災害復原的 （無 VMM) 在內部部署 HYPER-V Vm 與 Azure Site Recovery 服務。"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31`/2017
ms.author: raynew
ms.openlocfilehash: 633c14bd25bc8a1419196b2c76ca94c26db68991
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>設定 Hyper-V VM 至 Azure 的災害復原

[Azure Site Recovery](site-recovery-overview.md) 服務可藉由管理及協調內部部署電腦與 Azure 虛擬機器 (VM) 的複寫、容錯移轉及容錯回復，為您的災害復原策略做出貢獻。

本教學課程說明如何設定內部部署 Hyper-V VM 至 Azure 的災害復原。 本教學課程是關於的不受管理的 System Center Virtual Machine Manager (VMM) 的 HYPER-V Vm。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 選取您的複寫來源和目標。
> * 設定來源複寫環境，包括在內部部署復原站台元件，以及目標複寫環境。
> * 建立複寫原則。
> * 啟用複寫 VM。

這是本系列的第三個教學課程。 本教學課程假設您已經完成了先前教學課程中的工作：

1. [準備 Azure](tutorial-prepare-azure.md)
2. [準備內部部署 HYPER-V](tutorial-prepare-on-premises-hyper-v.md)

開始之前，最好先[檢閱架構](concepts-hyper-v-to-azure-architecture.md)此嚴重損壞修復案例。

## <a name="select-a-replication-goal"></a>選取複寫目標


1. 在**所有服務** > **復原服務保存庫**，按一下 上一個教學課程中，我們備妥的保存庫名稱**ContosoVMVault**。
2. 在**入門**，按一下  **Site Recovery**。 然後按一下 **準備基礎結構**
3. 在**保護目標** > **所在位置位於您機器**，選取**內部**。
4. 在**您要在其中複寫您的機器**，選取**To Azure**。
5. 在**虛擬化您機器**，選取**是的含 HYPER-V**。
6. 在**您使用 System Center VMM**，選取**否**。 然後按一下 [確定] 。

    ![複寫目標](./media/tutorial-hyper-v-to-azure/replication-goal.png)

## <a name="set-up-the-source-environment"></a>設定來源環境

若要設定的來源環境，您可以新增至 HYPER-V 站台的 HYPER-V 主機並下載並安裝 Azure Site Recovery Provider 和 Azure 復原服務代理程式，在保存庫中登錄 HYPER-V 站台。 

1. 在 [準備基礎結構] 中，按一下 [來源]。
2. 按一下**+ HYPER-V 站台**，並指定在上一個教學課程中，我們建立的站台名稱**ContosoHyperVSite**。
3. 按一下**+ HYPER-V Server**。
4. 下載提供者安裝檔案。
5. 下載保存庫註冊金鑰。 您執行提供者安裝程式時需要此金鑰。 該金鑰在產生後會維持 5 天有效。

    ![下載提供者](./media/tutorial-hyper-v-to-azure/download.png)
    

### <a name="install-the-provider"></a>安裝提供者

每個上執行提供者設定檔 (AzureSiteRecoveryProvider.exe) 的 HYPER-V 主機新增至您**ContosoHyperVSite**站台。 安裝程式會安裝 Azure Site Recovery Provider 和復原服務代理程式，每個 HYPER-V 主機上。

1. 在 Azure Site Recovery Provider 安裝精靈 > **Microsoft Update**，選擇要使用 Microsoft Update 來檢查提供者更新。
2. 在**安裝**，接受預設安裝位置，提供者和代理程式，然後按一下**安裝**。
3. 安裝完成後，在 Microsoft Azure Site Recovery 註冊精靈 >**保存庫設定**，按一下 **瀏覽**，然後在**金鑰檔案**，選取保存庫金鑰檔，您下載。 
4. 指定 Azure Site Recovery 訂用帳戶，保存庫名稱 (**ContosoVMVault**)，和 HYPER-V 站台 (**ContosoHyperVSite**) 所屬的 HYPER-V 伺服器。
5. 在**Proxy 設定**，選取**直接連線到不使用 proxy 的 Azure Site Recovery**。
6. 在**註冊**在保存庫註冊伺服器之後，按一下 **完成**。

Azure Site Recovery 會取出來自 Hyper-V 伺服器的中繼資料，而該伺服器會顯示在 [Site Recovery 基礎結構] > [Hyper-V 主機] 中。 這可能需要最多 30 分鐘的時間。


## <a name="set-up-the-target-environment"></a>設定目標環境

選取並確認目標資源。 

1. 按一下 [準備基礎結構] > [目標]。
2. 選取的訂用帳戶和資源群組**ContosoRG**，在 Azure Vm 會被建立容錯移轉之後。
3. 選取**資源管理員 」**部署模型。

Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。


## <a name="set-up-a-replication-policy"></a>設定複寫原則

1. 按一下 [準備基礎結構] > [複寫設定] > [+建立並產生關聯]。
2. 在**建立及關聯的原則**，指定原則名稱， **ContosoReplicationPolicy**。
3. 保留預設設定，再按**確定**。
    - **複製頻率**指出該差異 （在初始複寫） 之後的資料會複寫每隔五分鐘。
    - **復原點保留**指出每個復原點保留時間將兩個兩小時。
    - **應用程式一致快照的頻率**表示每小時將會建立包含應用程式一致快照的復原點。
    - **初始複寫開始時間**，表示會立即開始初始複寫。
4. 建立原則之後，請按一下**確定**。 當您建立新的原則，它會自動與指定的 HYPER-V 站台相關聯 (**ContosoHyperVSite**)

    ![複寫原則](./media/tutorial-hyper-v-to-azure/replication-policy.png)


## <a name="enable-replication"></a>啟用複寫


1. 在**複寫應用程式**，按一下 **來源**。 
2. 在**來源**，選取**ContosoHyperVSite**站台。 然後按一下 [確定] 。
3. 在**目標**，確認 Azure 為目標，保存庫的訂用帳戶，而**資源管理員**部署模型。
4. 選取**contosovmsacct1910171607**複寫的資料的上一個教學課程中建立的儲存體帳戶和**ContosoASRnet**哪一個 Azure Vm 中的網路，容錯移轉之後會位於位置。
5. 在**虛擬機器** > **選取**，選取您想要複寫的 VM。 然後按一下 [確定] 。

 您可以在 [作業] > [Site Recovery 作業] 中，追蹤 [啟用保護] 動作的進度。 在**完成保護**作業完成後，即完成初始複寫，虛擬機器已可進行容錯移轉。

## <a name="next-steps"></a>後續步驟
[執行災害復原演練](tutorial-dr-drill-azure.md)
