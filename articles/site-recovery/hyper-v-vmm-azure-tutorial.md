---
title: 設定災害復原的內部部署 HYPER-V Vm 至 Azure Site Recovery 的 VMM 雲端中 |Microsoft Docs
description: 了解如何設定內部部署 HYPER-V Vm 災害復原至 Azure 的 System Center VMM 雲端中使用 Site Recovery。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 653db1497fcce5981bba7416f073b0330ca2861f
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66398144"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>將 VMM 雲端中 Hyper-V VM 的災害復原設定至 Azure

這篇文章說明如何為內部部署 HYPER-V Vm 至 Azure 的災害復原使用的受管理的 System Center Virtual Machine Manager (VMM)，啟用複寫[Azure Site Recovery](site-recovery-overview.md)服務。 如果您未使用 VMM 中，[遵循本教學課程](hyper-v-azure-tutorial.md)改。

這是一系列中的第三個教學課程，說明如何為內部部署 VMware VM 設定 Azure 的災害復原。 在上一個教學課程中，我們[準備好內部部署 HYPER-V 環境](hyper-v-prepare-on-premises-tutorial.md)災害復原至 Azure。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 選取複寫來源和目標。
> * 設定來源複寫環境，包括內部部署 Site Recovery 元件和目標複寫環境。
> * 設定網路對應，以將 VMM VM 網路與 Azure 虛擬網路之間的對應。
> * 建立複寫原則。
> * 啟用 VM 複寫。

> [!NOTE]
> 這些教學課程示範案例的最簡單部署路徑。 可能的話，會使用預設選項，而不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱 [Site Recovery 文件](https://docs.microsoft.com/azure/site-recovery)的**操作指南**一節中的文章。

## <a name="before-you-begin"></a>開始之前

這是本系列的第三個教學課程。 其內容假設您已完成了先前教學課程中的工作：

1. [準備 Azure](tutorial-prepare-azure.md)
2. [準備內部部署 Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>選取複寫目標

1. 在 Azure 入口網站中，移至 [復原服務保存庫]  ，然後選取保存庫。 我們已在上一個教學課程中備妥保存庫 **ContosoVMVault**。
2. 在 [使用者入門]  中選取 [Site Recovery]  ，然後選取 [準備基礎結構]  。
3. 在 [保護目標]   > [您的電腦位於何處?]  中，選取 [內部部署]  。
4. 在 [您要將電腦複寫到何處?]  中，選取 [複製到 Azure]  。
5. 在 [您的電腦虛擬化了嗎?]  中，選取 [是，使用 Hyper-V]  。
6. 在 **您使用 System Center VMM 管理 HYPER-V 主機？** ，選取**是**。
7.  選取 [確定]  。

    ![複寫目標](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>確認部署規劃

1. 在 [部署規劃]  中，如果您正在規劃大型部署，請從頁面上的連結下載適用於 Hyper-V 的部署規劃工具。 [深入了解](hyper-v-deployment-planner-overview.md) Hyper-V 部署規劃。
2. 在本教學課程中，我們不需要部署規劃工具。 在 [完成部署規劃了嗎?]  中，選取 [稍後再進行]  ，然後選取 [確定]  。

## <a name="set-up-the-source-environment"></a>設定來源環境

當您設定來源環境時，您會在 VMM 伺服器上安裝 Azure Site Recovery Provider，並在保存庫中註冊伺服器。 您在每部 HYPER-V 主機上安裝 Azure 復原服務代理程式。

1. 在 **準備基礎結構**，選取**來源**。
2. 在 **準備來源**，選取 **+ VMM**將 VMM 伺服器。 在 [新增伺服器]  中，檢查 [System Center VMM 伺服器]  是否出現在 [伺服器類型]  中。
3. 下載 Microsoft Azure Site Recovery 提供者的安裝程式。
4. 下載存放庫註冊金鑰。 當您執行 Provider 安裝程式時，您會需要此金鑰。 該金鑰在產生後會維持 5 天有效。
5. 下載 Microsoft Azure 復原服務代理程式安裝程式。

    ![下載提供者、 註冊金鑰，以及代理程式](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>在 VMM 伺服器上將提供者解除安裝

1. 在 Azure Site Recovery Provider 安裝精靈 > **Microsoft Update** 中，選擇使用 Microsoft Update 來檢查 Provider 更新。
2. 在 **安裝**，接受預設安裝位置，提供者，然後選取**安裝**。
3. 安裝之後，在 Microsoft Azure Site Recovery 註冊精靈 >**保存庫設定**，選取**瀏覽**，然後在**金鑰檔案**，選取保存庫金鑰檔案您已下載。
4. 指定 Azure Site Recovery 訂用帳戶和保存庫名稱 (**ContosoVMVault**)。 為 VMM 伺服器指定易記名稱，以便在保存庫中加以識別。
5. 在 [Proxy 設定]  中，選取 [不使用 Proxy 而直接連線到 Azure Site Recovery]  。
6. 接受用來加密資料的憑證預設位置。 當您容錯移轉時，即會將加密的資料解密。
7. 在 [同步雲端中繼資料]  中，選取 [請將雲端中繼資料同步至 Site Recovery 入口網站]  。 這個動作需要在每一部伺服器上一次發生。 接著，選取 [註冊]  。
8. 在保存庫註冊伺服器之後，請選取**完成**。

註冊完成之後，Azure Site Recovery 會取出來自伺服器的中繼資料，而該 VMM 伺服器會顯示在 [Site Recovery 基礎結構]  中。

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>在 HYPER-V 主機上安裝復原服務代理程式

每個包含您想要複寫的 Vm 的 HYPER-V 主機上安裝代理程式。

1. 在 Microsoft Azure 復原服務代理程式安裝精靈 >**檢查先決條件**，選取**下一步**。 將自動安裝任何遺漏的必要條件。
2. 在 **安裝設定**，接受安裝位置和快取位置。 快取磁碟機至少需要 5 GB 的儲存體。 我們建議磁碟機要有 600 GB 或更多的可用空間。 然後，選取 [安裝]  。
3. 在 **安裝**，當安裝完成時，選取**關閉**以完成精靈。

    ![安裝代理程式](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>設定目標環境

1. 選取 [準備基礎結構]   > [目標]  。
2. 選取容錯移轉之後，將在其中建立 Azure VM 的訂用帳戶和資源群組 (**ContosoRG**)。
3. 選取  **Resource Manager**部署模型。

Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

## <a name="configure-network-mapping"></a>設定網路對應

1. 在  **Site Recovery 基礎結構** > **網路對應** > **網路對應**，選取 **+ 網路對應**圖示。
2. 在 [新增網路對應]  中，選取來源 VMM 伺服器。 選取 [Azure]  作為目標。
3. 在容錯移轉後確認訂用帳戶和部署模型。
4. 在 [來源網路]  中，選取來源內部部署 VM 網路。
5. 在 [目標網路]  中，選取 Azure 網路來放置容錯移轉之後建立的複本 Azure VM。 然後選取 [確定]  。

    ![網路對應](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>設定複寫原則

1. 選取 [準備基礎結構]   > [複寫設定]   > [+建立並產生關聯]  。
2. 在 [建立及關聯原則]  中指定原則名稱。 我們使用的是 **ContosoReplicationPolicy**。
3. 保留預設設定，然後選取**確定**。
    - **複製頻率**指出初始複寫之後，差異資料會複寫每隔五分鐘。
    - **復原點保留期**指示，將會保留兩個小時的每個復原點。
    - [應用程式一致快照頻率]  指出將會每小時建立一次包含應用程式一致快照集的復原點。
    - [初始複寫開始時間]  指出初始複寫將會立即開始。
    - **儲存在 Azure 上的資料加密**設為預設值 (**關閉**)，並指出，不會加密在 Azure 中的待用資料。
4. 原則建立後，請選取 [確定]  。 當您建立新的原則時，它會自動產生關聯與 VMM 雲端中。

## <a name="enable-replication"></a>啟用複寫

1. 在 [複寫應用程式]  中，選取 [來源]  。
2. 在 [來源]  中，選取 VMM 雲端。 然後選取 [確定]  。
3. 在 **目標**，確認目標 (Azure) 的保存庫訂用帳戶，然後選取**Resource Manager**模型。
4. 選取  **contosovmsacct1910171607**儲存體帳戶並**ContosoASRnet** Azure 網路。
5. 在 [虛擬機器]   > [選取]  中，選取您要複寫的 VM。 然後選取 [確定]  。

   您可以在 [作業]   > [Site Recovery 作業]  中，追蹤 [啟用保護]  動作的進度。 在**完成保護**作業完成後，即完成初始複寫，且 VM 已可進行容錯移轉。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [執行災害復原演練](tutorial-dr-drill-azure.md)
