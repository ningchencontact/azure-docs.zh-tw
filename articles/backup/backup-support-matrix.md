---
title: Azure 備份支援矩陣
description: 摘要說明 Azure 備份服務的支援設定和限制。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 99dd3c0b07307f2d0bf97dbff697e32e648705ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66400166"
---
# <a name="azure-backup-support-matrix"></a>Azure 備份支援矩陣

您可以使用[Azure 備份](backup-overview.md)將資料備份到 Microsoft Azure 雲端平台。 本文摘要說明的一般支援設定和針對 Azure 備份的案例和部署的限制。

另有其他的支援矩陣可供使用：

- 支援矩陣[Azure 虛擬機器 (VM) 備份](backup-support-matrix-iaas.md)
- 使用備份的支援矩陣[System Center Data Protection Manager (DPM) / Microsoft Azure 備份伺服器 (MABS)](backup-support-matrix-mabs-dpm.md)
- 使用備份的支援矩陣[Microsoft Azure 復原服務 (MARS) 代理程式](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>保存庫支援

Azure Backup 使用復原服務保存庫來協調和管理備份。 它也會使用保存庫來儲存備份資料。

下表描述的復原服務保存庫的功能：

**功能** | **詳細資料**
--- | ---
**訂用帳戶中的保存庫** | 一個訂用帳戶中最多可以有 500 個復原服務保存庫。
**保存庫中的機器** | 在單一保存庫中多達 1000 個 Azure Vm。<br/><br/> 可在單一保存庫中註冊最多 50 個 MABS 伺服器。
**保存庫儲存體中的資料來源** | 最大 54400 GB。 Azure VM 備份沒有限制。
**備份至保存庫** | **Azure Vm:** 一天一次。<br/><br/>**DPM/MABS 所保護的機器：** 一天兩次。<br/><br/> **使用 MARS 代理程式來直接備份的機器：** 一天 3 次。
**保存庫之間的備份** | 在區域內備份。<br/><br/> 您在每個 Azure 區域中都必須有一個保存庫包含您想要備份的 VM。 您無法備份至不同的區域。
**移動保存庫** | 您可以[移動保存庫](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault)跨訂用帳戶，或在相同的訂用帳戶中的資源群組之間。
**在保存庫之間移動資料** | 不支援移動保存庫之間的備份資料。
**修改保存庫儲存體類型** | 備份會儲存之前，您可以修改儲存體複寫類型 （異地備援儲存體或本機備援儲存體） 的保存庫。 開始在保存庫中進行備份後，即無法修改複寫類型。

## <a name="on-premises-backup-support"></a>內部部署備份支援

如果您想要備份內部部署機器支援的功能如下：

**機器** | **備份的內容** | **位置** | **特性**
--- | --- | --- | ---
**透過 MARS 代理程式直接備份 Windows 機器** | 檔案、資料夾、系統狀態 | 備份至復原服務保存庫。 | 備份 一天 3 次<br/><br/> 沒有應用程式感知備份<br/><br/> 還原檔案、 資料夾、 磁碟區
**透過 MARS 代理程式直接備份 Linux 機器** | 不支援備份
**備份至 DPM** | 檔案、 資料夾、 磁碟區、 系統狀態、 應用程式資料 | 備份至本機 DPM 儲存體。 然後，DPM 再備份至保存庫。 | 應用程式感知快照集<br/><br/> 完整的資料粒度的備份和復原<br/><br/> Linux 支援的 Vm (Hyper-v/VMware)<br/><br/> 不支援的 oracle
**備份到 MABS** | 檔案、 資料夾、 磁碟區、 系統狀態、 應用程式資料 | 備份至 MABS 本機儲存體。 然後，MABS 再備份至保存庫。 | 應用程式感知快照集<br/><br/> 完整的資料粒度的備份和復原<br/><br/> Linux 支援的 Vm (Hyper-v/VMware)<br/><br/> 不支援的 oracle

## <a name="azure-vm-backup-support"></a>Azure VM 備份支援

### <a name="azure-vm-limits"></a>Azure VM 限制

**限制** | **詳細資料**
--- | ---
**Azure VM 資料磁碟** | 16 個
**Azure VM 資料磁碟大小** | 個別的磁碟可以是最多 4,095 GB

### <a name="azure-vm-backup-options"></a>Azure VM 備份選項

如果您想要備份 Azure Vm 支援的功能如下：

**機器** | **備份的內容** | **位置** | **特性**
--- | --- | --- | ---
**使用 VM 延伸模組的 azure VM 備份** | 整個 VM | 備份到保存庫。 | 當您為 VM 啟用備份時安裝的延伸模組。<br/><br/> 如果需要備份的一天一次。<br/><br/> 適用於 Windows Vm; 的應用程式感知備份適用於 Linux Vm 的檔案一致性備份。 您可以使用自訂指令碼來設定 Linux 機器的應用程式一致性。<br/><br/> 還原 VM 或磁碟。<br/><br/> 無法將 Azure VM 備份到內部部署位置。
**使用 MARS 代理程式的 azure VM 備份** | 檔案、資料夾、系統狀態 | 備份到保存庫。 | 如果需要備份的一天 3 次。<br/><br/> 如果您想要備份特定檔案或資料夾，而不是整個 VM，MARS 代理程式可以執行連同 VM 延伸模組。
**使用 DPM 的 Azure VM** | 檔案、 資料夾、 磁碟區、 系統狀態、 應用程式資料 | 備份至執行 DPM 的 Azure VM 的本機儲存體。 然後，DPM 再備份至保存庫。 | 應用程式感知快照集。<br/><br/> 備份和復原的完整細微性。<br/><br/> VM (Hyper-V/VMware) 支援 Linux。<br/><br/> 不支援 Oracle。
**使用 MABS 的 Azure VM** | 檔案、 資料夾、 磁碟區、 系統狀態、 應用程式資料 | 備份至 Azure vm 的 MABS 執行的本機儲存體。 然後，MABS 再備份至保存庫。 | 應用程式感知快照集。<br/><br/> 備份和復原的完整細微性。<br/><br/> VM (Hyper-V/VMware) 支援 Linux。<br/><br/> 不支援 Oracle。

## <a name="linux-backup-support"></a>Linux 備份支援

如果您想要備份的 Linux 機器支援的功能如下：

**備份類型** | **Linux (Azure 背書)**
--- | ---
**執行 Linux 的內部部署機器的直接備份** | 不支援。 MARS 代理程式可以只在 Windows 機器上安裝。
**使用代理程式擴充功能，將執行 Linux 的 Azure VM 備份** | 使用應用程式一致備份[自訂指令碼](backup-azure-linux-app-consistent.md)。<br/><br/> 檔案層級復原。<br/><br/> 從復原點或磁碟建立 VM 以進行還原。
**使用 DPM 來備份內部部署或執行 Linux 的 Azure VM** | HYPER-V 和 VMWare 上 Linux 來賓 Vm 的檔案一致備份。<br/><br/> HYPER-V 和 VMWare Linux 來賓 Vm 的 VM 還原。<br/><br/> 檔案一致性備份 Azure 虛擬機器無法使用。
**使用 MABS 備份內部部署機器或執行 Linux 的 Azure VM** | HYPER-V 和 VMWare 上 Linux 來賓 Vm 的檔案一致備份。<br/><br/> HYPER-V 和 VMWare Linux 來賓 Vm 的 VM 還原。<br/><br/> 檔案一致性備份不適用於 Azure VM。

## <a name="daylight-saving-time-support"></a>日光節約時間的支援

Azure 備份不支援自動時鐘調整的 Azure VM 備份的日光節約時間。 請視需要手動修改備份原則。

## <a name="disk-deduplication-support"></a>磁碟重複資料刪除支援

磁碟重複資料刪除支援如下︰

- 磁碟重複資料刪除是受支援之內部，當您使用 DPM 或 MABs 備份執行 Windows 的 HYPER-V Vm。 Windows Server 虛擬硬碟 (Vhd) 連接至 VM 作為備份儲存體上執行重複資料刪除 （主機層級）。
- 在 Azure 中不支援對任何備份元件進行重複資料刪除。 當在 Azure 中部署 DPM 和 MABS 時，連接至 VM 的儲存體磁碟不能被重複資料刪除。

## <a name="security-and-encryption-support"></a>安全性和加密的支援

Azure 備份支援傳輸中和待用資料加密。

### <a name="network-traffic-to-azure"></a>Azure 的網路流量

- 從伺服器備份到復原服務保存庫的流量會使用進階加密標準 256 加密。
- 備份資料會透過安全的 HTTPS 連結來傳送。
- 備份資料會儲存在復原服務保存庫，以加密形式。
- 只有您持有可將此資料解除鎖定的複雜密碼。 Microsoft 無法解密在任何時間點備份的資料。

    > [!WARNING]
    > 設定保存庫後，只有您能夠存取加密金鑰。 Microsoft 絕不會持有金鑰複本，也沒有存取金鑰的權限。 如果金鑰遺失，Microsoft 將無法復原備份資料。

### <a name="data-security"></a>資料安全性

- 當您正在備份 Azure Vm 時，您需要設定加密*內*虛擬機器。
- Azure 備份支援 Azure 磁碟加密，其會在 Windows 虛擬機器上使用 BitLocker，而在 Linux 虛擬機器上使用 **dm-crypt**。
- 在後端，使用 Azure 備份[Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)，以保護待用資料。

**機器** | **傳輸中** | **待用**
--- | --- | ---
**在內部部署 Windows 機器，而不需要 DPM/MABS** | ![是][green] | ![是][green]
**Azure VM** | ![是][green] | ![是][green]
**在內部部署 Windows 機器或使用 DPM 的 Azure Vm** | ![是][green] | ![是][green]
**在內部部署 Windows 機器或使用 MABS Azure Vm** | ![是][green] | ![是][green]

## <a name="compression-support"></a>壓縮支援

Backup 支援備份的資料傳輸的壓縮下, 表摘要說明。

- 針對 Azure Vm，VM 延伸模組資料直接從 Azure 儲存體帳戶會透過讀取存放網路中，因此不需要壓縮此流量。
- 如果您使用 DPM 或 MABS，您可以藉由壓縮資料，才能備份來節省頻寬。

**機器** | **壓縮到 MABS DPM (TCP)** | **壓縮至保存庫 (HTTPS)**
--- | --- | ---
**直接備份內部部署 Windows 機器** | NA | ![是][green]
**使用 VM 擴充功能的 Azure Vm 備份** | NA | NA
**使用 MABS/DPM 在內部部署/Azure 機器上的備份** | ![是][green] | ![是][green]

## <a name="retention-limits"></a>保留期限

**設定** | **限制**
--- | ---
**每個受保護的執行個體 （電腦或工作負載） 的最大復原點** | 9,999
**最大復原點的到期時間** | 沒有限制
**以 DPM/MABS 的最大備份頻率** | 每隔 15 分鐘 (SQL Server)<br/><br/> 一小時一次針對其他工作負載
**若要保存庫的最大備份頻率** | **內部部署 Windows 機器或執行 MARS 的 Azure Vm:** 三個每日<br/><br/> **DPM/MABS:** 每天兩次<br/><br/> **Azure VM 備份：** 每天
**復原點保留期** | 每日、每週、每月、每年
**最大保留期限** | 依照備份頻率而定
**DPM/MABS 磁碟上的復原點** | 檔案伺服器; 64應用程式伺服器的 448 <br/><br/>在內部部署 DPM 的無限制的磁帶復原點

## <a name="next-steps"></a>後續步驟

- [檢閱](backup-support-matrix-iaas.md) Azure VM 備份的支援矩陣。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
