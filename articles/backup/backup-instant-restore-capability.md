---
title: Azure 立即還原功能
description: Azure 立即還原功能和 VM 備份堆疊、Azure Resource Manager 部署模型的常見問題集
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: sogup
ms.openlocfilehash: 3aceffa719ef8938aa049f126231f8628822566b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794772"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>透過 Azure 備份的立即還原功能取得改良的備份和還原效能

> [!NOTE]
> 我們根據使用者的意見反應，正在將 **VM 備份堆疊 V2** 重新命名為**立即還原**，以減少與 Azure Stack 功能的混淆。<br/><br/> 所有 Azure 備份使用者現在已升級至**立即還原**。

「立即還原」的新模型提供下列增強功能：

* 能夠使用在備份作業中所建立的快照集 (可用於復原)，而不需等到資料轉送到保存庫完成。 如此可減少觸發還原之前，將快照集複製到保存庫的等候時間。
* 藉由本機預設保留 2 天的快照集，可減少備份和還原時間。 此預設快照集保留值是設定為 1 到 5 天之間的任何值。
* 支援最多 4 TB 磁碟大小。 Azure 備份不支援等量的磁碟。 Azure 備份，建議您不調整大小的磁碟。
* 支援標準的 SSD 磁碟，以及標準的 HDD 磁碟和進階 SSD 磁碟。
*   還原時，能夠使用非受控 VM 的原始儲存體帳戶 (依據磁碟)。 即使 VM 的磁碟分散於多個儲存體帳戶，仍然具有此功能。 它能夠針對各種不同的 VM 設定，加快還原作業的速度。


## <a name="whats-new-in-this-feature"></a>這項功能有什麼新增功能？

目前的備份作業包含兩個階段：

1.  建立 VM 快照集。
2.  將 VM 快照集傳輸到 Azure 復原服務保存庫。

復原點只會在第 1 和第 2 階段完成之後才建立。 在此升級中，完成快照集時就會建立復原點，而且快照集類型的此復原點可用來執行還原 (使用相同的還原流程)。 您可以使用 「 快照集 」 作為復原點類型，來識別此復原點，在 Azure 入口網站中的，快照集傳輸至保存庫之後，復原點類型變更為 「 快照集與保存庫 」。

![VM 備份堆疊 Resource Manager 部署模型的備份作業 -- 儲存體與保存庫](./media/backup-azure-vms/instant-rp-flow.png)

默认情况下，快照将保留两天。 這項功能可讓還原時間減少還原作業，從那里這些快照集。 它可減少需要轉換，並將資料複製回從保存庫的時間。

## <a name="feature-considerations"></a>功能考量

* 快照集會儲存在磁碟上，以便建立復原點及加快還原作業的速度。 因此，您將看見儲存體成本會隨著在這段期間內取得的快照集而增減。
* 增量快照會儲存為分頁 Blob。 使用非受控磁碟的所有客戶，需對儲存在其本機儲存體帳戶中的快照集付費。 因為由受控 VM 備份所使用的還原點集合會於基礎儲存體層級使用 Blob 快照集，因此針對受控磁碟，您將會看見對應到 Blob 快照集定價的成本，而且它們會持續累加。
* 進階儲存體帳戶，如立即復原點都算是的 10 TB 限制所建立的快照集已配置空間。
* 您可以根據還原需求來設定快照集保留期。 您可以根據需求，在備份原則刀鋒視窗中，將快照集保留期最少設為一天，如下所述。 如果您沒有經常執行還原，此方式可以協助您省下快照集保留期的成本。
* 這是一個方向升級，升級後立即還原，您不能移回。

>[!NOTE]
>如果升級此立即還原，所有客戶的快照集保留持續時間 (**同時包含新的和現有的**) 會設定為預設值兩天。 不過，您可以根據每個需求，將持續時間設定為介於 1 至 5 天的任何值。

## <a name="cost-impact"></a>成本影響

增量快照集會儲存在 VM 的儲存體帳戶中，以便用於立即復原。 增量快照集表示快照集所佔用的空間等於建立快照集後寫入的頁面所佔用的空間。 計費方式仍為快照集所佔用的每 GB 使用空間，而每 GB 的價格與[定價頁面](https://azure.microsoft.com/pricing/details/managed-disks/)中所提的價格相同。

>[!NOTE]
> 快照集保留期會固定為 5 天的每週的原則。

## <a name="configure-snapshot-retention"></a>設定快照集保留期

### <a name="using-azure-portal"></a>使用 Azure 入口網站

在 Azure 入口網站中，您可以看到中加入的欄位**VM 的備份原則**下方的刀鋒視窗**立即還原**一節。 您可以針對與特定備份原則相關聯的所有 VM，從 **VM 備份原則**刀鋒視窗變更快照集保留持續時間。

![立即還原功能](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>使用 PowerShell

>[!NOTE]
> 您可以從 Az PowerShell 1.6.0 版及更新版本，更新在原則中使用 PowerShell 的 「 立即還原快照集保留期限

```powershell
PS C:\> $bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```
預設的快照集保留期限，每個原則設定為 2 天。 使用者可以變更值，以最少 1，最多 5 天。 針對每週的原則，快照集保留期會固定為 5 天。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="what-are-the-cost-implications-of-instant-restore"></a>「立即還原」的成本影響為何？
快照集會儲存在磁碟上，以便加快建立復原點和還原作業的速度。 因此，您將在 VM 備份原則中看見對應至所選快照集保留的儲存體成本。

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>在進階儲存體帳戶中，立即復原點建立的快照集佔用 10 TB 的快照集限制？
是，進階儲存體帳戶建立立即復原點的快照集佔用的快照集已配置的空間的 10 TB。

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>快照集保留在五天期間內的運作方式為何？
每天都會取得新的快照集，所以有五個個別的累加快照集。 快照集的大小取決於資料流失，也就是在大部分情況下大約 2-7%。

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>立即還原快照集是增量快照集，還是完整的快照集？
立即還原功能所取得的快照集是增量快照集。

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>如何計算立即還原功能所造成的大約成本增加？
這取決於 VM 的變換。 在穩定的狀態下，您可以假設成本增加 = 快照集保留期間，每部 VM 的每日變換，每 GB 的儲存體成本。

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>如果還原點的復原類型是「快照集與保存庫」，而我執行還原作業，則會使用哪個復原類型？
如果復原類型是「快照集與保存庫」，則會從本機快照集自動完成還原，相較於從保存庫進行還原，這麼做更快速。

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>如果我選取的還原點 (第 2 層) 保留期間小於快照集 (第 1 層) 保留期間，會發生什麼事？
除非刪除快照集 (第 1 層)，否則新模型不允許刪除還原點 (第 2 層)。 建議您排程大於快照集保留期間的還原點 (第 2 層) 保留期間。

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>為何在備份原則中設定的保留期間之後，我的快照集仍然存在？
如果復原點具有快照集而且是最新的可用 RP，則會保留到有下一個成功備份的時候。 這是依據現今設計的 GC 原則，該原則要求一律至少有一個最新的 RP，以防所有備份由於 VM 問題進而失敗。 在正常情況下，RP 會在到期後的最多 24 小時內清除。
