---
title: 管理和監視 Azure 備份備份 Azure VM 上的 SQL Server 資料庫 |Microsoft Docs
description: 本文說明如何管理和監視 Azure VM 執行的 SQL Server 資料庫。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: f5ec5a64f1de39cd0d196242fb1a93669dbab15d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64681852"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>管理和監視備份的 SQL Server 資料庫

這篇文章會說明一般工作的管理及監視 SQL Server 資料庫執行的 Azure 虛擬機器 (VM) 上，而且都是，會備份到 Azure 備份復原服務保存庫[Azure 備份](backup-overview.md)服務。 您將了解如何監視作業和警示、 停止和繼續資料庫的保護、 執行備份作業，並取消註冊從備份的 VM。

如果您尚未設定備份的 SQL Server 資料庫，請參閱[備份 Azure Vm 上的 SQL Server 資料庫](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>監視手動備份的作業，在入口網站

Azure 備份會顯示在所有手動觸發的作業**備份作業**入口網站。 您看到此入口網站包含資料庫探索和註冊，和備份和還原作業的作業。

![備份作業入口網站](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> **備份作業**入口網站不會顯示已排程的備份作業。 請使用 SQL Server Management Studio 來監視已排程的備份作業，如下一節中所述。
>

如需監視案例的詳細資訊，請移至[在 Azure 入口網站中監視](backup-azure-monitoring-built-in-monitor.md)並[監視使用 Azure 監視器](backup-azure-monitoring-use-azuremonitor.md)。  


## <a name="view-backup-alerts"></a>檢視備份警示

記錄備份會每隔 15 分鐘出現一次，因此監視備份作業可能會很繁瑣。 Azure 備份可以減輕監視來傳送電子郵件警示。 電子郵件警示︰

- 觸發所有的備份失敗。
- 合併資料庫層級的錯誤程式碼。
- 只傳送資料庫的第一次的備份失敗。

若要監視資料庫備份的警示：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在保存庫儀表板上，選取 [警示與事件]。

   ![選取 [警示和事件]](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. 在 [警示和事件] 中，選取 [備份警示]。

   ![選取 [備份警示]](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>停止保護 SQL Server 資料庫

您可以透過數種方式停止備份 SQL Server 資料庫：

* 停止所有未來的備份作業並刪除所有復原點。
* 停止所有未來的備份作業，並保留復原點不變。

如果您選擇保留復原點，請記住這些詳細資料：

* 所有復原點永遠會都維持不變，所有的剪除應該會停止在停止保護保留資料。
* 您將支付受保護的執行個體和已使用的儲存體。 如需詳細資訊，請參閱 < [Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)。
* 如果您刪除資料來源而不需停止備份時，新的備份將會失敗。

若要停止保護資料庫：

1. 在保存庫儀表板中，選取**備份項目**。

2. 底下**備份管理類型**，選取**在 Azure VM 中的 SQL**。

    ![選取 Azure VM 中的 SQL](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. 選取您要停止保護的資料庫。

    ![選取要停止保護的資料庫](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. 在資料庫功能表上，選取 [停止備份]。

    ![選取 [停止備份]](./media/backup-azure-sql-database/stop-db-button.png)


5. 在 [**停止備份**] 功能表中，選取是否要保留或刪除資料。 如果您想，提供原因和註解。

    ![保留或刪除 [停止備份] 功能表上的資料](./media/backup-azure-sql-database/stop-backup-button.png)

6. 選取 **停止備份**。


## <a name="resume-protection-for-a-sql-database"></a>繼續保護 SQL 資料庫

當您停止保護 SQL database 中，如果您選取**保留備份資料**選項時，您可以稍後繼續保護。 如果您不會保留備份資料，您無法繼續保護。

若要繼續保護 SQL database:

1. 開啟 備份項目，然後選取**繼續備份**。

    ![選取 [繼續備份] 以繼續保護資料庫](./media/backup-azure-sql-database/resume-backup-button.png)

2. 在 [備份原則] 功能表上選取原則，然後選取 [儲存]。

## <a name="run-an-on-demand-backup"></a>執行隨選備份

您可以執行不同類型的隨選備份：

* 完整備份
* 僅複製完整備份
* 差異備份
* 記錄備份

當您需要指定只複製完整備份的保留期限時，其他備份類型的保留範圍會自動設定為 30 天，從目前的時間。 <br/>
如需詳細資訊，請參閱 < [SQL Server 備份類型](backup-architecture.md#sql-server-backup-types)。

## <a name="unregister-a-sql-server-instance"></a>將 SQL Server 執行個體取消註冊

停用保護之後但在您刪除保存庫之前，請取消註冊 SQL Server 執行個體：

1. 在保存庫儀表板，底下**管理**，選取**備份基礎結構**。  

   ![選取 [備份基礎結構]](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. 在 [管理伺服器] 底下，選取 [受保護的伺服器]。

   ![選取 [受保護的伺服器]](./media/backup-azure-sql-database/protected-servers.png)

3. 在 [受保護的伺服器] 中，選取要取消註冊的伺服器。 若要刪除保存庫，您必須將所有伺服器取消註冊。

4. 以滑鼠右鍵按一下 受保護的伺服器，然後選取**取消註冊**。

   ![選取 [刪除]](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>重新註冊 SQL Server VM 上的延伸模組

有時候，在 VM 上的工作負載延伸模組可能會受到影響的資料表或另一個原因。 在此情況下，在 VM 上觸發的所有作業就會都開始失敗。 接著，您可能需要重新註冊 VM 上的延伸模組。 **重新註冊**作業重新安裝針對作業持續在 VM 上的工作負載備份延伸模組。  <br>

建議您小心; 使用此選項具有已狀況良好的擴充功能的 VM 上觸發，這項作業會取得重新啟動的延伸模組。 這可能會導致失敗的所有進行中工作。 請檢查一或多個[徵狀](backup-sql-server-azure-troubleshoot.md#symptoms)觸發重新註冊作業之前。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 <<c0> [ 疑難排解 SQL Server 資料庫上的備份](backup-sql-server-azure-troubleshoot.md)。
