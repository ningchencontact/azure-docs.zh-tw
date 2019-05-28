---
title: 何謂 Azure 備份？
description: 提供 Azure 備份服務的概觀，並說明如何將它提供給商務持續性和災害復原 (BCDR) 策略。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 04/24/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bd90d315fd5590a8bd862a1a3397cf8c254fccc8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714293"
---
# <a name="what-is-azure-backup"></a>何謂 Azure 備份？

Azure 備份服務會將資料備份至 Microsoft Azure 雲端。 您可以備份內部部署機器和工作負載，以及 Azure 虛擬機器 (VM)。


## <a name="why-use-azure-backup"></a>為何使用 Azure 備份？

Azure 備份可提供下列主要優點：

- **卸載內部部署備份**：Azure 備份提供了簡單的解決方案，可讓您將內部部署資源備份到雲端。 您不需部署複雜的內部部署備份解決方案，即可取得短期與長期備份。
- **備份 Azure IaaS VM**：Azure 備份提供獨立且隔離的備份，可防止原始資料意外毀損。 備份會儲存在復原服務保存庫中，且具有內建的受控復原點。 設定和調整都十分容易，且備份會最佳化，可在必要時輕易還原。
- **輕鬆調整** - Azure 備份使用 Azure 雲端的基礎功能及無限制調整來提供高可用性，沒有維護或監視的額外負荷。
- **取得無限制的資料傳輸**：Azure 備份不會限制輸入或輸出資料的傳輸，或對傳輸的資料收費。
    - 輸出資料是指還原作業期間傳輸自復原服務保存庫的資料。
    - 如果您使用 Azure 匯入/匯出服務執行離線初始備份以匯出大量資料，則會有輸入資料的相關費用。  [深入了解](backup-azure-backup-import-export.md)。
- **確保資料安全性**：Azure 備份提供解決方案來保護傳輸中和待用的資料。
- **取得應用程式一致備份**：應用程式一致備份表示復原點具有還原備份複本所需的所有資料。 Azure 備份提供應用程式一致備份，確保資料還原不需要其他修正程式。 還原應用程式一致的資料會減少還原時間，讓您能夠快速回到執行狀態。
- **保留短期和長期資料**：您可以使用復原服務保存庫進行短期和長期資料保留。 Azure 不會限制您可在復原服務保存庫中保留資料的時間長度。 您可以無限期保留資料。 Azure 備份的每個受保護執行個體上限為 9999 個復原點。 [深入了解](backup-introduction-to-azure-backup.md#backup-and-retention)這項限制對您的備份需求有何影響。
- **自動儲存管理** - 混合式環境通常需要異質性儲存體 - 部份在內部部署，部份在雲端。 使用 Azure 備份，使用內部部署儲存體裝置無需成本。 Azure 備份會自動配置和管理備份儲存體，且採用隨用隨付模式，因此您只需為已使用的儲存體付費。 [深入了解](https://azure.microsoft.com/pricing/details/backup)定價。
- **多個儲存體選項** - Azure 備份提供兩種類型的複寫，讓您的儲存體/資料保有高可用性。
    - [本地備援儲存體 (LRS)](../storage/common/storage-redundancy-lrs.md) 會將資料複寫至資料中心的儲存體縮放單位三次 (建立三個資料複本)。 此資料的所有複本都存在於相同的區域內。 LRS 是保護資料免於本機硬體失敗的低成本選項。
    - [異地備援儲存體 (GRS)](../storage/common/storage-redundancy-grs.md) 是預設且建議使用的複寫選項。 GRS 會將資料複寫到次要地區 (與來源資料主要位置距離數百英哩)。 GRS 的價格高於 LRS，但它為您的資料提供更高層級的持久性，即使遭受區域性停電也不影響。


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Azure 備份與 Azure Site Recovery 之間有何差異？

Azure 備份與 Azure Site Recovery 都是有助於為您的企業建立商務持續性和災害復原 (BCDR) 策略的服務。 BCDR 有兩個主要目標：

- 確保您商務資料的安全性，且在中斷狀況發生時可以復原。
- 讓您的應用程式和工作負載在計劃性與非計劃性停機期間保有啟動並運作的狀態。

這兩項服務可相輔相成，但功能並不相同。

- **Azure Site Recovery**：Site Recovery 提供適用於內部部署機器和 Azure VM 的災害復原解決方案。 您可將機器從主要位置複寫到次要位置。 當災害發生時，您可以將機器容錯移轉至次要位置，並從該處加以存取。 當一切都恢復正常啟動並運作後，您即可將機器容錯回復，並在主要站台加以復原。
- **Azure 備份**：Azure 備份服務會從內部部署機器和 Azure VM 備份資料。 資料可精細地進行備份和復原，包括備份檔案、資料夾、機器系統狀態，和應用程式感知的資料備份。 Azure 備份處理資料的精細度會高於 Site Recovery。 例如，如果筆記型電腦上的簡報損毀，您可以使用 Azure 備份來還原簡報。 如果您想要確保 VM 組態和資料的安全性且可供存取，則可以使用 Site Recovery。  

您可以透過列表來釐清您的 BCDR 需求。

**目標** | **詳細資料** | **比較**
--- | --- | ---
**資料備份/保留** | 備份資料可以保留並儲存數天、數個月，甚或數年 (若有合規方面的需求)。 | 備份解決方案 (例如 Azure 備份) 可讓您精確挑選您要備份的資料，並精細調整備份和保留原則。<br/><br/> Site Recovery 則不允許這樣的微調。
**復原點目標 (RPO)** | 在需要進行復原的情況下可接受的資料遺失數量。 | 備份的 RPO 有較多變化。<br/><br/> VM 備份的 RPO 通常為一天，而資料庫備份的 RPO 則可能只要 15 分鐘。<br/><br/> 由於複寫會連續或頻繁地執行，使得來源與複本之間的差異很小，因此 Site Recovery 會提供較低的 RPO。
**復原時間目標 (RTO)** |完成復原或還原所需的時間量。 | 由於 RPO 較大，備份解決方案需要處理的資料量通常更多，這會導致 RTO 較長。 例如，根據從異地傳輸磁帶所需的時間，從磁帶還原資料可能需要數天的時間。

## <a name="what-backup-scenarios-are-supported"></a>支援哪些備份案例？

Azure 備份可備份內部部署機器和 Azure VM。

**機器** | **備份案例**
--- | ---
**內部部署備份** |  1) 在內部部署 Windows 機器上執行 Azure 備份 Microsoft Azure 復原服務 (MARS) 代理程式，以備份個別檔案和系統狀態。 <br/><br/>2) 將內部部署機器備份至備份伺服器 (System Center Data Protection Manager (DPM) 或 Microsoft Azure 備份伺服器 (MABS))，然後設定備份伺服器，以備份至 Azure 中的 Azure 備份復原服務保存庫。
**Azure VM** | 1) 啟用個別 Azure VM 的備份。 當您啟用備份時，Azure 備份會將擴充功能安裝至執行於 VM 上的 Azure VM 代理程式。 代理程式會備份整個 VM。<br/><br/> 2) 在 Azure VM 上執行 MARS 代理程式。 如果您要備份 VM 上的個別檔案和資料夾，這將有其效益。<br/><br/> 3) 將 Azure VM 備份至在 Azure 中執行的 DPM 伺服器或 MABS。 然後，使用 Azure 備份將 DPM 伺服器/MABS 備份至保存庫。


## <a name="why-use-a-backup-server"></a>為何要使用備份伺服器？
先將機器和應用程式備份至 MABS/DPM 儲存體，再將 DPM/MABS 儲存體備份至保存庫，可提供下列的優點：

- 備份至 MABS/DPM，可讓您在檔案/資料夾/磁碟區備份和機器狀態備份 (裸機、系統狀態) 以外，額外取得適用於常見應用程式 (例如 SQL Server、Exchange 及 SharePoint) 的應用程式感知備份。
- 對於內部部署機器，您不需要在要備份的每個機器上安裝 MARS 代理程式。 每個機器都會執行 DPM/MABS 保護代理程式，而 MARS 代理程式則只會在 MABS/DPM 上執行。
- 您有更高的彈性和更精細的排程選項可執行備份。
- 您可以在單一主控台中，為多個納入保護群組中的機器進行備份管理。 如果您的應用程式分層於多個機器，而您想要一併加以備份，前述功能尤有效用。

深入了解使用備份伺服器時的[備份運作方式](backup-architecture.md#architecture-back-up-to-dpmmabs)，和備份伺服器的[支援需求](backup-support-matrix-mabs-dpm.md)。

## <a name="what-can-i-back-up"></a>我可以備份什麼？

**機器** | **備份方法** | **備份**
--- | --- | ---
**內部部署 Windows VM** | 執行 MARS 代理程式 | 備份檔案、資料夾、系統狀態。<br/><br/> 不支援 Linux 機器。
**內部部署機器** | 備份至 DPM/MABS | 備份受 [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) 或 [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) 保護的任何項目，包括檔案/資料夾/共用/磁碟區，以及應用程式專屬資料。
**Azure VM** | 執行 Azure VM 代理程式備份擴充功能 | 備份整個 VM
**Azure VM** | 執行 MARS 代理程式 | 備份檔案、資料夾、系統狀態。<br/><br/> 不支援 Linux 機器。
**Azure VM** | 備份至在 Azure 中執行的 MABS/DPM | 備份受 [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) 或 [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) 保護的任何項目，包括檔案/資料夾/共用/磁碟區，以及應用程式專屬資料。

## <a name="what-backup-agents-do-i-need"></a>我需要何種備份代理程式？

**案例** | **代理程式**
--- | ---
**備份 Azure VM** | 不需要代理程式。 Azure VM 備份擴充功能會在您第一次執行 Azure VM 備份時安裝在 Azure VM 上。<br/><br/> 支援 Windows 和 Linux。
**內部部署 Windows 機器的備份** | 直接在機器上下載、安裝並執行 MARS 代理程式。
**使用 MARS 代理程式備份 Azure VM** | 直接在機器上下載、安裝並執行 MARS 代理程式。 MARS 代理程式可與備份擴充功能一起執行。
**將內部部署機器和 Azure VM 備份至 DPM/MABS** | DPM 或 MABS 保護代理程式會在您要保護的機器上執行。 MARS 代理程式會在要備份至 Azure 的 DPM 伺服器/MABS 上執行。

## <a name="which-backup-agent-should-i-use"></a>我應使用哪個備份代理程式？

**備份** | **方案** | **限制**
--- | --- | ---
**我想要備份整個 Azure VM** | 啟用 VM 的備份。 備份擴充功能會在 Windows 或 Linux Azure VM 上自動設定。 | 整個 VM 都會備份 <br/><br/> Windows VM 的備份具應用程式一致性。 Linux 的備份則具檔案一致性。 如果需要 Linux VM 的應用程式感知能力，必須使用自訂指令碼加以設定。
**我想要備份 Azure VM 上的特定檔案/資料夾** | 在 VM 上部署 MARS 代理程式。
**我想要直接備份內部部署 Windows 機器** | 在機器上安裝 MARS 代理程式。 | 您可以將檔案、資料夾和系統狀態備份到 Azure。 備份不是應用程式感知的。
**我想要直接備份內部部署 Linux 機器** | 您必須部署 DPM 或 MABS，以備份至 Azure。 | 不支援 Linux 主機的備份，您只能備份 Hyper-V 或 VMWare 上裝載的 Linux 客體機器。
**我想要備份在內部部署執行的應用程式** | 若要進行應用程式感知備份，機器必須受到 DPM 或 MABS 保護。
**我希望 Azure VM 有精細且彈性的備份和復原設定** | 在 Azure 中執行 MABS/DPM 為 Azure VM 提供保護，以提高備份排程的彈性，並且能有充分的彈性可保護及還原檔案、資料夾、磁碟區、應用程式和系統狀態。

## <a name="how-does-azure-backup-work-with-encryption"></a>Azure 備份如何與加密功能搭配運作？

**加密** | **備份內部部署** | **備份 Azure VM** | **在 Azure VM 上備份 SQL**
--- | --- | --- | ---
待用加密<br/> (將持續性/已儲存資料加密) | 客戶指定的複雜密碼會用來將資料加密 | Azure [儲存體服務加密 (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)會用來將儲存在保存庫中的資料加密。<br/><br/> 備份會儲存資料前自動進行加密。 Azure 儲存體會在擷取資料前進行解密。 目前不支援將客戶管理的金鑰用於 SSE。<br/><br/> 您可以將使用 [Azure 磁碟加密 (ADE)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) 的 VM 備份，以加密作業系統和資料磁碟。 Azure 備份支援以僅 BEK 和包含 BEK 與 [KEK](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/) 的方式加密的 VM。 檢閱[限制](backup-azure-vms-encryption.md#encryption-support)。 | Azure 備份支援已啟用 [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) 的 SQL Server 資料庫或伺服器的備份。 備份功能支援搭配 Azure 所管理的金鑰，或搭配客戶管理的金鑰 (BYOK) 使用 TDE。<br/><br/> 備份功能不會在備份程序中執行任何 SQL 加密作業。
傳輸中加密<br/> (將從某個位置移動到另一個位置的資料加密) | 資料會使用 AES256 加密，然後透過 HTTPS 傳送到 Azure 中的保存庫 | 在 Azure 中，會透過 HTTPS 保護 Azure 儲存體與保存庫之間的資料。 此資料會保持在 Azure 中樞網路上。<br/><br/> iSCSI 可保護在保存庫與 Azure VM 之間傳輸的資料，以用於檔案復原。 安全通道會保護 iSCSI 通道。 | 在 Azure 中，會透過 HTTPS 保護 Azure 儲存體與保存庫之間的資料。<br/><br/> 檔案復原與 SQL 無關。

## <a name="next-steps"></a>後續步驟

- [檢閱](backup-architecture.md)不同備份案例的架構和元件。
- [確認](backup-support-matrix.md)備份的支援功能和設定。

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
