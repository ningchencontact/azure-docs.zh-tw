---
title: 關於備份 Azure Vm 使用 Azure 備份常見問題集
description: 備份 Azure Vm 使用 Azure 備份的常見問題的解答。
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sogup
ms.openlocfilehash: 9f233af316bd6022b93a7208bf3fae37e913e6af
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885259"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>常見問題集的問題-備份 Azure Vm

這篇文章回答有關 Azure Vm 備份常見問題[Azure 備份](backup-introduction-to-azure-backup.md)服務。


## <a name="backup"></a>Backup 

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>哪些 VM 映像可以啟用備份時建立它們？
當您建立 VM 時，您可以執行的 Vm 啟用備份[支援的作業系統](backup-support-matrix-iaas.md#supported-backup-actions)
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>是包含 VM 成本中的備份成本？ 

沒有。 備份的成本會分開 VM 的成本。 深入了解[Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)。
 
### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>若要啟用 VM 的備份需要哪些權限？ 

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


### <a name="what-azure-vms-can-you-back-up-using-azure-backup"></a>您可以使用 Azure 備份來備份哪些 Azure VM？

檢閱[支援矩陣](backup-support-matrix-iaas.md)支援詳細資料和限制。

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>隨選備份作業是否會使用與排定備份相同的保留排程？
沒有。 指定隨選備份作業的保留範圍。 根據預設，若從入口網站觸發，則會保留 30 天。

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>我在最近一些 VM 上啟用了 Azure 磁碟加密。 我的備份是否會繼續運作？
提供適用於 Azure 備份來存取 Key Vault 的權限。 在 PowerShell 中指定權限，如 [Azure 備份 PowerShell](backup-azure-vms-automation.md) 文件的**啟用備份**一節中所述。

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>我已將 VM 磁碟遷移至受控磁碟。 我的備份是否會繼續運作？
是，備份會順利運作。 不需要重新設定任何項目。

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>為什麼我在 [設定備份] 精靈中看不到我的 VM？
此精靈只會列出與保存庫位於相同區域且尚未備份的 VM。

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>我的 VM 關閉了。 隨選或排程的備份工作會運作嗎？
是。 備份會在機器關機時執行。 復原點會標示為「當機時保持一致」。

### <a name="can-i-cancel-an-in-progress-backup-job"></a>我是否可以取消進行中的備份作業？
是。 您可以取消處於**正在建立快照**狀態的備份作業。 如果正在從快照集傳輸資料，則無法取消作業。

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprggeonumber-will-my-backups-continue-to-work"></a>我啟用了 Azure 備份服務 （也就是所建立的資源群組鎖定 `AzureBackupRG_<geo>_<number>`)，將我的備份繼續運作？
如果您鎖定 Azure 備份服務所建立的資源群組，備份將會開始失敗，因為沒有上限為 18 個還原點。

使用者必須移除鎖定並清除 使未來的備份成功，還原點集合，從該資源群組[遵循下列步驟](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal)移除還原點集合。

### <a name="does-the-backup-policy-consider-daylight-saving-time-dst"></a>備份原則是否會考慮日光節約時間 (DST)？
沒有。 您本機電腦上的日期與時間會在本機套用目前的日光節約時間。 針對排定備份所設定的時間可能會因為 DST 而與當地時間不同。

### <a name="how-many-data-disks-can-i-attach-to-a-vm-backed-up-by-azure-backup"></a>我可以將多少個資料磁碟連結到由 Azure 備份所備份的 VM？
Azure 備份可以備份最多具有 16 個磁碟的 VM。 [立即還原](backup-instant-restore-capability.md)中提供 16 個磁碟的支援。

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Azure 備份是否支援標準 SSD 受控磁碟？
Azure 備份支援[標準 SSD 受控磁碟](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)。 SSD 受控磁碟提供持久儲存體的新類型的 Azure Vm。 [立即還原](backup-instant-restore-capability.md)中提供 SSD 受控磁碟的支援。

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>我們可以備份具有已啟用寫入加速器 (WA) 之磁碟的 VM 嗎？
您無法在已啟用 WA 的磁碟上建立快照集。 不過，Azure 備份服務可以從備份中排除已啟用 WA 的磁碟。 僅針對已升級到「立即還原」的訂用帳戶，提供對具有已啟用 WA 之磁碟的 VM 進行磁碟排除的支援。

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>我的 VM 具有寫入加速器 (WA) 磁碟並已安裝 SAP HANA。 該如何備份？
Azure 備份無法備份已啟用 WA 的磁碟，但可從備份中排除該磁碟。 不過，備份將不會提供資料庫一致性，因為系統不會備份已啟用 WA 之磁碟的相關資訊。 如果您想要作業系統磁碟備份以及未啟用 WA 的磁碟備份，您可以使用此設定來備份磁碟。

我們正在執行 SAP HANA 備份的私人的預覽使用 15 分鐘的 RPO。 它會以類似 SQL DB 備份的方式建置，並針對 SAP HANA 所認證的協力廠商解決方案使用 backInt 介面。 如果您想要傳送電子郵件來信`AskAzureBackupTeam@microsoft.com`主旨**登入，以在 Azure Vm 中的 SAP hana 備份的私人預覽**。


## <a name="restore"></a>Restore

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>如何決定是否只要還原磁碟，還是要還原整個 VM？
將 VM 還原視為 Azure VM 的快速建立選項。 此選項會變更磁碟名稱、 磁碟、 公用 IP 位址和網路介面名稱所使用的容器。 變更會在建立 VM 時維護唯一的資源。 VM 不會新增至可用性設定組。

如果您想要，您可以使用還原磁碟選項：
  * 自訂所建立的 VM。 例如，變更大小。
  * 新增組態設定有不在備份的時間。
  * 控制所建立資源的命名慣例。
  * 將 VM 新增至可用性設定組。
  * 新增任何其他必須使用 PowerShell 或範本設定的設定。

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>升級至受控磁碟之後，可以還原非受控 VM 磁碟的備份嗎？
是，您可以使用在將磁碟從非受控磁碟遷移至受控磁碟之前所擷取的備份。
- 根據預設，還原 VM 作業會建立非受控 VM。
- 不過，您可以還原磁碟，並使用它們來建立受控 VM。

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>如何將 VM 還原至將 VM 遷移至受控磁碟之前的還原點？
根據預設，還原 VM 作業會使用非受控磁碟來建立 VM。 若要使用受控磁碟建立虛擬機器：
1. [還原至非受控磁碟](tutorial-restore-disk.md#restore-a-vm-disk)。
2. [將還原的磁碟轉換至受控磁碟](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)。
3. [使用受控磁碟建立 VM](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)。

[深入了解](backup-azure-vms-automation.md#restore-an-azure-vm)如何在 PowerShell 中執行此動作。

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>我可以還原已刪除的 VM 嗎？
是。 即使您刪除 VM，還是可以移至保存庫中對應的備份項目，然後從復原點還原。

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>如何將 VM 還原至相同的可用性設定組？
針對受控磁碟，Azure VM 會在還原為受控磁碟時，藉由在範本中提供選項來啟用還原為可用性設定組。 此範本具有稱為**可用性設定組**的輸入參數。

### <a name="how-do-we-get-faster-restore-performances"></a>我們該如何取得更快速的還原效能？
為了更快速的還原效能，我們會採用[立即還原](backup-instant-restore-capability.md)功能。

## <a name="manage-vm-backups"></a>管理 VM 備份

### <a name="what-happens-if-i-modify-a-backup-policy"></a>如果我修改備份原則，會發生什麼事？
VM 會使用已修改或新的原則中的排程和保留期設定來備份。

- 如果延長保留期，就會標示現有的復原點，並依據新原則加以保留。
- 如果縮短保留期，則會標示復原點，以便在下次清除作業中加以剪除然後刪除。

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>如何將 Azure 備份所備份的 VM 移至不同的資源群組？

1. 暫時停止備份並保留備份資料。
2. 將 VM 移至目標資源群組。
3. 在相同或新的保存庫中的重新啟用的備份。

您可以從移動作業之前所建立的可用還原點來還原 VM。
