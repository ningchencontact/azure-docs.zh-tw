---
title: 使用 Azure Site Recovery 部署 VMware 災害復原的組態伺服器 | Microsoft Docs
description: 本文說明如何使用 Azure Site Recovery 部署 VMware 災害復原的組態伺服器
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 2389ff6824a005db46c04bd1b45eabfd5ce50481
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188475"
---
# <a name="deploy-a-configuration-server"></a>部署設定伺服器

當您使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 和實體伺服器災害復原到 Azure 時，便會部署內部部署設定伺服器。 設定伺服器會協調內部部署 VMware 與 Azure 之間的通訊。 它也會管理資料複寫。 本文會逐步引導您完成當您將 VMware VM 複寫至 Azure 時部署設定伺服器所需的步驟。 如果您必須設定實體伺服器複寫的設定伺服器，請[遵循這篇文章](physical-azure-set-up-source.md)。

## <a name="prerequisites"></a>先決條件

我們建議您將組態伺服器部署為高可用性的 VMware VM。 下表彙總最低硬體需求。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]




## <a name="capacity-planning"></a>容量規劃

組態伺服器的大小需求取決於可能的資料變更率。 請使用下表作為指南。

| **CPU** | **記憶體** | **快取磁碟大小** | **資料變更率** | **受保護的機器** |
| --- | --- | --- | --- | --- |
| 8 個 vCPU (2 個插槽 * 4 核心 @ 2.5GHz) |16 GB |300 GB |500 GB 或更少 |複寫少於 100 部電腦。 |
| 12 個 vCPU (2 個插槽 * 6 核心 @ 2.5GHz) |18 GB |600 GB |500 GB 至 1 TB |複寫 100-150 部機器。 |
| 16 個 vCPU (2 個插槽 * 8 核心 @ 2.5GHz) |32 GB |1 TB |1 TB 至 2 TB |複寫 150-200 部機器。 |


如果您複寫 VMware VM，請深入了解[容量規劃考量](/site-recovery-plan-capacity-vmware.md)。 執行用於 VMware 複寫的[部署規劃工具](site-recovery-deployment-planner.md)。



## <a name="download-the-template"></a>下載範本

Site Recovery 提供了可下載的範本，供您將組態伺服器設定為高可用性的 VMware VM。 

1. 在保存庫中，移至 [準備基礎結構] > [來源]。
2. 在 [準備來源] 中，選取 [+設定伺服器]。
3. 在 [新增伺服器] 中，檢查 [VMware 的組態伺服器] 是否出現在 [伺服器類型] 中。
4. 下載組態伺服器的開放式虛擬化格式 (OVF) 範本。

  > [!TIP]
  您可以直接從 [Microsoft 下載中心](https://aka.ms/asrconfigurationserver)，下載最新版的設定伺服器範本。


## <a name="import-the-template-in-vmware"></a>在 VMware 中匯入範本


1. 使用 VMWare vSphere 用戶端，登入 VMware vCenter 伺服器或 vSphere ESXi 主機。
2. 在 [檔案] 功能表上，選取 [部署 OVF 範本] 以啟動 [部署 OVF 範本] 精靈。

     ![OVF 範本](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. 在 [選取來源] 中，輸入所下載 OVF 的位置。
4. 在 [檢閱詳細資料] 中，選取 [下一步]。
5. 在 [選取名稱和資料夾] 和 [選取設定] 中，接受預設設定。
6. 在 [選取儲存體] 中，於 [選取虛擬磁碟格式] 中選取 [Thick Provision Eager Zeroed]，以達到最佳效能。
4. 在精靈的其餘頁面中，接受所有的預設設定。
5. 在 [準備要完成] 中：

    * 若要使用預設設定來設定 VM，請選取 [在部署後開啟電源] > [完成]。

    * 若要新增額外的網路介面，請清除 [在部署後開啟電源]，然後選取 [完成]。 預設會使用單一 NIC 部署設定伺服器範本。 您可以在部署後新增其他 NIC。


## <a name="add-an-additional-adapter"></a>新增其他介面卡

如果您想要將其他 NIC 新增至設定伺服器，請在保存庫中註冊伺服器前新增。 註冊之後，便不支援新增其他介面卡。

1. 在 vSphere 用戶端詳細目錄中，以滑鼠右鍵按一下 VM 並選取 [編輯設定]。
2. 在 [硬體] 中，選取 [新增] > [乙太網路介面卡]。 然後，選取 [下一步]。
3. 選取介面卡類型和網路。 
4. 若要在 VM 開啟時連線虛擬 NIC，請選取 [在電源開啟時連線]。 然後，選取 [下一步] > [完成] > [確定]。
 

## <a name="register-the-configuration-server"></a>註冊組態伺服器 

1. 從 VMWare vSphere 用戶端主控台開啟 VM。
2. VM 會開機進入 Windows Server 2016 安裝體驗。 接受授權合約，並輸入系統管理員密碼。
3. 在安裝完成之後，以系統管理員身分登入 VM。
4. 您第一次登入時，Azure Site Recovery 設定工具會啟動。
5. 輸入用來向 Site Recovery 註冊設定伺服器的名稱。 然後，選取 [下一步]。
6. 此工具會檢查 VM 是否可連線到 Azure。 建立連線之後，選取 [登入] 以登入您的 Azure 訂用帳戶。 認證必須能夠存取您要在其中註冊組態伺服器的保存庫。
7. 此工具會執行一些設定工作，而後重新開機。
8. 再次登入機器。 設定伺服器管理精靈會自動啟動。

### <a name="configure-settings"></a>配置設定

1. 在設定伺服器管理精靈中，選取 [設定連線]。 選取用來接收複寫流量的 NIC，然後選取 [儲存]。 您在設定後便無法變更此設定。
2. 在 [選取復原服務保存庫] 中，選取您的 Azure 訂用帳戶及相關的資源群組和保存庫。
3. 在 [安裝第三方軟體] 中，接受授權合約。 選取 [下載並安裝] 以安裝 MySQL Server。
4. 選取 [安裝 VMware PowerLCI]。 在您執行這個步驟之前，確定所有瀏覽器視窗都已關閉。 然後選取 [繼續]。
5. 在 [驗證設備設定] 中，必要條件會在您繼續之前進行驗證。
6. 在 [設定 vCenter 伺服器/vSphere ESXi 伺服器] 中，輸入 vCenter 伺服器或 vSphere 主機 (您要複寫的 VM 位於其上) 的 FQDN 或 IP 位址。 輸入伺服器正在接聽的連接埠，以及保存庫中 VMware 伺服器的易記名稱。
7. 輸入供設定伺服器用來連線至 VMware 伺服器的認證。 Site Recovery 會使用這些認證來自動探索可用於複寫的 VMware VM。 選取 [新增]，然後選取 [繼續]。
8. 在 [設定虛擬機器認證] 中，輸入要用於在電腦上自動安裝 Azure Site Recovery 行動服務的使用者名稱和密碼 (已啟用複寫時)。 若為 Windows 電腦，此帳戶需具備您要複寫之機器的本機系統管理員權限。 若為 Linux，請提供根帳戶的詳細資料。
9. 選取 [完成設定] 以完成註冊。 
10. 註冊完成後，在 Azure 入口網站中確認組態伺服器和 VMware 伺服器都已列在保存庫中的 [來源] 頁面上。 然後選取 [確定] 以設定目標設定。


## <a name="upgrade-the-configuration-server"></a>升級設定伺服器

若要將組態伺服器升級為最新版本，請閱讀[此處](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)的步驟


## <a name="troubleshoot-deployment-issues"></a>為部署問題進行疑難排解

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>後續步驟

為 [VMware VM](vmware-azure-tutorial.md) 設定以 Azure 作為目標的災害復原。
