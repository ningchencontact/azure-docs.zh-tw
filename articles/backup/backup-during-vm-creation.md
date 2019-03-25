---
title: 當您使用 Azure 備份建立 Azure VM 啟用備份
description: 描述如何使用 Azure 備份建立 Azure VM 時啟用備份。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: d96b898c8f72abd7e4eb3522ae046e9fc926f387
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403573"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>建立 Azure VM 時啟用備份

您可以使用 Azure 備份服務來備份 Azure 虛擬機器 (Vm)。 根據備份原則中指定的排程備份 Vm，並從備份建立復原點。 復原點會儲存在復原服務保存庫。

這篇文章會說明如何啟用備份，當您建立虛擬機器 (VM) 時，在 Azure 入口網站中。  

## <a name="before-you-start"></a>開始之前

- [檢查](backup-support-matrix-iaas.md#supported-backup-actions)如果當您建立 VM 時，會啟用備份支援哪些作業系統。

## <a name="sign-in-to-azure"></a>登入 Azure

如果您不已登入您的帳戶，登入[Azure 入口網站](https://portal.azure.com)。
 
## <a name="create-a-vm-with-backup-configured"></a>建立具有已設定備份的 VM

1. 在 Azure 入口網站中，按一下**建立資源**。

2. 在 Azure Marketplace 中，按一下**計算**，然後選取 VM 映像。

3. 根據 VM 設定[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)或是[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)指示。

4. 在 **管理**索引標籤**啟用備份**，按一下 **上**。
5. Azure 備份備份至復原服務保存庫。 按一下 **新建**如果您沒有現有的保存庫。
6. 接受建議的保存庫名稱，或指定您自己。
7. 指定或建立保存庫會位於資源群組。 資源群組的保存庫可以從 VM 資源群組不同。

    ![啟用 VM 的備份](./media/backup-during-vm-creation/enable-backup.png) 

8. 接受預設備份原則，或修改的設定。
    - 備份原則會指定如何經常建立備份快照的 VM，並保留這些備份複本的時間長度。 
    - 預設的原則一天一次備份 VM。
    - 您可以自訂您自己進行每日或每週備份 Azure VM 的備份原則。
    - [了解更多](backup-azure-vms-introduction.md#backup-and-restore-considerations)有關 Azure Vm 的備份考量。
    - [了解更多](backup-instant-restore-capability.md)需立即還原功能。

      ![預設備份原則](./media/backup-during-vm-creation/daily-policy.png) 


## <a name="start-a-backup-after-creating-the-vm"></a>開始備份之後建立 VM 

您的 VM 備份將會根據您的備份原則執行。 不過，我們建議您執行初始備份。 

建立 VM 之後，執行下列作業：

1. 在 VM 屬性中，按一下**備份**。 執行初始備份之前，VM 狀態是初始備份擱置中
2. 按一下 **立即備份**来執行隨選備份。

    ![執行隨選備份](./media/backup-during-vm-creation/run-backup.png) 

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>使用 Resource Manager 範本來部署受保護的 VM

先前的步驟說明如何使用 Azure 入口網站建立虛擬機器，並在復原服務保存庫中保護它。 若要快速部署一或多個 Vm，並在復原服務保存庫中保護它們，請參閱範本[部署 Windows VM，並啟用備份](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)。



## <a name="next-steps"></a>後續步驟 

既然您已來保護您的 VM，了解如何管理並將它們還原。

- [管理和監視 Vm](backup-azure-manage-vms.md) 
- [還原 VM](backup-azure-arm-restore-vms.md) 

如果您遇到任何問題，[檢閱](backup-azure-vms-troubleshoot.md)疑難排解指南。
