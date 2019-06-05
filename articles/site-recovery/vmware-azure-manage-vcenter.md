---
title: 管理 VMware vCenter 伺服器以使用 Azure Site Recovery 進行 VMware VM 至 Azure 的災害復原 | Microsoft Docs
description: 本文說明如何新增和管理 VMware vCenter，以便使用 Azure Site Recovery 進行 VMware VM 至 Azure 的災害復原。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 9694c682f171ab715812b05fed2064c9bbcd36b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60600449"
---
# <a name="manage-vmware-vcenter-server"></a>管理 VMware vCenter 伺服器

本文討論可以在 VMware vCenter 上執行的各種 Site Recovery 作業。 開始之前，請確認[必要條件](vmware-physical-azure-support-matrix.md#replicated-machines)。


## <a name="set-up-an-account-for-automatic-discovery"></a>設定帳戶以進行自動探索

Site Recovery 需要存取 VMware，才能讓處理序伺服器自動探索虛擬機器，以及針對虛擬機器進行容錯移轉和容錯回復。 建立帳戶以進行存取，如下所示：

1. 登入設定伺服器電腦。
2. 使用 [桌面] 捷徑開啟啟動 cspsconfigtool.exe。
3. 按一下 [管理帳戶]  索引標籤上的 [新增帳戶]  。

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. 提供帳戶詳細資料，並按一下 [確定]  加以新增。  帳戶應該具有下表摘要說明的權限。 

帳戶資訊需要花費約 15 分鐘的時間以和 Site Recovery 服務進行同步處理。

### <a name="account-permissions"></a>帳戶權限

|**Task** | **帳戶** | **權限** | **詳細資料**|
|--- | --- | --- | ---|
|**自動探索/遷移 (不含容錯回復)** | 您需要至少一個唯讀使用者 | 数据中心对象 –> 传播到子对象、角色=只读 | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件]  物件的 [沒有存取權]  角色指派給子物件 (vSphere 主機、資料存放區、虛擬機器及網路)。|
|**複寫/容錯移轉** | 您需要至少一個唯讀使用者| 数据中心对象 –> 传播到子对象、角色=只读 | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件]  物件的 [沒有存取權]  角色指派給子物件 (vSphere 主機、資料存放區、虛擬機器及網路)。<br/><br/> 適用於移轉用途，而不是完整複寫、容錯移轉、容錯回復。|
|**複寫/容錯移轉/容錯回復** | 建議您建立具有必要權限的角色 (AzureSiteRecoveryRole)，然後將角色指派給 VMware 使用者或群組 | 資料中心物件 –> 傳播至子物件、role=AzureSiteRecoveryRole<br/><br/> 資料存放區 -> 配置空間、瀏覽資料存放區、底層檔案作業、移除檔案、更新虛擬機器檔案<br/><br/> 網路 -> 網路指派<br/><br/> 資源 -> 指派 VM 至資源集區、移轉已關閉電源的 VM、移轉已開啟電源的 VM<br/><br/> 任务 -> 创建任务、更新任务<br/><br/> 虛擬機器 -> 組態<br/><br/> 虛擬機器 -> 互動 -> 回答問題、裝置連線、設定 CD 媒體、設定磁碟片媒體、電源關閉、電源開啟、VMware 工具安裝<br/><br/> 虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/> 虛擬機器 -> 佈建 -> 允許虛擬機器下載、允許虛擬機器檔案上傳<br/><br/> 虚拟机 -> 快照 -> 删除快照 | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件]  物件的 [沒有存取權]  角色指派給子物件 (vSphere 主機、資料存放區、虛擬機器及網路)。|


## <a name="add-vmware-server-to-the-vault"></a>將 VMware 伺服器新增至保存庫

1. 在 Azure 入口網站中，開啟您的保存庫 > [Site Recovery 基礎結構]   > [設定伺服器]  ，然後開啟設定伺服器。
2. 在 [詳細資料]  頁面上，按一下 [+vCenter]  。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>修改認證

修改用來連線至 vCenter Server 或 ESXi 主機的認證，如下所示：

1. 登入設定伺服器，並從桌面啟動 cspsconfigtool.exe。
2. 按一下 [管理帳戶]  索引標籤上的 [新增帳戶]  。

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. 提供新帳戶的詳細資料，並按一下 [確定]  加以新增。 該帳戶必須具備[上方](#account-permissions)所列的權限。
4. 在 Azure 入口網站中，開啟保存庫 > [Site Recovery 基礎結構]   > [設定伺服器]  ，然後開啟設定伺服器。
5. 在 [詳細資料]  頁面上，按一下 [重新整理伺服器]  。
6. [重新整理伺服器] 作業完成後，選取 [vCenter Server] 以開啟 vCenter [摘要]  頁面。
7. 在 [vCenter Server/vSphere 主機帳戶]  欄位中選取新增的帳戶，然後按一下 [儲存]  。

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>刪除 vCenter Server

1. 在 Azure 入口網站中，開啟您的保存庫 > [Site Recovery 基礎結構]   > [設定伺服器]  ，然後開啟設定伺服器。
2. 在 [詳細資料]  頁面中，選取 vCenter Server。
3. 按一下 [刪除]  按鈕。

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>修改 vCenter IP 位址和連接埠

1. 登入 Azure 入口網站。
2. 瀏覽至**復原服務保存庫** > **Site Recovery 基礎結構** > **組態伺服器**。
3. 在組態伺服器 vCenter 上的按一下 指派給。
4. 在  **vCenter 伺服器**區段中，按一下您想要修改的 vCenter。
5. 在 [vCenter 摘要] 頁面中，更新 IP 位址和連接埠的對應欄位中中的 vCenter，然後儲存變更。

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. 變更才會生效，稍候 15 分鐘或[重新整理設定伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>所有受保護的虛擬機器移轉至新的 vCenter

若要將所有虛擬機器都移轉至新的 vCenter，請勿加入另一個 vCenter 的帳戶。 這可能會導致重複的項目。 只要更新新的 vCenter IP 位址：

1. 登入 Azure 入口網站。
2. 瀏覽至**復原服務保存庫** > **Site Recovery 基礎結構** > **組態伺服器**。
3. 在組態伺服器舊的 vCenter 上按一下 指派給。
4. 在  **vCenter 伺服器**區段中，按一下想要從移轉的 vCenter。
5. 在 vCenter 摘要 頁面中，更新 新的 vCenter，欄位中的 IP 位址**vCenter server/vSphere 主機名稱或 IP 位址**。 儲存您的變更。

只要更新 IP 位址時，Site Recovery 元件就會開始接收新的 vCenter 虛擬機器的探索資訊。 這不會影響進行中的複寫活動。

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>幾個受保護的虛擬機器移轉至新的 vCenter

> [!NOTE]
> 本節是適用於只有當您移轉幾個受保護的虛擬機器到新的 vCenter。 如果您想要防止新的 vCenter，一組新的虛擬機器[將新的 vCenter 詳細資料新增至組態伺服器](#add-vmware-server-to-the-vault)且開頭 **[啟用保護](vmware-azure-tutorial.md#enable-replication)** 。

若要將一些虛擬機器移至新的 vCenter 中：

1. [將新的 vCenter 詳細資料新增至組態伺服器](#add-vmware-server-to-the-vault)。
2. [停用虛擬機器的複寫](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)您正在規劃移轉。
3. 完成選取虛擬機器移轉至新的 vCenter。
4. 現在，保護已移轉的虛擬機器，透過[啟用保護時，選取新的 vCenter](vmware-azure-tutorial.md#enable-replication)。

> [!TIP]
> 如果要移轉的虛擬機器數目**更高版本**在舊的 vCenter，保留的虛擬機器數目，更新新的 vCenter，使用此處提供的指示的 IP 位址。 會保留在舊的 vCenter 上的幾個虛擬機器[停用複寫](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure);[將新的 vCenter 詳細資料新增至組態伺服器](#add-vmware-server-to-the-vault)，並啟動 **[啟用保護](vmware-azure-tutorial.md#enable-replication)** 。

## <a name="frequently-asked-questions"></a>常見問題集

1. 如果受保護的虛擬機器不會移動到另一台 ESXi 主機，它會影響複寫？

    否，這不會影響進行中的複寫。 不過，[請確實部署主要目標伺服器具備足夠權限](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. 什麼是連接埠號碼用於 vCenter 和其他 Site Recovery 之間的通訊元件嗎？

    預設連接埠為 443。 組態伺服器會透過此連接埠來存取 vCenter/vSphere 主機資訊。 如果您想要更新此資訊，請按一下[此處](#modify-the-vcenter-ip-address-and-port)。
