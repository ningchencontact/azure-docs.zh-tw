---
title: 關於使用 Azure 備份在 Azure VM 上備份 SQL Server 資料庫的常見問題
description: 尋找有關使用 Azure 備份的 Azure Vm 上 SQL Server 資料庫的備份常見問題的解答。
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vijayts
ms.openlocfilehash: b5e6b1b07f986228eef66e2e92fb9ac3caef32fa
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704829"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>在 Azure VM 備份執行的 SQL Server 資料庫的相關常見問題集

這篇文章回答有關 SQL Server 資料庫備份 Azure 虛擬機器 (Vm) 上的執行，並且使用[Azure 備份](backup-overview.md)服務。

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>是否可用於 Azure 備份 IaaS VM，以及 SQL Server 在同一部電腦上？
是，您可以在相同的 VM 上有 VM 備份和 SQL 備份。 在此情況下，我們在內部觸發程序不會截斷記錄檔的 VM 上的只複製完整備份。


## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>重試或自動修復備份解決方案？

在某些情況下，Azure 備份服務會觸發修復備份。 自動修復可能發生任何下面所述的六個條件：

  - 如果記錄或差異備份失敗，因為 LSN 驗證錯誤下, 一步 的記錄或差異備份會改為轉換成完整的備份。
  - 如果沒有完整備份發生在記錄或差異備份之前，該記錄或差異備份會改為轉換成完整的備份。
  - 如果超過 15 天的最新完整備份的時間點下, 一步 的記錄或差異備份會改為轉換成完整的備份。
  - 在升級完成後啟動的延伸模組，因為延伸模組會取消所有備份工作會重新觸發。
  - 如果您選擇在還原期間會覆寫資料庫下, 一步 的記錄檔/差異備份會失敗，並完整備份，就會改為觸發。
  - 在需要重設記錄鏈結，因為在資料庫復原模式中變更的完整備份的位置的情況下，完整取得自動觸發下一步的排程。

自動修復功能啟用預設的情況下，所有的使用者如果您選擇退出它，但是再執行下面：

  * SQL Server 執行個體中*C:\Program Files\Azure 工作負載 Backup\bin*資料夾中，建立或編輯**ExtensionSettingsOverrides.json**檔案。
  * 在  **ExtensionSettingsOverrides.json**，將 *{"EnableAutoHealer": false}* 。
  * 儲存變更並關閉該檔案。
  * 在 SQL Server 執行個體中，開啟**任務管理**，然後重新啟動**AzureWLBackupCoordinatorSvc**服務。  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>我可以控制在 SQL server 上，執行多少並行備份嗎？

是的。 您可以限制備份原則的執行速率，以儘量降低對 SQL Server 執行個體的影響。 變更設定：
1. SQL Server 執行個體中*C:\Program Files\Azure 工作負載 Backup\bin*資料夾中，建立*ExtensionSettingsOverrides.json*檔案。
2. 在  *ExtensionSettingsOverrides.json*檔案中，變更**DefaultBackupTasksThreshold**設為較低的值 (例如，5)。 <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. 儲存變更並關閉該檔案。
4. 在 SQL Server 執行個體上，開啟 [工作管理員]  。 重新啟動 **AzureWLBackupCoordinatorSvc** 服務。<br/> <br/>
 雖然備份應用程式會耗用大量資源，SQL Server，此方法可協助[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017)是泛型的方法來指定所需的 CPU、 實體 IO 和記憶體可以連入的應用程式要求的限制使用。

> [!NOTE]
> 在 UX，您仍然可以繼續並在任何指定時間排程的備份，不過他們會說，5，上述範例所示的滑動視窗中處理。

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>是否可以從次要複本執行完整備份？
根據 SQL 限制，您可以執行複製僅完整備份在次要複本上不過，不允許完整備份。

## <a name="can-i-protect-availability-groups-on-premises"></a>我可以保護可用性群組內部部署嗎？
資料分割 Azure 備份可保護在 Azure 中執行的 SQL Server 資料庫。 如果 Azure 與內部部署電腦之間分散在可用性群組 (AG)，只有當主要複本在 Azure 中執行時，才可保護 AG。 此外，Azure 備份可保護與復原服務保存庫相同的 Azure 區域中執行的節點。

## <a name="can-i-protect-availability-groups-across-regions"></a>可以保護可用性群組跨區域嗎？
Azure 備份復原服務保存庫可以偵測並保護與保存庫位於相同區域中的所有節點。 如果您 SQL Server Always On 可用性群組跨越多個 Azure 區域，設定具有主要節點的區域的備份。 「Azure 備份」可以依據您的備份喜好設定，偵測並保護可用性群組中的所有資料庫。 備份喜好設定不符合時，備份失敗，而且您看見失敗警示。

## <a name="do-successful-backup-jobs-create-alerts"></a>成功的備份作業是否會建立警示？
資料分割 成功的備份作業不會產生警示。 只有失敗的備份作業會傳送警示。 入口網站警示的詳細的行為會記載[此處](backup-azure-monitoring-built-in-monitor.md)。 不過，如果您想要執行具有警示即使針對成功的作業，您可以使用[使用 Azure 監視器監視](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>我是否可以看到備份作業 功能表中的排程備份工作？
**備份作業**功能表只會顯示 ad 臨機操作備份工作。 用於排程工作[使用 Azure 監視器監視](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="are-future-databases-automatically-added-for-backup"></a>未來的資料庫會自動加入以進行備份嗎？
是，您可以達到這項功能 [自動保護](backup-sql-server-database-azure-vms.md#enable-auto-protection)。  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>如果我從 autoprotected 執行個體刪除資料庫，備份會發生什麼事？
如果從 autoprotected 執行個體卸除資料庫，還是嘗試資料庫備份。 這表示已刪除的資料庫會開始顯示在為狀況不良**備份項目**和仍會受到保護。

若要停止保護此資料庫的正確方式是執行 **停止備份**具有**刪除資料**對此資料庫。  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>如果我執行停止 autoprotected 資料庫的備份作業會有什麼行為？
如果您這樣做**停止備份並保留資料**，就會進行任何未來備份現有的復原點會維持不變。 資料庫仍然會被視為為受保護，並顯示在下面的**備份項目**。

如果您這樣做**停止備份並刪除資料**就會進行任何未來的備份，也會刪除現有的復原點。 資料庫會被視為未受保護，並且顯示在設定的備份中的執行個體。 不過，不同於其他向上保護的資料庫，可以手動將它選取，或者，可以取得 autoprotected，此資料庫呈現灰色，而且無法選取。 重新保護此資料庫的唯一方法是停用自動保護執行個體上。 您現在可以選取此資料庫和在其上設定保護或再次重新啟用自動保護執行個體上。

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>如果已受到保護之後，我就會變更資料庫的名稱，會有什麼行為？
重新命名的資料庫會被視為新的資料庫。 因此，服務會將這種情況下，找不到資料庫，並與失敗的備份。

您可以選取資料庫，它現在已重新命名，並在其上設定保護。 如果執行個體上啟用自動保護時，重新命名的資料庫將會自動偵測及保護中。

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>為什麼看不到 autoprotected 執行個體加入的資料庫？
資料庫，您[autoprotected 執行個體新增](backup-sql-server-database-azure-vms.md#enable-auto-protection)可能會立即出現在受保護的項目底下。 這是因為探索通常每隔 8 小時才會執行一次。 不過，您可以探索並立即保護新的資料庫，如果您選取手動執行探索**復原的資料庫使用**，如下圖所示。

  ![手動探索新加入的資料庫](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>後續步驟

了解如何[備份 SQL Server 資料庫](backup-azure-sql-database.md)Azure VM 上執行。
