---
title: 使用 Azure Site Recovery 設定內部部署 Hyper-V VM (不含 VMM) 至 Azure 的災害復原 | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 服務來設定內部部署 Hyper-V VM (不含 VMM) 至 Azure 的災害復原。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: da643a4d7a1dc74385b3854c1952af5ba93bd241
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358091"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>設定 Hyper-V VM 至 Azure 的災害復原

c

> [!div class="checklist"]
> * 選取複寫來源和目標。
> * 設定來源複寫環境，包括內部部署 Site Recovery 元件和目標複寫環境。
> * 建立複寫原則。
> * 啟用 VM 複寫。

> [!NOTE]
> 這些教學課程示範案例的最簡單部署路徑。 可能的話，會使用預設選項，而不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱 Site Recovery 目錄的「操作說明」區段中的文章。

## <a name="before-you-begin"></a>開始之前
這是本系列的第三個教學課程。 本教學課程假設您已經完成了先前教學課程中的工作：

1. [準備 Azure](tutorial-prepare-azure.md)
2. [準備內部部署 Hyper-V](tutorial-prepare-on-premises-hyper-v.md)



## <a name="select-a-replication-goal"></a>選取複寫目標

1. 在 [復原服務保存庫] 中選取保存庫。 我們已在上一個教學課程中備妥保存庫 **ContosoVMVault**。
2. 在 [使用者入門] 中，按一下 [Site Recovery]。 然後按一下 [準備基礎結構]
3. 在 [保護目標] > [您的電腦位於何處] 中，選取 [內部部署]。
4. 在 [您要將電腦複寫到何處?] 中，選取 [複製到 Azure]。
5. 在 [您的電腦虛擬化了嗎?] 中，選取 [是，使用 Hyper-V]。
6. 在 [您是否使用 System Center VMM 來管理 Hyper-V 主機]，選取 [否]。 然後按一下 [確定] 。

    ![複寫目標](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>確認部署規劃

1. 在 [部署規劃] 中，如果您正在規劃大型部署，請從頁面上的連結下載適用於 Hyper-V 的部署規劃工具。 [深入了解](hyper-v-deployment-planner-overview.md) Hyper-V 部署規劃。
2. 基於本教學課程的目的，我們不需要部署規劃工具。 在 [完成部署規劃了嗎?] 中，選取 [稍後再進行]。 然後按一下 [確定] 。

    ![部署規劃](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>設定來源環境

若要設定來源環境，您可以建立 Hyper-V 網站並將含有所要複寫 VM 的 Hyper-V 主機新增至該網站。 然後下載 Azure Site Recovery Provider 和 Azure 復原服務代理程式並安裝於每部主機上，然後在保存庫中註冊 Hyper-V 網站。 

1. 在 [準備基礎結構] 之下，按一下 [來源]。
2. 在 [準備來源] 中，按一下 [+Hyper-V 網站]。
3. 在 [建立 Hyper-V 網站] 中，指定網站名稱。 我們使用的是 **ContosoHyperVSite**。

    ![Hyper-V 站台](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. 建立網站後，請在 [準備來源] > [步驟 1：選取 Hyper-V 網站] 中，選取您所建立的網站。
4. 按一下 [+Hyper-V 伺服器]。

    ![Hyper-V 伺服器](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. 下載 Microsoft Azure Site Recovery 提供者的安裝程式。
6. 下載保存庫註冊金鑰。 您需要此金鑰才能安裝 Provider。 該金鑰在產生後會維持 5 天有效。

    ![下載 Provider](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>安裝 Provider

在您要新增至 Hyper-V 網站的每部 Hyper-V 主機上安裝所下載的安裝程式檔案 (AzureSiteRecoveryProvider.exe)。 安裝程式會在每部 Hyper-V 主機上安裝 Azure Site Recovery Provider 和復原服務代理程式。

1. 執行安裝程式檔案。
2. 在 Azure Site Recovery Provider 安裝精靈 > **Microsoft Update** 中，選擇使用 Microsoft Update 來檢查 Provider 更新。
3. 在 [安裝] 中，接受 Provider 和代理程式的預設安裝位置，然後按一下 [安裝]。
4. 安裝之後，在 Microsoft Azure Site Recovery 註冊精靈 > [保存庫設定] 中，按一下 [瀏覽]，然後在 [金鑰檔案] 中，選取您下載的保存庫金鑰檔。 
5. 指定 Azure Site Recovery 訂用帳戶、保存庫名稱 (**ContosoVMVault**)，以及 Hyper-V 伺服器所屬的 Hyper-V 網站 (**ContosoHyperVSite**)。
6. 在 [Proxy 設定] 中，選取 [不使用 Proxy 而直接連線到 Azure Site Recovery]。
7. 在保存庫中註冊伺服器之後，請於 [註冊] 中按一下 [完成]。

Azure Site Recovery 會取出來自 Hyper-V 伺服器的中繼資料，而該伺服器會顯示在 [Site Recovery 基礎結構] > [Hyper-V 主機] 中。 此程序可能需要 30 分鐘的時間。        

#### <a name="install-on-hyper-v-core-server"></a>安裝於 Hyper-V 核心伺服器

如果您執行的是 Hyper-V 核心伺服器，請下載安裝程式檔案並執行下列作業：

1. 藉由執行下列命令，從 AzureSiteRecoveryProvider.exe 將檔案解壓縮到本機目錄：

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2.  執行 `.\setupdr.exe /i。 結果會記錄到 %Programdata%\ASRLogs\DRASetupWizard.log.

3.  使用此命令註冊伺服器：

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```
 

## <a name="set-up-the-target-environment"></a>設定目標環境

選取並確認目標資源。 

1. 按一下 [準備基礎結構] > [目標]。
2. 選取容錯移轉之後，將在其中建立 Azure VM 的訂用帳戶和資源群組 **ContosoRG**。
3. 選取 [資源管理員] 部署模型。

Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。


## <a name="set-up-a-replication-policy"></a>設定複寫原則

1. 按一下 [準備基礎結構] > [複寫設定] > [+建立並產生關聯]。
2. 在 [建立及關聯原則] 中指定原則名稱。 我們使用的是 **ContosoReplicationPolicy**。
3. 在本教學課程中，我們會保留預設設定。
    - [複製頻率] 指出差異資料 (在初始複寫後) 的複寫頻率 (預設為每隔五分鐘複寫一次)。
    - [復原點保留] 指定復原點會保留兩小時。
    - [應用程式一致快照頻率]  指出將會每小時建立一次包含應用程式一致快照集的復原點。
    - [初始複寫開始時間]  指出初始複寫將會立即開始。
4. 建立原則之後，請按一下 [確定]。 建立的新原則會自動與指定的 Hyper-V 網站 (在我們的教學課程中為 **ContosoHyperVSite**) 產生關聯

    ![複寫原則](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>啟用複寫


1. 在 [複寫應用程式] 中，按一下 [來源]。 
2. 在 [來源] 中，選取 [ContosoHyperVSite] 網站。 然後按一下 [確定] 。
3. 在 [目標] 中，確認目標為 Azure、保存庫訂用帳戶，以及 [資源管理員] 部署模型。
4. 如果您使用教學課程設定，請選取在前一個教學課程中為所複寫資料建立的 **contosovmsacct1910171607** 儲存體帳戶，以及 Azure VM 在容錯移轉之後所在的 **ContosoASRnet** 網路。
5. 在 [虛擬機器] > [選取] 中，選取您要複寫的 VM。 然後按一下 [確定] 。

   您可以在 [作業] > [Site Recovery 作業] 中，追蹤 [啟用保護] 動作的進度。 在**完成保護**作業完成後，即完成初始複寫，虛擬機器已可進行容錯移轉。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [執行災害復原演練](tutorial-dr-drill-azure.md)
