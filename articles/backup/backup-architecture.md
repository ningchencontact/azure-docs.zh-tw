---
title: Azure 備份架構
description: 概略說明 Azure 備份服務所使用的架構、元件和程序。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: raynew
ms.openlocfilehash: 84890c0658970aa9f61a06764cf902a5e5ee4379
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812551"
---
# <a name="azure-backup-architecture"></a>Azure 備份架構

您可以使用 [Azure 備份服務](backup-overview.md)將資料備份至 Microsoft Azure 雲端。 本文將摘要說明 Azure 備份的架構、元件和程序。 


## <a name="what-does-azure-backup-do"></a>Azure 備份有何功能？

Azure 備份服務可備份在內部部署機器和 Azure VM 上執行的資料、機器狀態和工作負載。 Azure 備份有多種案例。

- **備份內部部署機器**：
    - 您可以使用 Azure 備份將內部部署機器直接備份至 Azure。
    - 您可以使用 System Center Data Protection Manager (DPM) 或 Microsoft Azure 備份伺服器 (MABS) 保護內部部署機器，然後再使用 Azure 備份將 DPM/MABS 上受保護的資料備份至 Azure。
- **備份 Azure VM**：
    - 您可以使用 Azure 備份直接進行 Azure VM 的備份。
    - 您可以使用在 Azure 中執行的 DPM 或 MABS 來保護 Azure VM，然後再使用 Azure 備份來備份 DPM/MABS 上受保護的資料。

深入了解[您可以備份的項目](backup-overview.md)和[支援的備份案例](backup-support-matrix.md)。


## <a name="where-is-data-backed-up"></a>資料會備份至何處？

Azure 備份會將已備份的資料儲存在復原服務保存庫中。 保存庫是 Azure 中的線上儲存實體，用來保存備份複本、復原點和備份原則等資料。

- 復原服務保存庫可輕鬆地組織您的備份資料，同時可減輕管理負擔。
- 在一個 Azure 訂用帳戶中，您最多可以建立 500 個復原服務保存庫。 
- 您可以監視保存庫中的備份項目，包括 Azure VM 和內部部署機器。
- 您可以使用 Azure [角色型存取控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 來管理保存庫存取。
- 您可以指定如何複寫保存庫中的資料以提供備援性：
    - **LRS**：您可以使用本地備援儲存體 (LRS) 來防止資料中心失敗的影響。 LRS 會將資料複寫至儲存體縮放單位。 [深入了解](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)。
    - **GRS**：您可以使用異地備援儲存體 (GRS)：防止全區域服務中斷的影響。 它會將您的資料會複寫至次要區域。 [深入了解](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。 
    - 根據預設，備份的復原服務保存庫會使用 GRS。 



## <a name="how-does-azure-backup-work"></a>Azure 備份如何運作？

Azure 備份會根據預設或自訂的備份原則執行備份作業。 Azure 備份建立備份的方式視案例而定。

**案例** | **詳細資料** 
--- | ---
**直接備份內部部署機器** | 若要直接備份內部部署機器，Azure 備份必須使用 Microsoft Azure 復原服務 (MARS) 代理程式。 此代理程式必須安裝在您要備份的每個機器上。 <br/><br/> 這類型的備份不適用於內部部署 Linux 機器。 
**直接備份 Azure VM** | 若要直接備份 Azure VM，必須在第一次為 VM 執行備份時將 Azure VM 擴充功能安裝在 VM 上。 
**備份受 DPM 或 MABS 保護的機器和應用程式** | 在此案例中，機器/應用程式會先備份至 DPM 或 MABS 本機儲存體。 然後，Azure 備份會將 DPM/MABS 中的資料備份至保存庫。 內部部署機器可由內部部署執行的 DPM/MABS 來保護。 Azure VM 可由執行於 Azure 中的 DPM/MABS 來保護。

[取得概觀](backup-overview.md)，並查看每個案例[支援的項目](backup-support-matrix.md)。


### <a name="backup-agents"></a>備份代理程式

Azure 備份會根據備份類型提供不同的代理程式。

**代理程式** | **詳細資料** 
--- | --- 
**Microsoft Azure 復原服務 (MARS) 代理程式** | 此代理程式會在個別的內部部署 Windows 伺服器上執行，以備份檔案、資料夾和系統狀態<br/><br/> 此代理程式會在 DPM/MABS 伺服器上執行，以備份 DPM/MABS 本機儲存體磁碟。 機器和應用程式會在本機備份至此 DPM/MABS 磁碟。
**Azure VM 擴充功能** | 若要備份 Azure VM，必須將備份擴充功能新增至執行於 VM 上的 Azure VM 代理程式。 


## <a name="backup-types"></a>備份類型

**備份類型** | **詳細資料** | **使用量**
--- | --- | ---
**完整** | 備份包含整個資料來源。<br/><br/> 完整備份會使用較多網路頻寬。 | 用於初始備份。
**差異** |  儲存在初始完整備份之後有所變更的區塊。 使用較少量的網路和儲存體，且不會為未變更的資料保留複本。<br/><br/> 由於在後續備份之間並未變更的資料區塊都會傳輸並儲存，因此效率不佳。 | Azure 備份並未使用。
**累加** | 儲存體和網路效率較高。 只會儲存在前次備份後有所變更的資料區塊。 <br/><br/> 使用增量備份，就不需要再額外執行完整備份。 | 供 DPM/MABS 用於磁碟備份，並且用於所有備份至 Azure 的作業。

### <a name="comparison"></a>比較

每一種備份的儲存體耗用量、復原時間目標 (RTO) 以及網路耗用量都不相同。 下圖顯示備份類型的比較。
- 資料來源 A 由 A1-A10 這 10 個儲存體區塊所組成，每月備份一次。
- 區塊 A2、A3、A4 和 A9 在第一個月有所變更，而 A5 則在下個月有所變更。
- 若使用差異備份，將會在第二個月備份有所變更的區塊 A2、A3、A4 和 A9。 在第三個月會再次備份這一些相同區塊，以及有所變更的區塊 A5。 在執行下次完整備份之前，都會繼續備份有所變更的區塊。
- 若使用增量備份，在第一個月執行完整備份之後，區塊 A2、A3、A4、和 A9 會標示為已變更，並移轉至第二個月。 在第三個月，只標示區塊 A5 有所變更，而加以傳輸。 

![圖中顯示備份方法的比較](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>備份功能

下表摘要說明不同備份類型的功能。

**功能** | **內部部署 Windows 機器 (直接)** | **Azure VM** | **使用 DPM/MABS 的機器/應用程式**
--- | --- | --- | ---
備份至保存庫 | ![是][green] | ![是][green] | ![是][green] 
備份至 DPM/MABS 磁碟，然後備份至 Azure | | | ![是][green] 
壓縮要備份的傳輸資料 | ![是][green] | 傳輸資料時不使用壓縮。 儲存體會略為膨脹，但還原速度較快。  | ![是][green] 
執行增量備份 |![是][green] |![是][green] |![是][green] 
備份已刪除重複資料的磁碟 | | | ![部分][yellow]<br/><br/> 僅用於內部部署的 DPM/MABS 伺服器。 

![資料表索引鍵](./media/backup-architecture/table-key.png)


## <a name="architecture-direct-backup-of-on-premises-windows-machines"></a>架構：內部部署 Windows 機器的直接備份

1. 若要設定案例，您可以在機器上下載並安裝 Microsoft Azure 復原服務 (MARS) 代理程式，並選取要備份的項目、要執行備份的時間，以及備份在 Azure 中的保留期限。
2. 初始備份會根據您的備份設定執行。
3. MARS 代理程式會使用 Windows 磁碟區陰影複製 (VSS) 服務，為選取的備份磁碟區建立時間點快照集。
    - MARS 代理程式只會使用「Windows 系統寫入」來擷取快照集。
    - 此代理程式不會使用任何應用程式 VSS 寫入器，因此不會擷取應用程式一致快照集。
3. 使用 VSS 建立快照集之後，MARS 代理程式會在您設定備份時所指定的快取資料夾中建立 VHD，並儲存每個資料區塊的總和檢查碼。 
4. 除非您執行臨機操作備份，否則都會根據您指定的排程執行增量備份。
5. 執行增量備份時，會識別已變更的檔案，並建立新的 VHD。 它會壓縮和加密，並傳送至保存庫。
6. 增量備份完成後，新的 VHD 會與初始複寫之後建立的 VHD 合併，而提供進行中的備份用來比較的最新狀態。 

![使用 MARS 代理程式備份內部部署 Windows 伺服器](./media/backup-architecture/architecture-on-premises-mars.png)


## <a name="architecture-direct-backup-of-azure-vms"></a>架構：直接備份 Azure VM

1. 當您啟用 Azure VM 的備份時，備份將根據您指定的排程執行。
2. 第一次備份時，會在執行中的 VM 上安裝備份擴充功能。
    - 對於 Windows VM，會安裝 VMSnapshot 擴充功能。
    - 對於 Linux VM，則會安裝 VMSnapshot Linux 擴充功能。
3. 此擴充功能會建立儲存體層級快照集。 
    - 對於執行中的 Windows VM，「備份」會與 VSS 協調，以建立 VM 的應用程式一致快照集。 根據預設，「備份」會建立完整的 VSS 備份。 如果「備份」無法建立應用程式一致快照集，則會建立檔案一致快照集。
    - 對於 Linux VM，「備份」會建立檔案一致備份。 若要建立應用程式一致快照集，您必須手動自訂前置/後置指令碼。
    - 藉由平行備份每個 VM 磁碟，可將備份作業最佳化。 對於每個要備份的磁碟，Azure 備份會讀取磁碟上的區塊，並僅儲存已變更的資料。 
4. 建立快照集之後，資料會傳輸至保存庫。 
    - 只會複製在前次備份後有所變更的資料區塊。
    - 資料不會加密。 Azure 備份可備份使用 Azure 磁碟加密 (ADE) 進行加密的 Azure VM。
    - 快照集資料可能不會立即複製到保存庫。 這在尖峰時間可能需耗費數小時。 根據每日備份原則，VM 的總備份時間會小於 24 小時。
5. 資料傳送至保存庫之後，即會移除快照集，並建立復原點。


![備份 Azure VM](./media/backup-architecture/architecture-azure-vm.png)


## <a name="architecture-back-up-to-dpmmabs"></a>架構：備份至 DPM/MABS

1. 您在要保護的機器上安裝了 DPM 或 MABS 保護代理程式，並將機器新增至 DPM 保護群組。
    - 若要保護內部部署機器，DPM 或 MABS 伺服器必須位於內部部署環境。
    - 若要保護 Azure VM，DPM 或 MABS 伺服器必須位於 Azure 中，以 Azure VM 的形式執行。
    - 使用 DPM/MABS 可讓您保護備份磁碟區、共用、檔案和資料夾。 您可以保護電腦系統狀態/裸機，並使用應用程式感知備份設定來保護特定應用程式。
2. 當您在 DPM 中設定對電腦或應用程式的保護時，您選擇要備份至短期儲存的 DPM 本機磁碟，並備份至 Azure (線上保護)。 此外，您也指定了何時應執行備份至本機 DPM/MABS 儲存體的作業，以及何時應執行 Azure 的線上備份。
3. 受保護的工作負載磁碟會根據您指定的排程備份至本機 DPM 磁碟，並備份至 Azure。
4. 保存庫的線上備份由執行於 DPM/MABS 伺服器上的 MARS 代理程式所處理。

![備份受 DPM 或 MABS 保護的機器/工作負載](./media/backup-architecture/architecture-dpm-mabs.png)







## <a name="azure-vm-storage"></a>Azure VM 儲存體

- Azure VM 會使用磁碟來儲存其作業系統、應用程式和資料。
- Azure VM 至少有兩個磁碟。 一個用於作業系統，一個是暫存磁碟。 VM 也可能有應用程式資料的資料磁碟。 磁碟會儲存為 VHD。
- 在 Azure 的標準或進階儲存體帳戶中，VHD 會儲存為分頁 Blob。
    - 標準儲存體：為執行的工作負載不受延遲影響的 VM 提供可靠、低成本的磁碟支援。 標準儲存體可以使用標準 SSD 磁碟或標準 HDD 磁碟。
    - 進階儲存體：高效能磁碟支援。 使用進階 SSD 磁碟。
- 磁碟有不同的效能層級：
    - 標準 HDD 磁碟：採用 HDD，可用於符合成本效益的儲存。
    - 標準 SSD 磁碟：結合了進階 SSD 磁碟與標準 HDD 磁碟的元素，可提供比 HDD 更為穩定的效能和可靠性，但仍符合成本效益。
    - 進階 SSD 磁碟：採用 SSD，可為執行的工作負載需要大量 I/O 的 VM 提供高效能、低延遲的優勢。
- 磁碟可以是受控或非受控的：
    - 非受控磁碟：VM 所使用的傳統磁碟類型。 對於這些磁碟，您可以建立自己的儲存體帳戶，並在建立磁碟時指定該帳戶。 您必須了解如何將儲存體資源充分運用在 VM 上。
    - 受控磁碟：Azure 會為您建立及管理儲存體帳戶。 您只需指定磁碟大小和效能層級，Azure 會為您建立和管理磁碟。 Azure 會在您新增磁碟和調整 VM 時處理儲存體的相關事宜。

閱讀更多資訊：

- 深入了解適用於 [Windows](../virtual-machines/windows/about-disks-and-vhds.md) 和 [Linux](../virtual-machines/linux/about-disks-and-vhds.md) VM 的磁碟儲存體。
- 了解[標準](../virtual-machines/windows/standard-storage.md)和[進階](../virtual-machines/windows/premium-storage.md)儲存體。


### <a name="backing-up-and-restoring-azure-vms-with-premium-storage"></a>備份和還原使用進階儲存體的 Azure VM 

您可以使用 Azure 備份來備份使用進階儲存體的 Azure VM：

- 在備份使用進階儲存體的 VM 時，備份服務會在儲存體帳戶中建立名為 "AzureBackup-" 的暫存位置。 預備位置的大小等於復原點快照集的大小。
- 確定進階儲存體帳戶有足夠的可用空間可容納暫存位置。 [深入了解](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)。 請勿修改暫存位置。
- 備份作業完成後，就會刪除暫存位置。
- 用於暫存位置的儲存體，價格會與[進階儲存體價格](../virtual-machines/windows/premium-storage.md#pricing-and-billing)一致。

還原使用進階儲存體的 Azure VM 時，您可以將其還原為進階或標準儲存體。 一般會還原為進階儲存體，但如果您需要 VM 中的部分檔案，還原為標準儲存體可能較符合成本效益。

### <a name="backing-up-and-restoring-managed-disks"></a>備份和還原受控磁碟

您可以備份使用受控磁碟的 Azure VM。
- 備份使用受控磁碟的 VM 時，所使用相同的方式與備份任何其他 Azure VM 時相同。 您可以直接從虛擬機器設定備份 VM，或是為復原服務保存庫中的 VM 啟用備份。
- 您可以透過以受控磁碟為基礎的 RestorePoint 集合來備份受控磁碟上的 VM。
- Azure 備份也支援備份使用 Azure 磁碟加密 (ADE) 進行加密的受控磁碟 VM。

在還原使用受控磁碟的 VM 時，您可以還原為使用受控磁碟的完整 VM，或是還原至儲存體帳戶。
- 在還原的過程中，Azure 會處理受控磁碟，而您可以使用儲存體帳戶選項來管理在還原期間建立的儲存體帳戶。
- 如果要還原加密的受控 VM，您應先在金鑰保存庫中結束 VM 金鑰和秘密，再開始進行還原程序。




## <a name="next-steps"></a>後續步驟

- [檢閱](backup-support-matrix.md)支援矩陣，以了解備份案例的支援功能和限制。
- 位其中一個案例設定備份：
    - [備份 Azure VM](backup-azure-arm-vms-prepare.md)
    - [直接備份 Windows 機器](tutorial-backup-windows-server-to-azure.md)，而不使用備份伺服器。
    - [設定 MABS](backup-azure-microsoft-azure-backup.md) 以備份至 Azure，然後將工作負載備份至 MABS。
    - [設定 DPM](backup-azure-dpm-introduction.md) 以備份至 Azure，然後將工作負載備份至 DPM。


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

