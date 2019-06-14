---
title: Azure 備份架構
description: 概略說明 Azure 備份服務所使用的架構、元件和程序。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 98ffe145103b4be04014627ed04d04dcf7542015
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60647358"
---
# <a name="azure-backup-architecture"></a>Azure 備份架構

您可以使用[Azure 備份服務](backup-overview.md)將資料備份到 Microsoft Azure 雲端平台。 本文將摘要說明 Azure 備份的架構、元件和程序。 

## <a name="what-does-azure-backup-do"></a>Azure 備份有何功能？

Azure 備份會備份資料，機器狀態，並在內部部署機器和 Azure 虛擬機器 (VM) 執行個體上執行的工作負載。 Azure 備份有多種案例。

## <a name="how-does-azure-backup-work"></a>Azure 備份如何運作？

您可以使用多種方法來支援機器和資料：

- **備份內部部署機器**：
    - 您可以備份內部部署 Windows 機器直接部署至 Azure 所使用的 Azure 備份 Microsoft Azure 復原服務 (MARS) 代理程式。 不支援 Linux 機器。
    - 您可以備份到備份伺服器 （System Center Data Protection Manager (DPM) 或 Microsoft Azure 備份伺服器 (MABS)） 的內部部署機器。 您再備份至 Azure 復原服務保存庫的備份伺服器。

- **備份 Azure VM**：
    - 您可以直接備份 Azure VM。 Azure 備份 Azure VM 代理程式正在 VM 上安裝備份擴充功能。 此擴充功能會備份整個 VM。
    - 您可以執行 MARS 代理程式來備份 Azure VM 上的特定檔案和資料夾。
    - 您可以備份 Azure Vm 到執行於 Azure，MABS，您可以再備份 MABS 復原服務保存庫。

深入了解[您可以將備份](backup-overview.md)和 關於[支援的備份案例](backup-support-matrix.md)。

## <a name="where-is-data-backed-up"></a>資料會備份至何處？

Azure 備份會在復原服務保存庫中儲存備份資料。 保存庫是用來保存資料，例如備份、 復原點和備份原則的 Azure 中的線上儲存實體。

復原服務保存庫具有下列功能：

- 保存庫可輕鬆地組織您的備份資料，同時可減輕管理負擔。
- 在一個 Azure 訂用帳戶中，您最多可以建立 500 個保存庫。
- 您可以監視備份保存庫，包括 Azure Vm 和內部部署機器中的項目。
- 您可以使用 Azure [角色型存取控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 來管理保存庫存取。
- 您可以指定如何複寫保存庫中的資料以提供備援性：
    - **本地備援儲存體 (LRS)** ：若要防止資料中心失敗，您可以使用 LRS。 LRS 會將資料複寫至儲存體縮放單位。 [深入了解](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)。
    - **異地備援儲存體 (GRS)** ：若要防止全區停電，您可以使用 GRS。 GRS 會將資料複寫到次要區域。 [深入了解](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。 
    - 根據預設，復原服務保存庫會使用 GRS。 

## <a name="backup-agents"></a>備份代理程式

Azure 備份提供不同的備份代理程式，視何種機器正在進行備份：

**代理程式** | **詳細資料** 
--- | --- 
**MARS 代理程式** | <ul><li>將檔案、 資料夾和系統狀態備份的個別內部部署 Windows Server 機器上執行。</li> <li>將檔案、 資料夾和系統狀態備份的 Azure Vm 上執行。</li> <li>若要將 DPM/MABS 本機儲存體磁碟備份至 Azure 的 DPM/MABS 伺服器上執行。</li></ul> 
**Azure VM 擴充功能** | 會在 Azure VM 上執行以備份至保存庫。

## <a name="backup-types"></a>備份類型

下表說明不同類型的備份，並使用它們：

**備份類型** | **詳細資料** | **使用量**
--- | --- | ---
**Full** | 完整備份包含整個資料來源。 需要更多的網路頻寬比差異或增量備份。 | 用於初始備份。
**差異** |  差異備份會儲存自從初始完整備份之後變更的區塊。 使用較少量的網路和儲存體，並不會保留不變的資料的備援複本。<br/><br/> 因為較新的備份之間未變更的資料區塊會傳輸並儲存，效率不佳。 | Azure 備份並未使用。
**累加** | 增量備份會儲存上次備份後所變更的資料區塊。 儲存體和網路效率較高。 <br/><br/> 使用增量備份中,，則不需要額外使用完整備份。 | 供 DPM/MABS 用於磁碟備份，並且用於所有備份至 Azure 的作業。

## <a name="sql-server-backup-types"></a>SQL Server 備份類型

下表說明不同的使用 SQL Server 資料庫及它們的使用頻率的備份類型：

**備份類型** | **詳細資料** | **使用量**
--- | --- | ---
**完整備份** | 完整資料庫備份會備份整個資料庫。 它包含在特定資料庫或一組檔案群組或檔案中的所有資料。 完整備份也包含足夠的記錄檔，以復原該資料。 | 您每天最多可以觸發一次完整備份。<br/><br/> 您可以選擇依照每天或每週的時間間隔，進行完整備份。
**差異備份** | 差異備份是以最新、 先前的完整資料備份為基礎。<br/><br/> 只會擷取自完整備份後已變更的資料。 |  您每天最多可以觸發一次差異備份。<br/><br/> 您無法在同一天設定完整備份和差異備份。
**交易記錄備份** | 記錄備份最高允許特定秒的時間點還原。 | 您最多可以每隔 15 分鐘設定交易記錄備份一次。

### <a name="comparison-of-backup-types"></a>備份類型的比較

每一種備份的儲存體耗用量、復原時間目標 (RTO) 以及網路耗用量都不相同。 下圖顯示備份類型的比較：

- 資料來源的包含 10 個儲存體區塊，A1-A10，每月備份。
- 區塊 A2、A3、A4 和 A9 在第一個月有所變更，而 A5 則在下個月有所變更。
- 若使用差異備份，將會在第二個月備份有所變更的區塊 A2、A3、A4 和 A9。 在第三個月會再次備份這一些相同區塊，以及有所變更的區塊 A5。 在執行下次完整備份之前，都會繼續備份有所變更的區塊。
- 增量備份，在第二個月區塊 A2、 A3、 A4、 和 A9 會標示為已變更，並傳送。 在第三個月，只標示區塊 A5 有所變更，而加以傳輸。 

![顯示備份方法的比較的影像](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>備份功能

下表摘要說明不同類型的備份支援的功能：

**功能** | **在內部部署 Windows Server 機器 （直接支援）** | **Azure VM** | **機器或使用 DPM/MABS 應用程式**
--- | --- | --- | ---
備份至保存庫 | ![是][green] | ![是][green] | ![是][green] 
備份至 DPM/MABS 磁碟，然後到 Azure | | | ![是][green] 
壓縮要備份的傳輸資料 | ![是][green] | 傳輸資料時不使用壓縮。 儲存體會略為膨脹，但還原速度較快。  | ![是][green] 
執行增量備份 |![是][green] |![是][green] |![是][green] 
備份已刪除重複資料的磁碟 | | | ![部分][yellow]<br/><br/> 僅用於內部部署的 DPM/MABS 伺服器。 

![資料表索引鍵](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>架構：直接備份 Azure VM

1. 當您啟用 Azure VM 備份時，備份將會根據您指定的排程執行。
1. 期間第一次的備份，備份擴充功能會安裝在 VM 上，如果 VM 正在執行。
    - 適用於 Windows Vm，請安裝 VMSnapshot 延伸模組。
    - 針對 Linux Vm，會安裝 VMSnapshot Linux 延伸模組。
1. 延伸模組的快照存放裝置層級。 
    - 對於執行 Windows Vm，備份會協調與 Windows 磁碟區陰影複製服務 (VSS) 建立 VM 的應用程式一致快照集。 根據預設，完整的 VSS 備份會備份。 如果「備份」無法建立應用程式一致快照集，則會建立檔案一致快照集。
    - 針對 Linux Vm，會進行檔案一致的快照集備份。 對於應用程式一致快照集，您需要以手動方式自訂前置/後置指令碼。
    - 藉由平行備份每個 VM 磁碟，可將備份作業最佳化。 對於每個要備份的磁碟，Azure 備份會讀取磁碟上的區塊，並僅儲存已變更的資料。 
1. 建立快照集之後，資料會傳輸至保存庫。 
    - 只會將區塊的最後一次備份會複製之後變更的資料。
    - 資料不會加密。 Azure 備份可以備份使用 Azure 磁碟加密已加密的 Azure Vm。
    - 快照集資料可能不會立即複製到保存庫。 在尖峰時間，備份可能需要幾個小時。 適用於 VM 的備份時間總計會小於 24 小時的每日備份原則。
1. 將資料傳送至保存庫之後，會移除快照集，並建立復原點。

Azure Vm 需要網際網路存取的控制命令。 如果您正在備份 VM (例如，SQL Server database backups) 內的工作負載後, 端資料也會需要網際網路存取。 

![備份 Azure VM](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>架構：在內部部署 Windows Server 機器或 Azure VM 的檔案或資料夾直接備份

1. 若要設定的案例中，您可以下載並安裝在電腦上的 MARS 代理程式。 然後選取要備份的項目、 執行備份的時機和保留時間長度它們會在 Azure 中。
1. 根據您的備份設定，執行初始備份。
1. MARS 代理程式會使用 VSS 來選取要備份的磁碟區的時間點快照。
    - MARS 代理程式會同時使用只在 Windows 系統寫入作業，在擷取快照集。
    - 代理程式並不使用任何應用程式 VSS 寫入器，因為它不會擷取應用程式一致快照集。
1. 使用 VSS 快照集之後，MARS 代理程式，請在 設定備份時所指定的快取資料夾中建立虛擬硬碟 (VHD)。 代理程式也會儲存每個資料區塊的總和檢查碼。
1. 除非您執行臨機操作備份，可根據您指定的排程執行增量備份。
1. 執行增量備份時，會識別已變更的檔案，並建立新的 VHD。 VHD 是壓縮和加密，並再傳送至保存庫。
1. 增量備份完成之後，新的 VHD 會合併建立初始複寫之後的 VHD。 這個合併的 VHD 會提供要用於進行中的備份比較的最新狀態。

![使用 MARS 代理程式的內部部署 Windows Server 機器備份](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>架構：備份至 DPM/MABS

1. 您想要保護的電腦上安裝 DPM 或 MABS 保護代理程式。 然後，您會將機器加入 DPM 保護群組。
    - 若要保護內部部署機器，DPM 或 MABS 伺服器必須位於內部部署環境。
    - 若要保護 Azure VM，MABS 伺服器必須位於 Azure 中，以 Azure VM 的形式執行。
    - 您可以使用 DPM/MABS，來保護備份磁碟區、 共用、 檔案和資料夾。 您也可以保護電腦的系統狀態 （裸機），以及您可以使用應用程式感知備份設定保護特定應用程式。
1. 當您設定保護的電腦或 DPM/MABS 中的應用程式時，您選取備份的 MABS/DPM 的本機磁碟進行短期儲存和 Azure 線上保護。 此外，您也會指定何時應執行備份至本機 DPM/MABS 儲存體，以及何時應執行線上備份至 Azure。
1. 受保護的工作負載磁碟備份至本機 MABS/DPM 磁碟，根據您指定的排程。
4. DPM/MABS 磁碟所備份的保存庫的 DPM/MABS 伺服器執行的 MARS 代理程式。

![電腦與 DPM 或 MABS 保護工作負載的備份](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM 儲存體

Azure VM 會使用磁碟來儲存其作業系統、應用程式和資料。 每個 Azure VM 有至少兩個磁碟： 作業系統和暫存磁碟的磁碟。 Azure Vm 也可以讓應用程式資料的資料磁碟。 磁碟會儲存為 VHD。

- Vhd 儲存在 Azure 中的標準或進階儲存體帳戶中的分頁 blob 為：
    - **標準儲存體：** 為執行的工作負載不受延遲影響的 VM 提供可靠、低成本的磁碟支援。 標準的固態硬碟 (SSD) 磁碟或標準的硬碟磁碟機 (HDD) 磁碟，可以使用標準儲存體。
    - **進階儲存體：** 高效能磁碟支援。 使用進階 SSD 磁碟。
- 磁碟有不同的效能層級：
    - **標準的 HDD 磁碟：** 採用 HDD，可用於符合成本效益的儲存。
    - **標準的 SSD 磁碟：** 結合了進階 SSD 磁碟與標準的 HDD 磁碟的項目。 提供更一致的效能和可靠性比 HDD 上，但仍符合成本效益。
    - **進階 SSD 磁碟：** 受到 Ssd，並執行我 i/o 密集型工作負載的 Vm 提供高效能和低度延遲。
- 磁碟可以是受控或非受控的：
    - **非受控的磁碟：** 傳統 Vm 所使用的磁碟類型。 對於這些磁碟，您可以建立自己的儲存體帳戶，並在建立磁碟時指定該帳戶。 然後，您需要了解如何充分利用您的 vm 的儲存體資源。
    - **受控磁碟**：Azure 會建立，並為您管理儲存體帳戶。 您指定磁碟大小和效能層級，而且 Azure 會為您建立受控的磁碟。 當您新增磁碟，並調整 Vm 大小，Azure 會處理儲存體帳戶。

如需詳細的 Vm 磁碟儲存體和可用的磁碟類型的詳細資訊，請參閱下列文章：

- [適用於 Windows Vm 的 azure 受控的磁碟](../virtual-machines/windows/managed-disks-overview.md)
- [適用於 Linux Vm 的 azure 受控的磁碟](../virtual-machines/linux/managed-disks-overview.md)
- [適用於 Vm 的可用磁碟類型](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>備份及還原 Azure Vm 使用進階儲存體 

您可以使用進階儲存體使用 Azure 備份來備份 Azure Vm:

- 在備份進階儲存體的 Vm 的過程中，備份服務會建立暫存的預備位置，名為*AzureBackup-* ，儲存體帳戶中。 預備位置的大小等於復原點快照集的大小。
- 確定進階儲存體帳戶有足夠的可用空間可容納暫存位置。 [深入了解](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits)。 請勿修改暫存位置。
- 備份作業完成後，就會刪除暫存位置。
- 用於暫存位置的儲存體，價格會與[進階儲存體價格](../virtual-machines/windows/disks-types.md#billing)一致。

當您使用進階儲存體還原 Azure Vm 時，您可以將它們還原為進階或標準儲存體。 一般而言，您會將它們還原至進階儲存體。 但是，如果您需要的 VM 檔案的子集，它可能會比較符合成本效益將它們還原至標準儲存體。

### <a name="back-up-and-restore-managed-disks"></a>備份及還原受控的磁碟

您可以備份 Azure Vm 使用受控磁碟：

- 備份使用受控磁碟的 VM 時，所使用相同的方式與備份任何其他 Azure VM 時相同。 您可以直接從虛擬機器設定備份 VM，或是為復原服務保存庫中的 VM 啟用備份。
- 您可以透過以受控磁碟為基礎的 RestorePoint 集合來備份受控磁碟上的 VM。
- Azure 備份也支援備份具有使用 Azure 磁碟加密已加密的受控磁碟的 Vm。

當您還原具有受控磁碟的 Vm 時，您可以還原至完整的 VM 使用受控磁碟或儲存體帳戶：

- 在還原過程中，Azure 會處理受管理的磁碟。 如果您使用儲存體帳戶選項，您會管理還原程序期間建立的儲存體帳戶。
- 如果您還原已加密的受控的 VM，請確定 VM 的金鑰和祕密都在金鑰保存庫，再開始還原程序。

## <a name="next-steps"></a>後續步驟

- 檢閱的支援矩陣[深入了解支援的功能和限制備份案例](backup-support-matrix.md)。
- 設定備份這些案例的其中一個：
    - [Azure Vm 備份](backup-azure-arm-vms-prepare.md)。
    - [直接備份 Windows 機器](tutorial-backup-windows-server-to-azure.md)，而不使用備份伺服器。
    - [設定 MABS](backup-azure-microsoft-azure-backup.md) 以備份至 Azure，然後將工作負載備份至 MABS。
    - [設定 DPM](backup-azure-dpm-introduction.md) 以備份至 Azure，然後將工作負載備份至 DPM。


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

