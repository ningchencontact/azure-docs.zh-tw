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
ms.openlocfilehash: ff4ee1d88bd13e647d0f6218d7e9c9b2c57a5a01
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429565"
---
# <a name="azure-backup-support-matrix"></a>Azure 備份支援矩陣

您可以使用 [Azure 備份服務](backup-overview.md)將資料備份至 Microsoft Azure 雲端。 本文將摘要說明 Azure 備份案例和部署的一般支援設定和限制。

另有其他的支援矩陣可供使用：

Azure VM 備份適用的[支援矩陣](backup-support-matrix-iaas.md) 使用 System Center DPM/Microsoft Azure 備份伺服器 (MABS) 的備份適用的[支援矩陣](backup-support-matrix-mabs-dpm.md)使用 Microsoft Azure 復原服務 (MARS) 代理程式的備份適用的[支援矩陣](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>保存庫支援

Azure 備份會使用復原服務保存庫來協調和管理備份，以及儲存已備份的資料。

**設定** | **詳細資料**
--- | ---
**訂用帳戶中的保存庫** | 一個訂用帳戶中最多可以有 500 個復原服務保存庫。
**保存庫中的機器** | 一個保存庫中最多可以有 1000 個 Azure VM。<br/><br/> 可在單一保存庫中註冊最多 50 個 MABS 伺服器。
**保存庫儲存體中的資料來源** | 最大 54400 GB。 Azure VM 備份沒有限制。
**備份至保存庫** | Azure VM：一天一次<br/><br/>受 DPM/MABS 保護的機器：一天兩次<br/><br/> 直接使用 MARS 代理程式備份的機器：一天三次。 
**保存庫之間的備份** | 在區域內備份。<br/><br/> 您在每個 Azure 區域中都必須有一個保存庫包含您想要備份的 VM。 您無法備份至不同的區域。 
**移動保存庫** | 您可以在訂用帳戶之間[移動保存庫](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault)，或在相同訂用帳戶的資源群組之間移動。
**在保存庫之間移動資料** | 不支援在保存庫之間移動已備份的資料。
**修改保存庫儲存體類型** | 您可以在備份儲存之前修改保存庫的儲存體複寫類型 (GRS/LRS)。 開始在保存庫中進行備份後，即無法修改複寫類型。



## <a name="on-premises-backup-support"></a>內部部署備份支援

以下是您備份內部部署機器時所支援的項目。

**機器** | **已備份** | **位置** | **特性**
--- | --- | --- | ---
**透過 MARS 代理程式直接備份 Windows 機器** | 檔案、資料夾、系統狀態 | 備份至復原服務保存庫 | 一天備份三次。<br/><br/> 沒有應用程式感知備份。<br/><br/> 還原檔案、資料夾、磁碟區。
**透過 MARS 代理程式直接備份 Linux 機器** | 不支援備份。
**備份至 DPM** | 檔案、資料夾、磁碟區、系統狀態、應用程式資料。 | 備份至本機 DPM 儲存體。 然後，DPM 再備份至保存庫。 | 應用程式感知快照集<br/><br/> 備份和復原的完整細微性。<br/><br/> VM (Hyper-V/VMware) 支援 Linux。<br/><br/>上也提供本文中使用的原始碼。 不支援 Oracle。
**返回 MABS** | 檔案、資料夾、磁碟區、系統狀態、應用程式資料。 | 備份至 MABS 本機儲存體。 然後，MABS 再備份至保存庫。 | 應用程式感知快照集<br/><br/> 備份和復原的完整細微性。<br/><br/> VM (Hyper-V/VMware) 支援 Linux。<br/><br/>上也提供本文中使用的原始碼。 不支援 Oracle。


## <a name="azure-vm-backup-support"></a>Azure VM 備份支援

### <a name="azure-vm-limits"></a>Azure VM 限制

**限制** | **詳細資料**
--- | ---
Azure VM 資料磁碟 | 限制為 16。
Azure VM 資料磁碟大小 | 個別磁碟最多可達 4095 GB。


### <a name="azure-vm-backup-options"></a>Azure VM 備份選項

以下是您備份 Azure VM 時所支援的項目。

**機器** | **已備份** | **位置** | **特性**
--- | --- | --- | ---
**使用 VM 延伸模組的 Azure VM 備份** | 整個 VM | 備份至保存庫 | 當您為 VM 啟用備份時安裝的延伸模組。<br/><br/> 一天備份一次。<br/><br/> Windows VM 的應用程式感知備份，Linux VM 的檔案一致備份。 您可以使用自訂指令碼為 Linux 機器設定應用程式一致性。<br/><br/> 還原 VM/磁碟。<br/><br/> 無法從 Azure VM 備份至內部部署位置。
**使用 MARS 代理程式的 Azure VM 備份** | 檔案，資料夾 | 備份至保存庫 | 一天備份三次。<br/><br/> 如果您要備份特定的檔案/資料夾，而非整個 VM，則 MARS 代理程式會與 VM 延伸模組一起執行。
**使用 DPM 的 Azure VM** | 檔案、資料夾、磁碟區、系統狀態、應用程式資料。 | 備份至執行 DPM 的 Azure VM 本機儲存體。 然後，DPM 再備份至保存庫。 | 應用程式感知快照集<br/><br/> 備份和復原的完整細微性。<br/><br/> VM (Hyper-V/VMware) 支援 Linux。<br/><br/>上也提供本文中使用的原始碼。 不支援 Oracle。
**使用 MABS 的 Azure VM** | 檔案、資料夾、磁碟區、系統狀態、應用程式資料。 | 已備份至執行 MABS 的 Azure VM 本機儲存體。 然後，MABS 再備份至保存庫。 | 應用程式感知快照集<br/><br/> 備份和復原的完整細微性。<br/><br/> VM (Hyper-V/VMware) 支援 Linux。<br/><br/> 不支援 Oracle。





## <a name="linux-backup-support"></a>Linux 備份支援

以下是您備份 Linux 機器時所支援的項目。

**備份** | **Linux (Azure 背書)**
--- | ---
**直接備份執行 Linux 的內部部署機器**。 | 沒有。 MARS 代理程式只能安裝在 Windows 機器上。
**備份執行 Linux 的 Azure VM (使用代理程式延伸模組)** | 使用[自訂指令碼](backup-azure-linux-app-consistent.md)進行應用程式一致備份。<br/><br/> 檔案層級復原。<br/><br/> 從復原點或磁碟建立 VM 以進行還原。
**使用 DPM 備份執行 Linux 的內部部署或 Azure VM** | Hyper-V 和 VMWare 上 Linux 來賓 VM 的檔案一致性備份<br/><br/> Hyper-V 和 VMWare Linux 來賓 VM 的 VM 還原</br></br> 不適用於 Azure VM 的檔案一致性備份
**使用 MABS 備份執行 Linux 的內部部署機器/Azure VM** | Hyper-V 和 VMWare 上 Linux 來賓 VM 的檔案一致性備份<br/><br/> Hyper-V 和 VMWare Linux 來賓 VM 的 VM 還原</br></br> 檔案一致性備份不適用於 Azure VM。

## <a name="daylight-saving-support"></a>日光節約支援

Azure 備份在進行 Azure VM 備份時不支援依據日光節約變更而自動調整時鐘。 請視需要手動修改備份原則。


## <a name="disk-deduplication-support"></a>磁碟重複資料刪除支援

磁碟重複資料刪除支援如下︰
- 當您使用 DPM 或 MABS 來備份執行 Windows 的 Hyper-V VM 時，內部部署可支援磁碟重複資料刪除。 Windows Server 重複資料刪除會在當作備份儲存體連接至虛擬機器的虛擬硬碟 (VHD) 上，執行重複資料刪除 (主機層級)。
- 在 Azure 中不支援對任何備份元件進行重複資料刪除。 在 Azure 中部署 System Center DPM 和備份伺服器時，連結至 VM 的儲存體磁碟無法進行重複資料刪除。


## <a name="securityencryption-support"></a>安全性/加密支援

Azure 備份支援傳輸中和待用資料的加密：

對 Azure 的網路流量：
- 從伺服器到復原服務保存庫的備份流量會使用進階加密標準 256 進行加密。
- 備份資料會透過安全的 HTTPS 連結來傳送。
- 備份資料會在復原服務保存庫中以加密格式儲存。
- 只有您持有可將此資料解除鎖定的複雜密碼。 Microsoft 無法解密在任何時間點備份的資料。
    > [!WARNING]
    > 設定保存庫後，只有您能夠存取加密金鑰。 Microsoft 絕不會持有金鑰複本，也沒有存取金鑰的權限。 如果金鑰遺失，Microsoft 將無法復原備份資料。
資料安全性：
- 備份 Azure VM 時，您必須在虛擬機器*內*設定加密。
- Azure 備份支援 Azure 磁碟加密，其會在 Windows 虛擬機器上使用 BitLocker，而在 Linux 虛擬機器上使用 **dm-crypt**。
- 在後端，Azure 備份會使用 [Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)，以保護待用資料。


**機器** | **傳輸中** | **待用**
--- | --- | ---
不含 DPM/MABS 的內部部署 Windows 機器 | ![yes][green] | ![yes][green]
Azure VM | ![yes][green] | ![yes][green]
使用 DPM 的內部部署/Azure VM | ![yes][green] | ![yes][green]
使用 MABS 的內部部署/Azure VM | ![yes][green] | ![yes][green]



## <a name="compression-support"></a>壓縮支援

備份支援壓縮備份流量，如下表所說明。 

- 對於 Azure VM，VM 擴充功能會透過儲存體網路直接從 Azure 儲存體帳戶讀取資料，因此不需要壓縮此流量。
- 如果您使用 DPM 或 MABS，則可以先壓縮資料再將其備份至 DPM/MABS，以節省頻寬。

**機器** | **壓縮到 MABS DPM (TCP)** | **壓縮到保存庫 (HTTPS)**
--- | --- | ---
**直接備份內部部署 Windows 機器** | NA | yes
**使用 VM 延伸模組備份 Azure VM** | NA | NA
**使用 MABS/DPM 備份內部部署/Azure 機器 | ![yes][green] | ![yes][green]



## <a name="retention-limits"></a>保留期限

**設定** | **限制**
--- | ---
每個受保護執行個體的復原點數目上限 (機器/工作負載) | 9999
復原點的到期時間上限 | 沒有限制
備份至 DPM/MABS 的頻率上限 | 每隔 15 分鐘 (SQL Server)<br/><br/> 一小時一次 (其他工作負載)。
備份至保存庫的頻率上限 | 執行 MARS 的內部部署 Windows 機器/Azure VM：每天三次<br/><br/> DPM/MABS：每天兩次<br/><br/> Azure VM 備份：一天 1 次
復原點保留期 | 每日、每週、每月、每年。
最大保留期間 | 視備份頻率而定。
DPM/MABS 磁碟上的復原點 | 64 (檔案伺服器)、448 (應用程式伺服器)。<br/><br/> 內部部署 DPM 的磁帶復原點沒有限制。

## <a name="next-steps"></a>後續步驟

- [檢閱](backup-support-matrix-iaas.md) Azure VM 備份的支援矩陣。


[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
