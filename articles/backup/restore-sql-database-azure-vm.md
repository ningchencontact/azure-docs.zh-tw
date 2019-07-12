---
title: 使用 Azure 備份來還原備份 Azure VM 上的 SQL Server 資料庫 |Microsoft Docs
description: 本文說明如何還原 Azure VM 上執行，而且，Azure backup 備份的 SQL Server 資料庫。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: raynew
ms.openlocfilehash: 856f45f448aa843e9dc04ec3b6a60841cfe33227
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704867"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>在 Azure VM 上還原 SQL Server 資料庫

這篇文章說明如何在 Azure 虛擬機器 (VM) 執行 SQL Server 資料庫還原所[Azure 備份](backup-overview.md)服務已備份至 Azure 備份復原服務保存庫。

本文將說明如何還原 SQL Server 資料庫。 如需詳細資訊，請參閱 <<c0> [ 備份 Azure Vm 上的 SQL Server 資料庫](backup-azure-sql-database.md)。

## <a name="restore-to-a-time-or-a-recovery-point"></a>還原至時間或復原點

Azure 備份可以還原，如下所示執行 Azure Vm 的 SQL Server 資料庫：

- 使用交易記錄備份還原到特定的日期或時間 （秒）。 Azure 備份會自動決定適當的完整差異備份和還原所需的記錄備份鏈結會根據選取的時間。
- 將特定的完整或差異備份還原至特定復原點還原。


## <a name="prerequisites"></a>先決條件

您將資料庫還原之前，請注意下列各項：

- 您可以將資料庫還原至相同 Azure 區域中的 SQL Server 執行個體。
- 目的地伺服器必須註冊到和來源相同的保存庫。
- 若要將 TDE 加密資料庫還原到另一個 SQL Server，您需要先[還原到目的地伺服器的憑證](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)。
- 您將 「 主要 」 的資料庫還原之前，在單一使用者模式中啟動 SQL Server 執行個體使用啟動選項 **-m AzureWorkloadBackup**。
    - 值 **-m**是用戶端的名稱。
    - 只有指定的用戶端名稱可開啟連接。
- 所有系統資料庫 (模型、 master、 msdb)，請都停止 SQL Server Agent 服務，才能觸發還原。
- 關閉任何可能會嘗試採取任何這些資料庫連接的應用程式。
- 如果您有多個執行個體的伺服器上，執行所有執行個體應該會增加，並執行否則伺服器不會出現在清單中的目的地伺服器，供您還原資料庫。

## <a name="restore-a-database"></a>還原資料庫

若要還原，您需要下列權限：

* **Backup Operator**在保存庫中您要在其中進行還原的權限。
* 來源 VM (已備份) 的**參與者 (寫入)** 存取權。
* 目標 VM 的**參與者 (寫入)** 存取權：
    - 如果您要還原至相同的 VM，這是來源 VM。
    - 如果您要還原到替代位置，這會是新的目標 VM。

以下列方式進行還原：
1. 開啟其中有已註冊 SQL Server VM 的保存庫。
2. 在保存庫儀表板中的 [使用量]  底下，選取 [備份項目]  。
3. 在 [備份項目]  中的 [備份管理類型]  底下，選取 **Azure VM 中的 SQL**。

    ![選取 Azure VM 中的 SQL](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 選取要還原的資料庫。

    ![選取要還原的資料庫](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 檢閱資料庫功能表。 其中有資料庫備份的資訊，包括：

    * 最早和最近的還原點。
    * 過去 24 小時，會在完整和大量記錄復原模式，且設定為使用交易記錄備份的資料庫記錄檔備份狀態。

6. 選取  **Restore DB**。

    ![選取 [還原 DB]](./media/backup-azure-sql-database/restore-db-button.png)

7. 在 **還原設定**，指定要還原資料的位置：
   - 替代位置  ：將資料庫還原到替代位置，並保留原始的來源資料庫。
   - **覆寫 DB**：將資料還原至與原始來源相同的 SQL Server 執行個體。 此選項會覆寫原始的資料庫。

     > [!Important]
     > 如果選取的資料庫屬於 AlwaysOn 可用性群組，SQL Server 不允許覆寫資料庫。 只有 [替代位置]  可用。
     >

     ![[還原設定] 功能表](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>還原至替代位置

1. 在**還原組態**功能表底下**還原到何處**，選取**替代位置**。
2. 選取您要在其中還原資料庫的目的地 SQL Server 名稱和執行個體。
3. 在 [還原的 DB 名稱]  方塊中，輸入目標資料庫的名稱。
4. 如果適用的話，請選取 [Overwrite if the DB with the same name already exists on selected SQL instance] \(若選取的 SQL 執行個體上已存在相同名稱的 DB 則覆寫\)  。
5. 選取 [確定]  。

    ![提供 [還原設定] 功能表的值](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. 在 **選取還原點**，選取是否要[還原至特定點的時間](#restore-to-a-specific-point-in-time)或[還原至特定復原點](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 時間點還原是僅適用於在完整和大量記錄復原模式的資料庫記錄備份。

### <a name="restore-and-overwrite"></a>還原並覆寫資料庫

1. 在**還原組態**功能表底下**還原位置**，選取**覆寫 DB** > **確定**。

    ![選取 [覆寫 DB]](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. 在 **選取還原點**，選取**記錄 （時間點）** 來[還原至特定點的時間](#restore-to-a-specific-point-in-time)。 或選取**完整與差異**還原至[特定復原點](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 時間點還原是僅適用於在完整和大量記錄復原模式的資料庫記錄備份。

### <a name="restore-to-a-specific-point-in-time"></a>還原至特定時間點

如果您已選取 [記錄 (時間點)]  作為還原類型，請執行下列動作：

1.  底下**還原日期/時間**，開啟行事曆。 在行事曆，有個復原點的日期會以粗體顯示，目前的日期會反白顯示。
1. 選取已復原點的日期。 您無法選取任何復原點的日期。

    ![開啟行事曆](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 選取日期之後，時間軸圖表會顯示連續範圍中可用的復原點。
1. 在時間軸圖形中指定的復原時間，或選取的時間。 然後選取 [確定]  。

    ![選取 還原時間](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. 在 **進階組態**功能表上，如果您想要為非作業狀態的資料庫保持在還原之後，啟用**Restore with NORECOVERY**。
1. 如果您要變更目的地伺服器上的還原位置，請輸入新的目標路徑。
1. 選取 [確定]  。

    ![進階設定功能表](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. 在 [還原]  功能表上，選取 [還原]  以啟動還原作業。
1. 還原中追蹤進度**通知**區域中，或選取追蹤**還原作業**功能表中的資料庫。

    ![還原作業進度](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>還原至特定還原點

如果您已選取 [完整和差異]  作為還原類型，請執行下列動作：

1. 從清單中選取復原點，然後選取 [確定]  以完成還原點程序。

    ![選擇完整復原點](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. 在 **進階組態**功能表上，如果您想要為非作業狀態的資料庫保持在還原之後，啟用**Restore with NORECOVERY**。
1. 如果您要變更目的地伺服器上的還原位置，請輸入新的目標路徑。
1. 選取 [確定]  。

    ![進階設定功能表](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. 在 [還原]  功能表上，選取 [還原]  以啟動還原作業。
1. 還原中追蹤進度**通知**區域中，或選取追蹤**還原作業**功能表中的資料庫。

    ![還原作業進度](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>使用大量的檔案還原資料庫

如果資料庫中檔案的字串總大小大於[特定的限制](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)，Azure 備份會儲存在不同的 pit 元件中資料庫檔案的清單，您無法在還原期間設定的目標還原路徑作業。 檔案將改為還原到 SQL 的預設路徑。

  ![使用大型檔案中還原資料庫](./media/backup-azure-sql-database/restore-large-files.jpg)


## <a name="next-steps"></a>後續步驟

[管理和監視](manage-monitor-sql-database-backup.md)所備份的 Azure 備份的 SQL Server 資料庫。
