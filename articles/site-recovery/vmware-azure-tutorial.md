---
title: 使用 Azure Site Recovery 為內部部署 VMware VM 設定災害復原至 Azure | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 為內部部署 VMware VM 設定災害復原至 Azure。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6c86a98dd819b91608be04f1466dc1e6764ee4b9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>為內部部署 VMware VM 設定災害復原至 Azure

本教學課程說明如何為執行 Windows 的內部部署 VMware VM，設定 Azure 的災害復原。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 輸入複寫來源和目標。
> * 設定來源複寫環境，包括內部部署 Azure Site Recovery 元件和目標複寫環境。
> * 建立複寫原則。
> * 啟用 VM 複寫。

這是本系列的第三個教學課程。 本教學課程假設您已完成先前教學課程中的工作：

* [準備 Azure](tutorial-prepare-azure.md)。 本教學課程說明如何設定 Azure 儲存體帳戶和網路、確定您的 Azure 帳戶具有正確權限，以及建立復原服務保存庫。
* [準備內部部署 VMware](vmware-azure-tutorial-prepare-on-premises.md)。 在本教學課程中，您將準備一個帳戶，讓 Site Recovery 可存取 VMware 伺服器來探索 VM，以及在為 VM 啟用複寫時，選擇性地進行 Site Recovery 行動服務元件的推入安裝。 您也可確定您的 VMware 伺服器和 VM 完全符合 Site Recovery 需求。

開始之前，最好先針對災害復原案例[檢閱架構](vmware-azure-architecture.md)。


## <a name="select-a-replication-goal"></a>選取複寫目標

1. 在 [復原服務保存庫] 中，選取保存庫名稱 **ContosoVMVault**。
2. 在 [使用者入門] 中，選取 Site Recovery。 然後選取 [準備基礎結構]。
3. 在 [保護目標] > [您的電腦位於何處] 中，選取 [內部部署]。
4. 在 [您要將電腦複寫到何處] 中，選取 [複製到 Azure]。
5. 在 [您的電腦虛擬化了嗎] 中，選取 [是，使用 VMware vSphere Hyperviso]。 然後選取 [確定]。

## <a name="set-up-the-source-environment"></a>設定來源環境


若要設定來源環境，您需要單一、高可用性、內部部署的電腦來裝載 Site Recovery 元件。 元件包含設定伺服器、處理序伺服器和主要目標伺服器：

- 設定伺服器會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。
- 處理序伺服器可作為複寫閘道。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。 處理序伺服器也會在您要複寫的 VM 上安裝行動服務，並且在內部部署 VMware VM 上執行自動探索。
- 主要目標伺服器會在從 Azure 容錯回復期間，處理複寫資料。

若要將設定伺服器設定為高可用性 VMware VM，請下載備妥的開放式虛擬化格式 (OVF) 範本，然後將範本匯入 VMware 以建立 VM。 在設定好設定伺服器之後，在保存庫中加以註冊。 註冊之後，Site Recovery 會探索內部部署 VMware VM。

> [!TIP]
> 本教學課程會使用 OVF 範本來建立組態伺服器 VMware VM。 如果您無法進行此操作，可以執行[手動安裝](physical-manage-configuration-server.md)來完成此操作。 


### <a name="download-the-vm-template"></a>下載 VM 範本

1. 在保存庫中，移至 [準備基礎結構] > [來源]。
2. 在 [準備來源] 中，選取 [+設定伺服器]。
3. 在 [新增伺服器] 中，檢查 [VMware 的組態伺服器] 是否出現在 [伺服器類型] 中。
4. 下載設定伺服器的 OVF 範本。

  > [!TIP]
  您可以直接從 [Microsoft 下載中心](https://aka.ms/asrconfigurationserver)，下載最新版的設定伺服器範本。

## <a name="import-the-template-in-vmware"></a>在 VMware 中匯入範本

1. 使用 VMWare vSphere 用戶端，登入 VMware vCenter 伺服器或 vSphere ESXi 主機。
2. 在 [檔案] 功能表上，選取 [部署 OVF 範本] 以啟動 [部署 OVF 範本] 精靈。 

     ![OVF 範本](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. 在 [選取來源] 上，輸入所下載 OVF 的位置。
4. 在 [檢閱詳細資料] 上，選取 [下一步]。
5. 在 [選取名稱和資料夾] 和 [選取組態] 上，接受預設設定。
6. 在 [選取儲存體] 上，於 [選取虛擬磁碟格式] 中選取 [Thick Provision Eager Zeroed]，以達到最佳效能。
7. 在精靈的其餘頁面上，接受所有的預設設定。
8. 在 [準備要完成] 上：

    * 若要使用預設設定來設定 VM，請選取 [在部署後開啟電源] > [完成]。

    * 如果您想要新增額外的網路介面，請清除 [在部署後開啟電源]。 然後選取 [完成]。 預設會使用單一 NIC 部署設定伺服器範本。 您可以在部署後新增其他 NIC。

## <a name="add-an-additional-adapter"></a>新增其他介面卡

若要將其他 NIC 新增至設定伺服器，請在保存庫中註冊伺服器前新增。 註冊之後，便不支援新增其他介面卡。

1. 在 vSphere 用戶端詳細目錄中，以滑鼠右鍵按一下 VM 並選取 [編輯設定]。
2. 在 [硬體] 中，選取 [新增] > [乙太網路介面卡]。 然後，選取 [下一步]。
3. 選取介面卡類型和網路。 
4. 若要在 VM 開啟時連線虛擬 NIC，請選取 [在電源開啟時連線]。 選取 [下一步] > [完成]。 然後選取 [確定]。


## <a name="register-the-configuration-server"></a>註冊組態伺服器 

1. 從 VMWare vSphere 用戶端主控台開啟 VM。
2. VM 會開機進入 Windows Server 2016 安裝體驗。 接受授權合約，並輸入系統管理員密碼。
3. 在安裝完成之後，以系統管理員身分登入 VM。
4. 您第一次登入時，Azure Site Recovery 設定工具會啟動。
5. 輸入用來向 Site Recovery 註冊設定伺服器的名稱。 然後，選取 [下一步]。
6. 此工具會檢查 VM 是否可連線到 Azure。 建立連線之後，選取 [登入] 以登入您的 Azure 訂用帳戶。 認證必須能夠存取您要在其中註冊組態伺服器的保存庫。
7. 此工具會執行一些設定工作，而後重新開機。
8. 再次登入電腦。 設定伺服器管理精靈會自動啟動。

### <a name="configure-settings-and-add-the-vmware-server"></a>進行設定與新增 VMware 伺服器

1. 在設定伺服器管理精靈中，選取 [設定連線]，然後選取要接收複寫流量的 NIC。 然後選取 [儲存]。 您在設定後便無法變更此設定。
2. 在 [選取復原服務保存庫] 中，選取您的 Azure 訂用帳戶及相關的資源群組和保存庫。
3. 在 [安裝第三方軟體] 中，接受授權合約。 選取 [下載並安裝] 以安裝 MySQL Server。
4. 選取 [安裝 VMware PowerCLI]。 在您執行這項作業之前，確定所有瀏覽器視窗都已關閉。 然後選取 [繼續]。
5. 在 [驗證設備設定] 中，必要條件會在您繼續之前進行驗證。
6. 在 [設定 vCenter 伺服器/vSphere ESXi 伺服器] 中，輸入 vCenter 伺服器或 vSphere 主機 (您要複寫的 VM 位於其上) 的 FQDN 或 IP 位址。 輸入伺服器所接聽的連接埠。 輸入要用於保存庫中 VMware 伺服器的易記名稱。
7. 輸入供設定伺服器用來連線至 VMware 伺服器的認證。 Site Recovery 會使用這些認證來自動探索可用於複寫的 VMware VM。 選取 [新增]，然後選取 [繼續]。
8. 在 [設定虛擬機器認證] 中，輸入要用於在電腦上自動安裝行動服務的使用者名稱和密碼 (已啟用複寫時)。 若為 Windows 電腦，此帳戶需具備您要複寫之機器的本機系統管理員權限。 若為 Linux，請提供根帳戶的詳細資料。
9. 選取 [完成設定] 以完成註冊。 
10. 註冊完成後，在 Azure 入口網站中確認組態伺服器和 VMware 伺服器都已列在保存庫中的 [來源] 頁面上。 然後選取 [確定] 以設定目標設定。


Site Recovery 會使用指定的設定連線至 VMware 伺服器並探索 VM。

> [!NOTE]
> 可能需要 15 分鐘以上，帳戶名稱才會出現在入口網站。 若要立即更新，請選取 [設定伺服器] > 伺服器名稱 > [重新整理伺服器]。

## <a name="set-up-the-target-environment"></a>設定目標環境

選取並確認目標資源。

1. 選取 [準備基礎結構] > [目標]。 選取您要使用的 Azure 訂用帳戶。
2. 指定目標部署模型是以 Azure Resource Manager 為基礎，還是傳統的。
3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

   ![目標索引標籤](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>建立複寫原則

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後選取 [所有資源]。
2. 選取名為 **ContosoVMVault** 的復原服務保存庫。
3. 若要建立複寫原則，請選取 [Site Recovery 基礎結構] > [複寫原則] > [+複寫原則]。
4. 在 [建立複寫原則] 中，選取原則名稱 **VMwareRepPolicy**。
5. 在 [RPO 閾值] 中，使用預設值 60 分鐘。 這個值可定義復原點的建立頻率。 連續複寫超過此限制時會產生警示。
6. 在 [復原點保留] 中，使用預設值 24 小時作為每個復原點的保留期長度。 在本教學課程中，使用 72 小時。 複寫的 VM 可以還原至一個週期內的任何時間點。
7. 在 [應用程式一致快照頻率] 中，使用 60 分鐘預設值作為應用程式一致快照集的建立頻率。 選取 [確定] 以建立原則。

   ![建立複寫原則](./media/vmware-azure-tutorial/replication-policy.png)

此原則會自動與設定伺服器產生關聯。 依預設會自動建立容錯回復的比對原則。 例如，如果複寫原則是 **rep-policy**，容錯回復原則就會是 **rep-policy-failback**。 從 Azure 起始容錯回復時才會使用此原則。

## <a name="enable-replication"></a>啟用複寫

Site Recovery 會在 VM 已啟用複寫時安裝行動服務。 可能需要 15 分鐘或更久的時間，變更才會生效並顯示在入口網站中。

啟用複寫，如下所示︰

1. 選取 [複寫應用程式] > [來源]。
2. 在 [來源] 中，選取設定伺服器。
3. 在 [機器類型] 中，選取 [虛擬機器]
4. 在 [vCenter/vSphere Hypervisor] 中，選取管理 vSphere 主機的 vCenter 伺服器，或選取主機。
5. 選取處理序伺服器 (組態伺服器)。 然後選取 [確定]。
6. 在 [目標] 中，選取您想要在其中建立容錯移轉 VM 的訂用帳戶和資源群組。 選擇您想要在 Azure (傳統或 Resource Manager) 中，針對容錯移轉 VM 使用的部署模型。
7. 選取您要用來複寫資料的 Azure 儲存體帳戶。
8. 選取 Azure VM 在容錯移轉後所要連線的 Azure 網路和子網路。
9. 選取 [立即設定選取的機器]，將網路設定套用至您選取要進行保護的所有機器。 選取 [稍後設定] 以選取每部機器的 Azure 網路。
10. 在 [虛擬機器] > [選取虛擬機器] 中，選取您要複寫的每部機器。 您只能選取可以啟用複寫的機器。 然後選取 [確定]。
11. 在 [屬性] > [設定屬性] 中，選取處理序伺服器要用來在電腦上自動安裝行動服務的帳戶。
12. 在 [複寫設定] > [設定複寫設定] 中，確認已選取正確的複寫原則。
13. 選取 [啟用複寫]。

您可以在 [設定]  >  [作業]  >  [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。 執行 [完成保護] 作業之後，機器即準備好進行容錯移轉。

若要監視您新增的 VM，請在 [設定伺服器] >  [上次連絡時間] 中查看上次探索 VM 的時間。 若要新增 VM 而不等候已排定的探索，請醒目提示設定伺服器 (但不要選取)，然後選取 [重新整理]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [執行災害復原演練](site-recovery-test-failover-to-azure.md)
