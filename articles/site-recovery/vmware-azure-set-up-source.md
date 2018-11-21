---
title: 設定來源環境與組態伺服器，以便使用 Azure Site Recovery 進行從 VMware VM 至 Azure 的災害復原 | Microsoft Docs
description: 本文說明如何設定內部部署環境與組態伺服器，以便使用 Azure Site Recovery 進行從 VMware VM 至 Azure 的災害復原。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: e09e93ef85449c51e35b8da7ad93ee7088a0e7b4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566307"
---
# <a name="set-up-the-source-environment-and-configuration-server"></a>設定來源環境與組態伺服器

本文說明如何設定來源內部部署環境，以便使用 [Azure Site Recovery](site-recovery-overview.md) 進行從 VMware VM 至 Azure 的災害復原。 這包括將內部部署機器設定為 Site Recovery 組態伺服器以及自動探索內部部署 VM 等步驟。 

## <a name="prerequisites"></a>必要條件

開始之前，請先確定您已完成下列項目

- 使用 [Azure Site Recovery 部署規劃工具](site-recovery-deployment-planner.md)規劃災害復原部署。 規劃工具協助確保您針對災害復原有足夠的資源和頻寬，以及組織中的復原點目標 (RPO)。
- 在 [Azure 入口網站](http://portal.azure.com)中[設定 Azure 資源](tutorial-prepare-azure.md)。
- [設定內部部署 VMware](vmware-azure-tutorial-prepare-on-premises.md) 資源，包括進行自動探索內部部署 VMware VM 的專用帳戶。
- 您可以[檢閱關於組態伺服器部署和管理的常見問題](vmware-azure-common-questions.md#configuration-server)。


## <a name="choose-protection-goals"></a>選擇保護目標

1. 在 [復原服務保存庫] 中，選取保存庫名稱。 
2. 在 [使用者入門] 中，選取 Site Recovery。 然後選取 [準備基礎結構]。
3. 在 [保護目標] > [您的電腦位於何處] 中，選取 [內部部署]。
4. 在 [您要將電腦複寫到何處] 中，選取 [複製到 Azure]。
5. 在 [您的電腦虛擬化了嗎] 中，選取 [是，使用 VMware vSphere Hyperviso]。 然後選取 [確定]。

## <a name="about-the-configuration-server"></a>關於組態伺服器

當您設定 VMware VM 到 Azure 的災害復原時，便會部署內部部署組態伺服器。

- 設定伺服器會協調內部部署 VMware 與 Azure 之間的通訊。 它也會管理資料複寫。
- 部署組態伺服器作為 VMware VM。
- Site Recovery 會提供您從 Azure 入口網站下載的 OVA 範本，並將該範本匯入至 vCenter Server 來設定組態伺服器 VM。
- [深入了解](vmware-azure-architecture.md)組態伺服器元件和程序。


>[!NOTE]
如果您要部署組態伺服器，以便進行內部部署實體機器至 Azure 的災害復原，請勿使用這些指示。 針對此案例，請遵循[這篇文章](physical-azure-set-up-source.md)。


## <a name="before-you-deploy-the-configuration-server"></a>部署組態伺服器之前

如果使用 OVA 範本將組態伺服器作為 VMware VM 安裝，則將會包含所有必要條件安裝 VM。 如果您想要檢閱必要條件，請使用下列文章。

- [深入了解](vmware-azure-configuration-server-requirements.md)組態伺服器的硬體、軟體和容量需求。
- 如果您要複寫多部 VMware VM，您應檢閱[容量規劃考量](site-recovery-plan-capacity-vmware.md)，並執行 [Azure Site Recovery 部署規劃工具](site-recovery-deployment-planner.md)以進行 VMWare 複寫。
- OVA 範本的 Windows 授權是有效期限為 180 天的評估授權。 在此時間之後，您需要使用有效的授權來啟用 Windows。 
- OVA 範本提供一個簡單的方式，將組態伺服器設定為 VMware VM。 如果基於某些原因您需要在沒有範本的情況下設定 VMware VM，請檢閱必要條件，並按照[這篇文章](physical-azure-set-up-source.md)中的手動指示進行操作。
- 您的 Azure 帳戶必須建立 Azure AD 應用程式的權限。


>[!IMPORTANT]
必須按照這篇文章中的描述部署組態伺服器。 不支援複製或再製現有的組態伺服器。

### <a name="set-up-azure-account-permissions"></a>設定 Azure 帳戶權限

租用戶/全域管理員可以為該帳戶指派建立 Azure AD 應用程式的權限，或者將應用程式開發人員角色 (具有權限) 指派給該帳戶。


租用戶/全域管理員可以授與帳戶的權限，如下所示：

1. 在 Azure AD 中，租用戶/全域管理員應該瀏覽至 [Azure Active Directory] > [使用者] > [使用者設定]。
2. 管理員應將 [應用程式註冊] 設定為 [是]。

 > [!NOTE]
 > 這是一個不敏感的預設設定。 [深入了解](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

或者，租用戶/全域管理員有權將角色指派給該帳戶。 [深入了解](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

 


## <a name="download-the-ova-template"></a>下載 OVA 範本

1. 在保存庫中，移至 [準備基礎結構] > [來源]。
2. 在 [準備來源] 中，選取 [+設定伺服器]。
3. 在 [新增伺服器] 中，檢查 [VMware 的組態伺服器] 是否出現在 [伺服器類型] 中。
4. 下載適用於設定伺服器的「開放式虛擬化應用程式」(OVA) 範本。

  > [!TIP]
>您也可以從 [Microsoft 下載中心](https://aka.ms/asrconfigurationserver)下載最新版的組態伺服器範本。


## <a name="import-the-ova-template-in-vmware"></a>在 VMware 中匯入 OVA 範本

1. 透過 VMWare vSphere 用戶端，登入 VMware vCenter 伺服器或 vSphere ESXi 主機。
2. 在 [檔案] 功能表上，選取 [部署 OVF 範本] 以啟動 [部署 OVF 範本] 精靈。

     ![OVF 範本](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. 在 [選取來源] 中，輸入所下載 OVF 的位置。
4. 在 [檢閱詳細資料] 中，選取 [下一步]。
5. 在 [選取名稱和資料夾] 和 [選取設定] 中，接受預設設定。
6. 在 [選取儲存體] 中，於 [選取虛擬磁碟格式] 中選取 [Thick Provision Eager Zeroed]，以達到最佳效能。
7. 在精靈的其餘頁面中，接受所有的預設設定。
8. 在 [準備完成] 上，若要使用預設設定來設定 VM，請選取 [在部署後開啟電源] > [完成]。
9. 根據預設，VM 會使用單一 NIC 進行部署。 如果您想要新增額外的 NIC，請清除 [在部署後開啟電源]，然後按一下 [完成]。 然後遵循下一個程序。

## <a name="add-an-adapter-to-the-configuration-server"></a>將網路介面卡新增到組態伺服器

如果您想要將其他 NIC 新增至設定伺服器，請在保存庫中註冊伺服器前新增。 註冊之後，便不支援新增其他介面卡。

1. 在 vSphere 用戶端詳細目錄中，以滑鼠右鍵按一下 VM 並選取 [編輯設定]。
2. 在 [硬體] 中，選取 [新增] > [乙太網路介面卡]。 然後，選取 [下一步]。
3. 選取介面卡類型和網路。
4. 若要在 VM 開啟時連線虛擬 NIC，請選取 [在電源開啟時連線]。 然後，選取 [下一步] > [完成] > [確定]。

## <a name="register-the-configuration-server"></a>註冊組態伺服器 
開啟 VM，並在 Site Recovery 保存庫中註冊組態伺服器。

1. 從 VMWare vSphere 用戶端主控台開啟 VM。
2. VM 會開機進入 Windows Server 2016 安裝體驗。 接受授權合約，並輸入系統管理員密碼。
3. 在安裝完成之後，以系統管理員身分登入 VM。



## <a name="set-up-the-configuration-server"></a>設定組態伺服器

作為部署的一部分，您需要在組態伺服器 VM 上安裝 MySQL。 您可以使用下列幾種方式來執行這個動作：

- 讓 Site Recovery 下載並安裝 MySQL。 如果您想要使用此選項，則在開始組態伺服器設定之前不需要採取任何動作。
- 手動下載並安裝 MySQL：在設定組態伺服器之前，請下載 MySQL 安裝程式，並將它放在 **C:\Temp\ASRSetup** 資料夾。 現在安裝 MySQL。 
- 手動下載，讓 Site Recovery 安裝。 在設定組態伺服器之前，請下載 MySQL 安裝程式，並將它放入 **C:\Temp\ASRSetup** 資料夾。


1. 您第一次登入 VM 時，Azure Site Recovery 設定工具會啟動。
2. 指定用來向 Site Recovery 保存庫註冊組態伺服器的名稱。 然後，選取 [下一步]。
3. 此工具會檢查 VM 是否可連線到 Azure。 建立連線之後，選取 [登入] 以登入您的 Azure 訂用帳戶。 請注意，該帳戶必須能夠存取您要在其中註冊組態伺服器的保存庫。
4. 此工具會執行一些設定工作，而後重新開機。
5. 再次登入機器。 組態伺服器管理精靈會在數秒內自動啟動。
6. 在精靈中，選取 [設定連線]。
7. 選取處理序伺服器 (依預設會在組態伺服器上執行) 用來 接收來自 VM 之複寫流量的 NIC。
8. 然後選取 [儲存]。 請注意，在註冊設定伺服器之後，您無法變更保存庫設定。 
9. 在 [選取復原服務保存庫] 中，登入 Microsoft Azure，選取您的 Azure 訂用帳戶，以及相關的資源群組和保存庫。 
10. 在 [安裝協力廠商軟體] 中，安裝 MySQL：
     - 如果 Site Recovery 正在處理 MySQL 下載和安裝，請按一下 [下載並安裝]。 等候安裝完成，然後繼續執行精靈。
     - 如果您下載了 MySQL 且 Site Recovery 將安裝它，請按一下 [下載並安裝]。 等候安裝完成，並繼續執行精靈。
     - 如果您手動下載並安裝 MySQL，請按一下 [下載並安裝]。 該應用程式會顯示為**已安裝**。 繼續執行精靈。
11. 在 [驗證設備設定] 中，安裝程式會檢查必要條件才能繼續。 
12. 在 [設定 vCenter 伺服器/vSphere ESXi 伺服器] 中，輸入 vCenter 伺服器或 vSphere 主機 (您要複寫的 VM 位於其上) 的 FQDN 或 IP 位址。 輸入伺服器正在接聽的連接埠，並指定保存庫中 VMware 伺服器的易記名稱。
13. 輸入組態伺服器將用於連線至 VMware 伺服器的認證，並自動探索可用於複寫的 VMware VM。 選取 [新增] >  [繼續]。 認證會儲存在本機。
14. 在 [設定虛擬機器認證] 中，指定當您針對 VM 啟用複寫時，Site Recovery 將用於自動安裝行動服務的認證。
    - 若為 Windows 電腦，此帳戶需具備您要複寫之機器的本機系統管理員權限。
    - 若為 Linux，請提供根帳戶的詳細資料。
15. 選取 [完成設定] 以完成註冊。
16. 註冊完成之後，開啟 Azure 入口網站並確認 [復原服務保存庫] > [管理] > [Site Recovery 基礎結構] > [組態伺服器] 上已列出組態伺服器和 VMware 伺服器。


## <a name="exclude-antivirus-on-the-configuration-server"></a>排除組態伺服器上的防毒軟體

如果組態伺服器 VM 上正在執行防毒軟體，請確保從防毒軟體的作業中排除下列資料夾。 這可確保複寫和連線能力如預期般運作： 

- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool
- C:\thirdparty
- C:\Temp
- C:\strawberry
- C:\ProgramData\MySQL
- C:\Program Files (x86)\MySQL
- C:\ProgramData\ASR
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\inetpub
- Site Recovery 安裝目錄。 例如：E:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>後續步驟
- 如果遇到困難，請檢閱組態伺服器的[常見問題](vmware-azure-common-questions.md#configuration server)和[疑難排解](vmware-azure-troubleshoot-configuration-server.md)。
- 現在，在 Azure 中[設定您的目標環境](./vmware-azure-set-up-target.md)。
