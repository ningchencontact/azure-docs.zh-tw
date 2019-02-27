---
title: 關於使用 Azure 備份在 Azure VM 上備份 SQL Server 資料庫的常見問題
description: 提供有關使用 Azure 備份在 Azure VM 上備份 SQL Server 資料庫之常見問題的解答。
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: a14406733ff60d53d4bf7792ff0c9a015c57d9b3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430922"
---
# <a name="faq-on-sql-server-running-on-azure-vm-backup"></a>關於在 Azure VM 備份上執行之 SQL Server 的常見問題集

這篇文章回答有關使用 [Azure 備份](backup-overview.md)服務備份在 Azure VM 上執行之 SQL Server 資料庫的常見問題。

> [!NOTE]
> 此功能目前為公開預覽狀態。



## <a name="can-i-throttle-the-backup-speed"></a>我可以調節備份速度嗎？

是。 您可以限制備份原則的執行速率，以儘量降低對 SQL Server 執行個體的影響。 變更設定：
1. 在 SQL Server 執行個體上的 *C:\Program Files\Azure Workload Backup\bin 資料夾*中，建立 **ExtensionSettingsOverrides.json** 檔案。
2. 在 **ExtensionSettingsOverrides.json** 檔案中，將 **DefaultBackupTasksThreshold** 設定變更為較低的值 (例如，5) <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. 儲存您的變更。 關閉檔案。
4. 在 SQL Server 執行個體上，開啟 [工作管理員]。 重新啟動 **AzureWLBackupCoordinatorSvc** 服務。

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>是否可以從次要複本執行完整備份？
沒有。 不支援此功能。

## <a name="do-successful-backup-jobs-create-alerts"></a>成功的備份作業是否會建立警示？

沒有。 成功的備份作業不會產生警示。 只有失敗的備份作業會傳送警示。

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>是否可以在 [作業] 功能表中看到已排程的備份作業？

沒有。 [備份作業] 功能表會顯示隨選工作的詳細資訊，但不會顯示已排程的備份作業。 如果已排程的備份作業失敗，則可以在失敗的作業警示中取得詳細資料。 若要監視所有已排程與臨機操作備份作業，請使用 [SQL Server Management Studio](manage-monitor-sql-database-backup.md)。

## <a name="are-future-databases-automatically-added-for-backup"></a>未來的資料庫會自動加入以進行備份嗎？

沒有。 在為 SQL Server 執行個體設定保護時，如果您選取伺服器層級選項，系統會新增所有資料庫。 如果在設定保護之後將資料庫新增至 SQL Server 執行個體，則必須手動新增新的資料庫來加以保護。 資料庫不會自動包含在已設定的保護中。

##  <a name="how-do-i-restart-protection-after-changing-recovery-type"></a>變更復原類型後如何重新啟動保護？

觸發完整備份。 記錄備份會如預期地開始。

## <a name="can-i-protect-availability-groups-on-premises"></a>我可以在內部部署保護可用性群組嗎？

沒有。 Azure 備份會保護在 Azure 中執行的 SQL Server。 如果可用性群組 (AG) 分散在 Azure 與內部部署機器之間，則只有當主要複本是在 Azure 中執行時，才可保護 AG。 此外，Azure 備份只會保護與復原服務保存庫位於相同 Azure 區域中的執行中節點。

## <a name="can-i-protect-availability-groups-across-regions"></a>我可以跨區域保護可用性群組嗎？

「Azure 備份復原服務保存庫」可以偵測並保護與「復原服務保存庫」位於相同區域中的所有節點。 如果您的「SQL Always On 可用性群組」跨多個 Azure 區域，您就必須從具有主要節點的區域設定備份。 「Azure 備份」將能夠依據備份喜好設定，偵測並保護可用性群組中的所有資料庫。 如果不符合備份喜好設定，備份就會失敗，而您將會收到失敗警示。

## <a name="can-i-exclude-databases-with-auto-protection-enabled"></a>我可以排除啟用自動保護的資料庫嗎？

否，[自動保護](backup-azure-sql-database.md#enable-auto-protection)會套用到整個執行個體上。 您無法使用自動保護，選擇性地保護執行個體的資料庫。

## <a name="can-i-have-different-policies-in-an-auto-protected-instance"></a>我可以在已受自動保護的執行個體中使用不同的原則嗎？

如果您在執行個體中已經有一些受保護的資料庫，它們將繼續受到其各自的原則所保護，即使您已**開啟**[自動保護](backup-azure-sql-database.md#enable-auto-protection)。 不過，所有未受保護的資料庫以及您未來新增的資料庫將只會有單一原則，而您會在選取資料庫之後，於 [設定備份] 下方定義此原則。 事實上，不同於其他受保護的資料庫，您甚至無法在已受自動保護的執行個體下方變更資料庫的原則。
如果您想要這樣做，唯一的方式是暫時在執行個體上停用自動保護，然後變更該資料庫的原則。 您現在可以針對此執行個體重新啟用自動保護。

## <a name="if-i-delete-a-database-from-auto-protection-will-backups-stop"></a>如果我從自動保護中刪除資料庫，備份會停止嗎？

否，如果從已受自動保護的執行個體中卸除資料庫，仍會在該資料庫上嘗試進行備份。 這表示已刪除的資料庫會開始在 [備份項目] 下方顯示為狀況不良，且仍被視為受到保護。

停止保護此資料庫的唯一方式，是暫時在執行個體上停用 [自動保護](backup-azure-sql-database.md#enable-auto-protection)，然後在該資料庫的 [備份項目] 下方，選擇 [停止備份] 選項。 您現在可以針對此執行個體重新啟用自動保護。

##  <a name="why-cant-i-see-an-added-database-for-an-auto-protected-instance"></a>為什麼我看不到自動保護執行個體已加入的資料庫？

您可能不會立即在受保護的項目下，看到新增至[已受自動保護](backup-azure-sql-database.md#enable-auto-protection)之執行個體的資料庫受到保護。 這是因為探索通常每隔 8 小時才會執行一次。 不過，使用者可以使用 [復原資料庫] 選項來執行手動探索，以立即探索並保護新的資料庫，如下圖所示：

  ![檢視新增的資料庫](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>後續步驟

[了解如何](backup-azure-sql-database.md)備份執行於 Azure VM 的 SQL Server 資料庫。
