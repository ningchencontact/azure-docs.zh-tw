---
title: 使用 Azure Site Recovery 部署 VMware 災害復原的組態伺服器 | Microsoft Docs
description: 本文說明如何使用 Azure Site Recovery 部署 VMware 災害復原的組態伺服器
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2019
ms.author: ramamill
ms.openlocfilehash: c25ca8c27b84f34b025ec5abce00c8d8c70e5df6
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565119"
---
# <a name="deploy-a-configuration-server"></a>部署設定伺服器

當您使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 和實體伺服器災害復原到 Azure 時，便會部署內部部署設定伺服器。 設定伺服器會協調內部部署 VMware 與 Azure 之間的通訊。 它也會管理資料複寫。 本文會逐步引導您完成當您將 VMware VM 複寫至 Azure 時部署設定伺服器所需的步驟。 如果您必須設定實體伺服器複寫的設定伺服器，請[遵循這篇文章](physical-azure-set-up-source.md)。

> [!TIP]
> 您可以從[這裡](vmware-azure-architecture.md)了解組態伺服器在 Azure Site Recovery 架構中扮演的角色。

## <a name="deployment-of-configuration-server-through-ova-template"></a>透過 OVA 範本部署設定伺服器

設定伺服器必須搭配特定的最基本硬體和大小需求來設定為高可用性 VMware VM。 為了能夠便利且輕鬆地進行部署，Site Recovery 提供一個可下載的 OVA (開放式虛擬化應用程式) 範本，以設定能符合下列所有規定需求的設定伺服器。

## <a name="prerequisites"></a>必要條件

下表彙總了設定伺服器的最基本硬體需求。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Azure Active Directory 权限要求

只有拥有 AAD (Azure Active Directory) 中设置的**以下权限之一**的用户才能将配置服务器注册到 Azure Site Recovery 服务。

1. 使用者應該有 「 應用程式開發人員 」 角色，才能建立應用程式。
   1. 若要验证角色，请登录到 Azure 门户</br>
   1. 导航到“Azure Active Directory”>“角色和管理员”</br>
   1. 检查“应用程序开发人员”角色是否已分配到该用户。 如果没有，请使用拥有此权限的用户，或[联系管理员启用该权限](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles)。
    
1. 如果您無法指定 「 應用程式開發人員 」 角色，請確定 「 使用者可以註冊應用程式 」 的旗標設為 true，若要建立身分識別的使用者。 若要启用上述权限，请执行以下操作：
   1. 登入 Azure 入口網站
   1. 导航到“Azure Active Directory”>“用户设置”
   1. 在“应用注册”下，“用户可以注册应用程序”应设置为“是”。

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> **不支持** Active Directory 联合身份验证服务 (ADFS)。 请使用通过 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 管理的帐户。

## <a name="capacity-planning"></a>容量規劃

組態伺服器的大小需求取決於可能的資料變更率。 請使用下表作為指南。

| **CPU** | **記憶體** | **快取磁碟大小** | **資料變更率** | **受保護的機器** |
| --- | --- | --- | --- | --- |
| 8 個 vCPU (2 個插槽 * 4 核心 \@ 2.5GHz) |16 GB |300 GB |500 GB 或更少 |複寫少於 100 部電腦。 |
| 12 個 vCPU (2 個插槽 * 6 核心 \@ 2.5GHz) |18 GB |600 GB |500 GB 至 1 TB |複寫 100-150 部機器。 |
| 16 個 vCPU (2 個插槽 * 8 核心 \@ 2.5GHz) |32 GB |1 TB |1 TB 至 2 TB |複寫 150-200 部機器。 |

如果您要複寫多個 VMware VM，請參閱[容量規劃考量](site-recovery-plan-capacity-vmware.md)。 執行用於 VMware 複寫的[部署規劃工具](site-recovery-deployment-planner.md)。

## <a name="download-the-template"></a>下載範本

1. 在保存庫中，移至 [準備基礎結構] > [來源]。
2. 在 [準備來源] 中，選取 [+設定伺服器]。
3. 在 [新增伺服器] 中，檢查 [VMware 的組態伺服器] 是否出現在 [伺服器類型] 中。
4. 下載適用於設定伺服器的「開放式虛擬化應用程式」(OVA) 範本。

   > [!TIP]
   >您也可以直接從 [Microsoft 下載中心](https://aka.ms/asrconfigurationserver)下載最新版的設定伺服器範本。

> [!NOTE]
> .OVA 範本隨附的授權是有效的 180 天的評估授權。 張貼這段期間，客戶必須啟用 windows procured 授權。

## <a name="import-the-template-in-vmware"></a>在 VMware 中匯入範本

1. 使用 VMWare vSphere 用戶端，登入 VMware vCenter 伺服器或 vSphere ESXi 主機。
2. 在 [檔案] 功能表上，選取 [部署 OVF 範本] 以啟動 [部署 OVF 範本] 精靈。

     ![OVF 範本](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. 在 [選取來源] 中，輸入所下載 OVF 的位置。
4. 在 [檢閱詳細資料] 中，選取 [下一步]。
5. 在 [選取名稱和資料夾] 和 [選取設定] 中，接受預設設定。
6. 在 [選取儲存體] 中，於 [選取虛擬磁碟格式] 中選取 [Thick Provision Eager Zeroed]，以達到最佳效能。 使用精簡佈建選項可能會影響設定伺服器的效能。
7. 在精靈的其餘頁面中，接受所有的預設設定。
8. 在 [準備要完成] 中：

    * 若要使用預設設定來設定 VM，請選取 [在部署後開啟電源] > [完成]。

    * 若要新增額外的網路介面，請清除 [在部署後開啟電源]，然後選取 [完成]。 預設會使用單一 NIC 部署設定伺服器範本。 您可以在部署後新增其他 NIC。

> [!IMPORTANT]
> 請勿在部署後變更資源設定 (記憶體/核心/CPU限制)，修改/刪除安裝服務或設定伺服器上的檔案。 這會影響設定伺服器與 Azure 服務的註冊，以及設定伺服器的效能。

## <a name="add-an-additional-adapter"></a>新增其他介面卡

如果您想要將其他 NIC 新增至設定伺服器，請在保存庫中註冊伺服器前新增。 註冊之後，便不支援新增其他介面卡。

1. 在 vSphere 用戶端詳細目錄中，以滑鼠右鍵按一下 VM 並選取 [編輯設定]。
2. 在 [硬體] 中，選取 [新增] > [乙太網路介面卡]。 然後，選取 [下一步]。
3. 選取介面卡類型和網路。
4. 若要在 VM 開啟時連線虛擬 NIC，請選取 [在電源開啟時連線]。 然後，選取 [下一步] > [完成] > [確定]。

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>向 Azure Site Recovery 服務註冊設定伺服器

1. 從 VMWare vSphere 用戶端主控台開啟 VM。
2. VM 會開機進入 Windows Server 2016 安裝體驗。 接受授權合約，並輸入系統管理員密碼。
3. 在安裝完成之後，以系統管理員身分登入 VM。
4. 您第一次登入時，「Azure Site Recovery 設定工具」在幾秒內就會啟動。
5. 輸入用來向 Site Recovery 註冊設定伺服器的名稱。 然後，選取 [下一步]。
6. 此工具會檢查 VM 是否可連線到 Azure。 建立連線之後，選取 [登入] 以登入您的 Azure 訂用帳戶。</br>
    a. 認證必須能夠存取您要在其中註冊組態伺服器的保存庫。</br>
    b. 确保所选用户帐户有权在 Azure 中创建应用程序。 若要启用所需的权限，请遵循[此处](#azure-active-directory-permission-requirements)所述的指导原则。
7. 此工具會執行一些設定工作，而後重新開機。
8. 再次登入機器。 設定伺服器管理精靈會在幾秒內**自動**啟動。

### <a name="configure-settings"></a>配置設定

1. 在設定伺服器管理精靈中，選取 [設定連線]。 從下拉式清單中，先選取內建處理序伺服器用來在來源機器上進行行動服務探索及推入安裝的 NIC，然後選取設定伺服器用來與 Azure 連線的 NIC。 然後選取 [儲存]。 它設定之後，您無法變更此設定。 強烈建議不要變更設定伺服器的 IP 位址。 請確定指派到設定伺服器的 IP 是靜態 IP 並不是 DHCP IP。
2. 在**選取 復原服務保存庫**，所使用的認證登入 Microsoft Azure**步驟 6**的 「[註冊組態伺服器與 Azure Site Recovery 服務](#register-the-configuration-server-with-azure-site-recovery-services)".
3. 登录后，选择你的 Azure 订阅以及相关的资源组和保管库。

    > [!NOTE]
    > 復原服務保存庫在註冊之後，即沒有變更的彈性。
    > 更改恢复服务保管库需要从当前保管库取消关联配置服务器，配置服务器下所有受保护虚拟机的复制将会停止。 [深入](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)了解。

4. 在 [安裝協力廠商軟體] 中，

    |案例   |要依循的步驟  |
    |---------|---------|
    |是否可以下載並手動安裝 MySQL？     |  是。 請下載 MySQL 並將它放在資料夾 **C:\Temp\ASRSetup** 資料夾中，然後手動安裝。 現在，當您接受條款 > 按一下 [下載並安裝] 時，入口網站會顯示「已經安裝」。 您可以繼續進行下一個步驟。       |
    |是否可以避免線上下載 MySQL？     |   是。 請將您的 MySQL 安裝程式應用程式放在 **C:\Temp\ASRSetup** 資料夾中。 接受條款 > 按一下 [下載並安裝]，入口網站將會使用您新增的安裝程式並安裝應用程式。 您可以繼續進行安裝後的下一個步驟。    |
    |我想要透過 Azure Site Recovery 下載並安裝 MySQL     |  接受授權合約，然後按一下 [下載並安裝]。 接著，您可以繼續進行安裝後的下一個步驟。       |

5. 在 [驗證設備設定] 中，必要條件會在您繼續之前進行驗證。
6. 在 [設定 vCenter 伺服器/vSphere ESXi 伺服器] 中，輸入 vCenter 伺服器或 vSphere 主機 (您要複寫的 VM 位於其上) 的 FQDN 或 IP 位址。 輸入伺服器所接聽的連接埠。 輸入要用於保存庫中 VMware 伺服器的易記名稱。
7. 輸入供設定伺服器用來連線至 VMware 伺服器的認證。 Site Recovery 會使用這些認證來自動探索可用於複寫的 VMware VM。 選取 [新增]，然後選取 [繼續]。 在這裡輸入的認證會儲存在本機。
8. 在 [設定虛擬機器認證] 中，輸入虛擬機器的使用者名稱和密碼，以在複寫期間自動安裝「行動服務」。 針對 **Windows** 電腦，此帳戶必須具備您要複寫之電腦的本機系統管理員權限。 針對 **Linux**，請提供根帳戶的詳細資料。
9. 選取 [完成設定] 以完成註冊。
10. 註冊完成之後，開啟 Azure 入口網站，確認 [復原服務保存庫] > [管理] > [Site Recovery 基礎結構] > [設定伺服器] 上已列出設定伺服器和 VMware 伺服器。

## <a name="upgrade-the-configuration-server"></a>升級設定伺服器

若要將組態伺服器升級為最新版本，請遵循[這裡](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)的步驟。 如需如何升級所有 Site Recovery 元件的詳細指示，請按一下[此處](service-updates-how-to.md)。

## <a name="manage-the-configuration-server"></a>管理組態伺服器

若要避免進行中的複寫中斷，請確定已向保存庫註冊組態伺服器後，組態伺服器的 IP 位址不會變更。 您可在[此處](vmware-azure-manage-configuration-server.md)深入了解常見組態伺服器管理工作。

## <a name="faq"></a>常見問題集

1. 透過 OVF 部署之設定伺服器上提供的授權有效期間多長？ 如果我未重新啟用授權，會發生什麼事？

    .OVA 範本隨附的授權是有效的 180 天的評估授權。 您必須在到期之前啟用授權。 否則，這會導致設定伺服器頻繁關機，因而阻礙複寫活動。

2. 是否可以將已安裝組態伺服器的 VM 用於不同用途？

    **否**，建議您使用 VM 作為設定伺服器的唯一用途。 請務必遵循[必要條件](#prerequisites)所述的所有規格，以便有效管理災害復原。
3. 是否可以將已在設定伺服器中註冊的保存庫與新建立的保存庫交換？

    **否**，在保存庫向設定伺服器註冊之後，即無法變更。
4. 是否可以使用同一部設定伺服器來保護實體和虛擬機器？

    **是**，可以使用同一部組態伺服器來複寫實體和虛擬機器。 不過，實體機器只能容錯回復到 VMware VM。
5. 組態伺服器的用途為何而其使用位置為何？

    請參考 [VMware 到 Azure 複寫架構](vmware-azure-architecture.md)，以深入了解組態伺服器及其功能。
6. 哪裡可以找到最新版的設定伺服器？

    如需透過入口網站升級組態伺服器的步驟，請參閱[升級組態伺服器](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)。 如需如何升級所有 Site Recovery 元件的詳細指示，請參閱[此處](https://aka.ms/asr_how_to_upgrade)。
7. 哪裡可以下載設定伺服器的複雜密碼？

    請參閱[本文](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)以下載複雜密碼。
8. 我可以變更複雜密碼嗎？

    **否**，**強烈建議您不要變更設定伺服器的複雜密碼**。 變更複雜密碼會破壞受保護機器的複寫，並導致危急的健全狀態。
9. 哪裡可以下載保存庫註冊金鑰？

    在 [復原服務保存庫] 的 [管理] > [Site Recovery Infrastructure] \(Site Recovery 基礎結構\) > [設定伺服器] 中。 在 [伺服器] 中，選取 [下載註冊金鑰] 以下載保存庫認證檔案。
10. 是否可以複製現有的設定伺服器並將它用於複寫協調流程？

    **否**，不支援使用複製的設定伺服器元件。 複製的向外延展處理序伺服器也是不支援的案例。 複製 Site Recovery 元件會影響進行中的複寫。

11. 我可以變更設定伺服器的 IP 嗎？

    **否**，強烈建議不要變更設定伺服器的 IP 位址。 請確定指派到設定伺服器的所有 IP 都是靜態 IP 而不是 DHCP IP。
12. 我可以在 Azure 上設定設定伺服器嗎？

    建議在內部部署環境中使用 v-Center 直接設定設定伺服器，並將資料傳輸的延遲降至最低。 您可以對設定伺服器建立排程的備份，以實現[容錯回復目的](vmware-azure-manage-configuration-server.md#failback-requirements)。

如需設定伺服器上的多個常見問題集，請參閱[有關設定伺服器常見問題的文件](vmware-azure-common-questions.md#configuration-server)。

## <a name="troubleshoot-deployment-issues"></a>為部署問題進行疑難排解

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>後續步驟

為 [VMware VM](vmware-azure-tutorial.md) 設定以 Azure 作為目標的災害復原。
