---
title: 在建立期間啟用 Azure VM 備份
description: 如何在建立過程中啟用 Azure 虛擬機器備份。
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: fd2beaa39f03d4f2342c94bf1cd8b8aea7440e62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780438"
---
# <a name="enable-backup-when-you-create-an-azure-virtual-machine"></a>當您建立 Azure 虛擬機器啟用備份

您可以使用 Azure 備份服務來備份 Azure 虛擬機器 (Vm)。 根據備份原則中指定的排程備份 Vm，並從備份建立復原點。 復原點會儲存在復原服務保存庫。

這篇文章會說明如何啟用備份，您要建立虛擬機器 (VM)，在 Azure 入口網站中。  

## <a name="sign-in-to-azure"></a>登入 Azure

如果您不已登入您的帳戶，登入[Azure 入口網站](https://portal.azure.com)。
 
## <a name="create-a-vm-with-backup-configured"></a>建立具有已設定備份的 VM 

1. 在 Azure 入口網站左上角，選取**新增**。

1. 選取 **計算**，然後選取 VM 映像。

1. 輸入 VM 的資訊。 將使用的使用者名稱和您提供密碼，登入 VM。 當您完成時，選取**確定**。 

1. 選取 VM 的大小。  

1. 底下**設定** > **備份**，選取**Enabled**以開啟 備份組態設定。

   - 若要接受預設值，請選取 **[確定]** 上**設定**頁面。 您接著會移至**摘要**頁面，即可建立 VM。 略過步驟 6-8。
   - 若要變更備份組態值，請遵循下一步。  

1. 建立或選取 復原服務保存庫來保存 VM 的備份。 如果您要建立復原服務保存庫，您可以選擇保存庫的資源群組。  

    ![在 VM 建立頁面上的備份組態設定](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > 復原服務保存庫的資源群組可以是不同 VM 的資源群組。  

1. 根據預設，備份原則選取要保護的 VM。 備份原則會指定如何經常建立備份快照，以及要保留這些備份複本。 

   - 您可以接受預設原則，也可以建立或選取不同的備份原則。 
   - 若要編輯備份原則，請選取**備份原則**和變更的值。  

1. 當您完成設定備份組態值時，選取 **[確定]** 上**設定**頁面。  

1. 在 **摘要**頁面上，於通過驗證之後選取**建立**建立會使用設定的備份設定的 VM。 

## <a name="start-a-backup-after-creating-the-vm"></a>開始備份之後建立 VM 

即使您已為您的 VM 設定備份原則，它會是最好的作法是建立一個初始備份。 

VM 建立範本完成之後，請前往**Operations**左側的功能表，然後選取**備份**檢視虛擬機器備份的詳細資料。 您可以使用此頁面：

- 觸發隨選備份。
- 還原完整的 VM 或所有其磁碟。
- 從 VM 備份還原檔案。
- 變更與 VM 相關聯的備份原則。  

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>使用 Resource Manager 範本來部署受保護的 VM

先前的步驟說明如何使用 Azure 入口網站建立虛擬機器，並在復原服務保存庫中保護它。 若要快速部署一或多個虛擬機器，並在復原服務保存庫中保護它們，請參閱範本[部署 Windows VM，並啟用備份](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)。

## <a name="frequently-asked-questions"></a>常見問題集 

### <a name="which-vm-images-support-backup-configuration-during-vm-creation"></a>哪些 VM 映像支援在建立 VM 時的備份設定？

支援下列 Microsoft 所發行的核心映像建立 VM 期間啟用備份。 對於其他的 Vm，VM 建立後，可以啟用備份。 若要進一步了解，請參閱[建立 VM 後啟用備份](quick-backup-vm-portal.md)。

- **Windows** -Windows Server 2016 Datacenter、 Windows Server 2016 Datacenter Core、 Windows Server 2012 Datacenter、 Windows Server 2012 R2 Datacenter、 Windows Server 2008 R2 SP1 
- **Ubuntu** -Ubuntu Server 17.10、 Ubuntu Server 17.04 Ubuntu Server 16.04 (LTS)，Ubuntu Server 14.04 (LTS) 
- **Red Hat** - RHEL 6.7、6.8、6.9、7.2、7.3、7.4 
- **SUSE** - SUSE Linux Enterprise Server 11 SP4、12 SP2、12 SP3 
- **Debian** - Debian 8、Debian 9 
- **CentOS** - CentOS 6.9、CentOS 7.3 
- **Oracle Linux** - Oracle Linux 6.7、6.8、6.9、7.2、7.3 
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>是包含 VM 成本中的備份成本？ 

沒有。 備份的成本會分開 VM 的成本。 如需有關如何備份定價的詳細資訊，請參閱 < [Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)。
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>需要哪些權限才能在 VM 上啟用備份？ 

如果您是 VM 參與者 」 時，您可以啟用 VM 上的備份。 如果您使用自訂角色，您需要下列權限，才能在 VM 上啟用備份： 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
如果您的復原服務保存庫和 VM 有不同的資源群組，請確定您在復原服務保存庫的資源群組中具有寫入權限。  

## <a name="next-steps"></a>後續步驟 

既然您已來保護您的 VM，請參閱下列文章以了解如何管理和還原的 Vm:

- [管理和監視虛擬機器](backup-azure-manage-vms.md) 
- [還原虛擬機器](backup-azure-arm-restore-vms.md) 
