---
title: Azure 立即還原功能
description: Azure 立即還原功能和 VM 備份堆疊、Azure Resource Manager 部署模型的常見問題集
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: trinadhk
ms.openlocfilehash: a0cbb8650a818c3ae6c839d69bbc9b94f8d080ca
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351391"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>透過 Azure 備份的立即還原功能取得改良的備份和還原效能

> [!NOTE]
> 根據接收自使用者的意見反應，**VM 備份堆疊 V2** 容易與 Azure stack 混淆，所以我們將它重新命名為 [立即還原]，因而提供升級且更好的體驗。

「立即還原」的新模型提供下列增強功能：

* 能夠使用在備份作業中所建立的快照集 (可用於復原)，而不需等到資料轉送到保存庫完成。 如此可減少觸發還原之前，將快照集複製到保存庫的等候時間。
* 將快照集於磁碟上保存七天，以減少備份與還原時間。
* 支援最多 4 TB 磁碟大小。
* 支援標準 SSD 磁碟。
* 還原時，能夠使用非受控 VM 的原始儲存體帳戶 (依據磁碟)。 即使 VM 的磁碟分散於多個儲存體帳戶，仍然具有此功能。 它能夠針對各種不同的 VM 設定，加快還原作業的速度。


## <a name="whats-new-in-this-feature"></a>這項功能有什麼新增功能？

目前的備份作業包含兩個階段：

1.  建立 VM 快照集。
2.  將 VM 快照集傳輸到 Azure 復原服務保存庫。

復原點只會在第 1 和第 2 階段完成之後才建立。 在此升級中，完成快照集時就會建立復原點，而且快照集類型的此復原點可用來執行還原 (使用相同的還原流程)。 您可以使用「快照集」作為復原點類型，以在 Azure 入口網站中識別此復原點，而在快照集傳輸至保存庫之後，復原點類型就會變更為「快照集和保存庫」。

![VM 備份堆疊 Resource Manager 部署模型的備份作業 -- 儲存體與保存庫](./media/backup-azure-vms/instant-rp-flow.png)

快照集會保留七天。 這項功能允許從這些快照集進行還原作業，因而縮減還原時間。 在非受控磁碟案例中，它可減少轉換資料並將資料從保存庫複製回到使用者儲存體帳戶所需的時間，然而對於受控磁碟使用者，則會從備份資料建立受控磁碟。

## <a name="feature-considerations"></a>功能考量

* 快照集會儲存在磁碟上，以便建立復原點及加快還原作業的速度。 因此，您將看見儲存體成本會隨著在這段期間內取得的快照集而增減。
* 增量快照會儲存為分頁 Blob。 使用非受控磁碟的所有客戶，需對儲存在其本機儲存體帳戶中的快照集付費。 因為由受控 VM 備份所使用的還原點集合會於基礎儲存體層級使用 Blob 快照集，因此針對受控磁碟，您將會看見對應到 Blob 快照集定價的成本，而且它們會持續累加。
* 對於進階儲存體帳戶，針對立即復原點取得的快照集會計入配置空間的 10 TB 限制。

## <a name="cost-impact"></a>成本影響

增量快照集會儲存在 VM 的儲存體帳戶中，以便用於立即復原。 增量快照集表示快照集所佔用的空間等於建立快照集後寫入的頁面所佔用的空間。 計費方式仍為快照集所佔用的每 GB 使用空間，而每 GB 的價格與[定價頁面](https://azure.microsoft.com/pricing/details/managed-disks/)中所提的價格相同。


## <a name="upgrading-to-instant-restore"></a>升級至立即還原

如果您使用 Azure 入口網站，將會在保存庫儀表板上看到通知。 此通知乃是關於大型磁碟支援，以及備份和還原速度的改進。

![VM 備份堆疊 Resource Manager 部署模型的備份作業 -- 支援通知](./media/backup-azure-vms/instant-rp-banner.png)

若要開啟畫面以升級至「立即還原」，請選取橫幅。

![VM 備份堆疊 Resource Manager 部署模型的備份作業 -- 升級](./media/backup-azure-vms/instant-rp.png)

或者，您可以前往保存庫的 [屬性] 頁面，以取得 **VM 備份堆疊** 下的 [升級] 選項。

![VM 備份堆疊的備份作業 - 屬性頁面](./media/backup-azure-vms/instant-restore-capability-properties.png)


## <a name="upgrade-to-instant-restore-using-powershell"></a>使用 PowerShell 升級至立即還原

如果您希望自行升級至「立即還原」，請從提高權限的 PowerShell 終端機執行下列 Cmdlet：

1.  登入您的 Azure 帳戶：

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  選取您要註冊的訂用帳戶：

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  註冊此訂用帳戶：

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="upgrade-to-instant-restore-using-cli"></a>使用 CLI 升級至立即還原

從殼層執行下列命令：

1.  登入您的 Azure 帳戶：

    ```
    az login
    ```

2.  選取您要註冊的訂用帳戶：

    ```
    az account set --subscription "Subscription Name"
    ```

3.  註冊此訂用帳戶：

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-successful"></a>確認升級成功

### <a name="powershell"></a>PowerShell
從提高權限的 PowerShell 終端機執行下列 Cmdlet︰

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>CLI
從殼層執行下列命令：

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

如果顯示 "Registered"，表示您的訂用帳戶已升級至「立即還原」。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="what-are-the-cost-implications-of-instant-restore"></a>「立即還原」的成本影響為何？
快照集會儲存在磁碟上，以便加快建立復原點和還原作業的速度。 因此，您將在 VM 備份原則中看見對應至所選快照集保留的儲存體成本。

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>在進階儲存體帳戶中，針對立即復原點取得的快照集是否會佔用 10 TB 的快照集限制？
是，對於進階儲存體帳戶，針對立即復原點取得的快照集會佔用 10 TB 的配置快照集空間。

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>快照集保留在五天期間內的運作方式為何？
每天都會取得新的快照集，所以有五個個別的累加快照集。 快照集的大小取決於資料變換，在大部分情況下約為 2%-7%。

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>立即還原快照集是增量快照集，還是完整的快照集？
立即還原功能所取得的快照集是增量快照集。

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>如何計算立即還原功能所造成的大約成本增加？
這取決於 VM 的變換。 在穩定的狀態下，您可以假設成本增加 = 快照集保留期限 * 每部 VM 的每日變換 * 每 GB 的儲存體成本。

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>如果還原點的復原類型是「快照集與保存庫」，而我執行還原作業，則會使用哪個復原類型？
如果復原類型是「快照集與保存庫」，則會從本機快照集自動完成還原，相較於從保存庫進行還原，這麼做更快速。

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>如果我選取的還原點 (第 2 層) 保留期間小於快照集 (第 1 層) 保留期間，會發生什麼事？
除非刪除快照集 (第 1 層)，否則新模型不允許刪除還原點 (第 2 層)。 我們目前針對快照集 (第 1 層) 刪除支援 7 天的保留期限，因此，不接受少於 7 天的還原點 (第 2 層) 保留期限。 建議您排程大於 7 天的還原點 (第 2 層) 保留期限。

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>為何在備份原則中設定的保留期間之後，我的快照集仍然存在？
如果復原點具有快照集而且是最新的可用 RP，則會保留到有下一個成功備份的時候。 這是依據現今設計的 GC 原則，該原則要求一律至少有一個最新的 RP，以防所有備份由於 VM 問題進而失敗。 在正常情況下，RP 會在到期後的最多 48 小時內清除。
