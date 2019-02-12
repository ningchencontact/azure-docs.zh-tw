---
title: 何謂 Azure 備份？
description: 提供 Azure 備份服務的概觀，並說明如何將其部署為商務持續性和災害復原 (BCDR) 策略的一部分。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 298c9fabca9d1994e0b952fdf8b48b70370c3ec2
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490664"
---
# <a name="what-is-azure-backup"></a>何謂 Azure 備份？

Azure 備份服務會將資料備份至 Microsoft Azure 雲端。 您可以備份內部部署機器和工作負載，以及 Azure 虛擬機器 (VM)。


## <a name="why-use-azure-backup"></a>為何使用 Azure 備份？

Azure 備份可提供下列主要優點：

- **卸載內部部署備份**：Azure 備份提供了簡單的解決方案，可讓您將內部部署資源備份到雲端。 您不需部署複雜的內部部署備份解決方案，即可取得短期與長期備份。 您不需再進行磁帶或離站備份。
- **備份 Azure IaaS VM**：Azure 備份提供獨立且隔離的備份，可防止原始資料意外毀損。 備份會儲存在復原服務保存庫中，且具有內建的受控復原點。 設定和調整都十分容易，且備份會最佳化，可在必要時輕易還原。
- **輕鬆調整** - Azure 備份使用 Azure 雲端的基礎功能及無限制調整來提供高可用性 - 沒有維護或監控的額外負荷。 您可以設定提供相關事件資訊的警示，但您不必擔心雲端資料的高可用性問題。
- **取得無限制的資料傳輸** - Azure 備份不會限制輸入或輸出資料的傳輸。 Azure 備份也不會對傳輸資料收取費用。 不過，如果您使用 Azure 匯入/匯出服務匯入大量的資料，會有與輸入資料關聯的費用。 如需這項成本的詳細資訊，請參閱 [Azure Backup 中的離線備份工作流程](backup-azure-backup-import-export.md)。 輸出資料是指還原作業期間傳輸自復原服務保存庫的資料。
- **確保資料安全性**：資料加密可讓您在公用雲端中的資料安全地進行傳輸和儲存。 加密複雜密碼會儲存在本機，並且永遠不會傳送至或儲存在 Azure 中。 如果需要還原任何資料，只有您有加密複雜密碼或金鑰。
- **取得應用程式一致備份**：應用程式一致備份表示復原點具有還原備份複本所需的所有資料。 Azure 備份提供應用程式一致備份，確保資料還原不需要其他修正程式。 還原應用程式一致的資料會減少還原時間，讓您能夠快速回到執行狀態。
- **取得短期和長期保留**：**長期保留** - 您可以使用復原服務保存庫進行短期和長期資料保留。 Azure 不會限制您可在復原服務保存庫中保留資料的時間長度。 只要您喜歡，就可以將資料保留在保存庫中。 Azure 備份的每個受保護執行個體上限為 9999 個復原點。 請參閱本文的[備份和保留](backup-introduction-to-azure-backup.md#backup-and-retention)一節，以了解這項限制對您的備份需求有何影響。
- **自動儲存管理** - 混合式環境通常需要異質性儲存體 - 部份在內部部署，部份在雲端。 使用 Azure 備份，使用內部部署儲存體裝置無需成本。 Azure 備份會自動配置和管理備份儲存體，且採用使用時付費制。 使用時付費表示您只需為有用到的儲存體付費。 如需詳細資訊，請參閱 [Azure 定價文章](https://azure.microsoft.com/pricing/details/backup)。
- **多個儲存體選項** - 高可用性的其中一環是儲存體複寫。 Azure 備份提供兩種複寫類型：[本地備援儲存體](../storage/common/storage-redundancy-lrs.md)和[異地備援儲存體](../storage/common/storage-redundancy-grs.md)。 可根據需求選擇備份儲存體：
    - 本地備援儲存體 (LRS) 會將資料複寫至資料中心的儲存體縮放單位三次 (建立三個資料複本)。 此資料的所有複本都存在於相同的區域內。 LRS 是保護資料免於本機硬體失敗的低成本選項。
    - 異地備援儲存體 (GRS) 是預設且建議的複寫選項。 GRS 會將資料複寫到次要地區 (與來源資料主要位置距離數百英哩)。 GRS 的價格高於 LRS，但它為您的資料提供更高層級的持久性，即使遭受區域性停電也不影響。


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Azure 備份與 Azure Site Recovery 之間有何差異？

Azure 備份與 Azure Site Recovery 都是有助於為您的企業建立商務持續性和災害復原 (BCDR) 策略的服務。 BCDR 有兩個主要目標：

- 確保您商務資料的安全性，且在中斷狀況發生時可以復原。
- 讓您的應用程式和工作負載在計劃性與非計劃性停機期間保有啟動並運作的狀態。

這兩項服務可相輔相成，但功能並不相同。

- **Azure Site Recovery**：Site Recovery 提供適用於內部部署機器和 Azure VM 的災害復原解決方案。 您可將機器從主要位置複寫到次要位置。 當災害發生時，您可以將機器容錯移轉至次要位置，並從該處加以存取。 當一切都恢復正常啟動並運作後，您即可將機器容錯回復，並在主要站台加以復原。
- **Azure 備份**：Azure 備份服務會從內部部署機器和 Azure VM 備份資料。 資料可精細地進行備份和復原，包括備份檔案、資料夾、機器系統狀態，和應用程式感知的資料備份。 Azure 備份處理資料的精細度會高於 Site Recovery。 例如，如果筆記型電腦上的簡報損毀，您可以使用 Azure 備份來還原簡報。 如果您想要確保 VM 組態和資料的安全性且可供存取，則可以使用 Site Recovery。  

您可以透過列表來釐清您的 BCDR 需求。 

**目標** | **詳細資料** | **比較**
--- | --- | --- | --- |
**資料備份/保留** | 備份資料可以保留並儲存數天、數個月，甚或數年 (若有合規方面的需求)。 | 備份解決方案 (例如 Azure 備份) 可讓您精確挑選您要備份的資料，並精細調整備份和保留原則。<br/><br/> Site Recovery 則不允許這樣的微調。
**復原點目標 (RPO)** | 在需要進行復原的情況下可接受的資料遺失數量。 | 備份的 RPO 有較多變化。<br/><br/> VM 備份的 RPO 通常為一天，而資料庫備份的 RPO 則可能只要 15 分鐘。<br/><br/> 由於複寫會連續或頻繁地執行，使得來源與複本之間的差異很小，因此 Site Recovery 會提供較低的 RPO。
**復原時間目標 (RTO)** |完成復原或還原所需的時間量。 | 由於 RPO 較大，備份解決方案需要處理的資料量通常更多，這會導致 RTO 較長。 例如，根據從異地傳輸磁帶所需的時間，從磁帶還原資料可能需要數天的時間。 | 災害復原解決方案 (例如 Site Recovery) 會有較低的 RPO，因為連續/頻繁的複寫通常意味著目標與來源之間具有較高的同步性。 |

## <a name="what-backup-scenarios-are-supported"></a>支援哪些備份案例？

Azure 備份可備份內部部署機器和 Azure VM。

**機器** | **備份案例**
--- | ---
**內部部署機器 (實體/虛擬)** |  您可以備份個別的內部部署機器。<br/><br/>您可以備份受到 System Center Data Protection Manager (DPM) 保護的內部部署機器<br/><br/> 您可以備份受到 Microsoft Azure 備份伺服器 (MABS) 保護的內部部署機器。
**Azure VM** | 您可以備份個別的 Azure VM。<br/><br/> 您可以備份受 DPM 或 MABS 保護的 Azure VM。

### <a name="back-up-servers"></a>備份伺服器

您可以備份內部部署伺服器和工作負載，或是 Azure VM 和其工作負載；先備份到備份伺服器，然後再備份到復原服務保存庫。 

**備份伺服器** | **詳細資料**
--- | ---
**System Center Data Protection Manager (DPM)** | 您可以使用 Azure 備份來備份受 DPM 保護的資料：<br/><br/> - DPM 可以在內部部署環境 (實體或虛擬) 或 Azure 中執行。<br/><br/> - 您可以藉由將資料備份到 DPM 伺服器，來保護在內部部署機器和 Azure VM 上執行的不同資料類型。<br/><br/> 接著，DPM 伺服器可以使用 Azure 備份服務備份到復原服務保存庫。<br/><br/> DPM 伺服器及其保護的機器必須位於相同網路中。 內部部署機器只能由內部部署 DPM 伺服器來保護。 同樣地，Azure VM 只能由執行於 Azure 中的 DPM 來保護。
**Microsoft Azure 備份伺服器 (MABS)** | 您可以使用 Azure 備份來備份受 MABS 保護的資料：<br/><br/> - MABS 可以在內部部署環境 (實體或虛擬) 或 Azure 中執行。<br/><br/> - 您可以藉由將資料備份到 MABS，來保護在內部部署機器和 Azure VM 上執行的不同資料類型。<br/><br/> 接著，MABS 可以使用 Azure 備份服務備份到復原服務保存庫。<br/><br/> - MABS 的功能與 DPM 相似，不同之處在於使用 MABS 時無法備份到磁帶。 MABS 不需要 System Center 授權。<br/><br/> 如同 DPM，內部部署機器只能由內部部署 MABS 來保護。 Azure VM 只能由 Azure 中的 MABS 來保護。

先備份到 DPM/MABS、再備份到保存庫的優點如下：

- 備份至 DPM/MAB，可讓您在檔案/資料夾/磁碟區備份和機器狀態備份 (裸機、系統狀態) 以外，額外取得適用於常見應用程式 (例如 SQL Server、Exchange 及 SharePoint) 的應用程式感知備份。
- 您不需要在要備份的每個機器上安裝 Azure 備份代理程式。 每個機器都會執行 DPM/MABS 保護代理程式，而 Azure 備份/Microsoft Azure 復原服務代理程式則只會在 DPM 伺服器/MABS 上執行。
- 您有更高的彈性和更精細的排程選項可執行備份。
- 您可以在單一主控台中，為多個納入保護群組中的機器進行備份管理。 如果您的應用程式分層於多個機器，而您想要一併加以備份，前述功能尤有效用。

## <a name="what-can-be-backed-up"></a>可備份哪些項目？

**機器** | **備份伺服器** | **備份**
--- | --- | ---
內部部署 Windows VM | 不會備份到 DPM 或 MABS | 備份檔案、資料夾、系統狀態。
Azure VM (Windows 和 Linux) | 不會備份到 DPM 或 MABS | 備份檔案、資料夾、系統狀態。<br/><br/> Windows 機器會有應用程式感知的備份，Linux 機器的則是檔案感知的備份。
內部部署 VM/Azure VM | 受 DPM 保護 | 備份受 DPM 保護的任何項目，包括檔案/資料夾/共用/磁碟區，以及應用程式專屬資料。 [了解](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) DPM 可備份的項目。
內部部署 VM/Azure VM | 受 MABS 保護 | 備份受 MABS 保護的任何項目，包括檔案/資料夾/共用/磁碟區，以及應用程式專屬資料。 [了解](backup-mabs-protection-matrix.md) MABS 可備份的項目。

## <a name="what-backup-agents-do-i-need"></a>我需要何種備份代理程式？
**案例** | **代理程式** | **詳細資料**
--- | --- | ---
內部部署機器 (沒有備份伺服器) | 在 Windows 機器上會執行 Microsoft Azure 復原服務 (MARS) 代理程式。 | 您可在 Azure 備份部署期間下載並安裝 MARS 代理程式。<br/><br/> 僅支援 Windows 機器。
Azure VM (沒有備份伺服器) | 不需要明確的代理程式。 在 Azure VM 上會執行 Azure VM 備份擴充功能。 | 此擴充功能會在您第一次執行 Azure VM 備份時安裝。<br/><br/> 支援 Windows 和 Linux。
受 DPM 保護的內部部署機器/Azure VM。 | 在 DPM 伺服器上會執行 MARS 代理程式。 | 您不需要在個別機器上安裝 MARS 代理程式。<br/><br/> 當您部署 DPM 時，您所保護的每個機器都會安裝 DPM 保護代理程式。 
備份受 MABS 保護的內部部署機器和 Azure VM | 在 MABS 上會執行 MARS 代理程式。 | 您不需要在個別機器上安裝 MARS 代理程式。<br/><br/> 當您部署 MABS 時，您所保護的每個機器都會安裝 MABS 保護代理程式。 


## <a name="which-backup-agent-should-i-use"></a>我應使用哪個備份代理程式？

**備份** | **方案** | **限制**
--- | --- | ---
我想要備份內部部署 Windows 機器。 機器未受 DPM 或 MABS 保護 | 在機器上安裝 MARS 代理程式。 | 您可以將檔案、資料夾和系統狀態備份到 Azure。 備份不是應用程式感知的。
我想要備份內部部署 Linux 機器。 機器未受 DPM 或 MABS 保護。 | 您必須部署 DPM 或 MABS，以備份至 Azure。
我想要備份在內部部署 Windows 機器上執行的應用程式 | 如需應用程式感知備份，Windows 機器必須受到 DPM 或 MABS 保護。
我想要備份 Azure VM | 使用 Azure 備份執行備份。 備份擴充功能會在 Windows 或 Linux Azure VM 上自動設定。 | 會備份 VM 磁碟。<br/><br/> Windows VM 的備份具應用程式一致性。 Linux 的備份則具檔案一致性。 如果需要應用程式感知能力，您必須使用自訂指令碼加以設定。
我希望在備份 Azure VM 時，備份和復原設定能有更細微的彈性 | 在 Azure 中執行 DPM 或 MABS 為 Azure VM 提供保護，以提高備份排程的彈性，並且能有充分的彈性可保護及還原檔案、資料夾、磁碟區、應用程式和系統狀態。


## <a name="next-steps"></a>後續步驟

- [檢閱](backup-architecture.md)不同備份案例的架構和元件。
- [確認](backup-support-matrix.md)備份的支援功能和設定。

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

