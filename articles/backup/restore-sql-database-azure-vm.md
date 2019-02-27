---
title: 使用 Azure 備份在 Azure VM 上還原備份的 SQL Server 資料庫 | Microsoft Docs
description: 本文說明如何還原在 Azure VM 上執行且使用 Azure 備份進行備份的 SQL Server 資料庫
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 9b741f8562ae2e81d297357afd3b0e0e3976a248
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445435"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>在 Azure VM 上還原 SQL Server 資料庫 


本文將說明如何還原在 Azure VM 上執行，且已使用 [Azure 備份服務](backup-overview.md)備份到 Azure 備份復原服務保存庫的 SQL Server 資料庫。


> [!NOTE]
> 針對在 Azure VM 上執行的 SQl Server 資料庫使用 Azure 備份進行備份，目前處於公開預覽狀態。


本文將說明如何還原 SQL Server 資料庫。 如果您尚未針對資料庫設定備份，請遵循[此文章](backup-azure-sql-database.md)的指示進行



## <a name="about-restoring-databases"></a>關於還原資料庫

Azure 備份可還原在 Azure VM 上執行的 SQL Server 資料庫，如下所示：

- 使用交易記錄備份還原至特定日期或時間 (可到秒的部分)。 Azure 備份會根據選取的時間，自動決定還原所需的適當完整差異備份和記錄備份鏈結。
- 將特定的完整或差異備份還原至特定復原點，而不是特定時間。


### <a name="prerequisites"></a>必要條件

還原資料庫之前，請注意下列各項：

- 您可以將資料庫還原至相同 Azure 區域中的 SQL Server 執行個體。
- 目的地伺服器必須註冊到和來源相同的保存庫。
- 若要將 TDE 加密資料庫還原至另一部 SQL Server，您必須先[將憑證還原至目的地伺服器](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)。
- 在觸發 "master" 資料庫的還原作業之前，請使用啟動選項 **-m AzureWorkloadBackup**，以單一使用者模式啟動 SQL Server 執行個體。
    - **-m** 的值是用戶端名稱。
    - 只有此指定用戶端名稱能夠開啟連線。
- 針對所有系統資料庫 (模型、master、msdb)，請在觸發還原之前停止 SQL Agent 服務。
- 關閉可能會嘗試奪取這些資料庫連線的任何應用程式。

## <a name="restore-a-database"></a>還原資料庫

若要進行還原，您需要下列權限︰

* 保存庫中的**備份操作員**權限，以便在其中進行還原。
* 來源 VM (已備份) 的**參與者 (寫入)** 存取權。
* 目標 VM 的**參與者 (寫入)** 存取權：
    - 如果您要還原至相同的 VM，這會是來源 VM。
    - 如果您要還原至替代位置，這會是新的目標 VM。 

以下方式進行還原：
1. 開啟其中有已註冊 SQL Server VM 的保存庫。
2. 在保存庫儀表板中的 [使用量] 底下，選取 [備份項目]。

    ![開啟 [備份項目] 功能表](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png)上也提供本文中使用的原始碼。

3. 在 [備份項目] 中的 [備份管理類型] 底下，選取 **Azure VM 中的 SQL**。

    ![選取 Azure VM 中的 SQL](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 選取要還原的資料庫。

    ![選取要還原的資料庫](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 檢閱資料庫功能表。 其中有資料庫備份的資訊，包括： 

    * 最早和最近的還原點。
    * 過去 24 小時的記錄備份狀態 (如果設定為交易記錄備份，則適用於完整和大量記錄復原模式的資料庫)。

6. 按一下 [還原 DB]。 

    ![選取 [還原 DB]](./media/backup-azure-sql-database/restore-db-button.png)

7. 在 [還原設定] 中，指定還原資料的目的地位置：
    - 替代位置：將資料庫還原至替代位置，並保留原始來源資料庫。
    - **覆寫 DB**：將資料還原至與原始來源相同的 SQL Server 執行個體。 此選項的效果是要覆寫原始資料庫。

    > [!Important]
    > 如果選取的資料庫屬於 Always On 可用性群組，SQL Server 不允許覆寫資料庫。 只有 [替代位置] 可用。
    >

    ![[還原設定] 功能表](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>還原至替代位置

1. 在 [還原設定] 功能表中的 [還原目的地] 底下，選取 [替代位置]。
2. 選取您要在其中還原資料庫的目的地 SQL Server 名稱和執行個體。
3. 在 [還原的 DB 名稱] 方塊中，輸入目標資料庫的名稱。
4. 如果適用的話，請選取 [Overwrite if the DB with the same name already exists on selected SQL instance] \(若選取的 SQL 執行個體上已存在相同名稱的 DB 則覆寫\)。
5. 按一下 [確定]。

    ![提供 [還原設定] 功能表的值](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. 在 [選取還原點] 中，選取是否要[還原至特定時間點](#restore-to-a-specific-point-in-time)，或還原到[特定復原點](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 還原時間點僅適用於具有完整和大量記錄復原模式的資料庫記錄備份。 


### <a name="restore-and-overwrite"></a>還原並覆寫資料庫

1. 在 [還原設定] 功能表中的 [還原目的地] 底下，選取 [複寫 DB] > [確定]。

    ![選取 [覆寫 DB]](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. 在 [選取還原點] 中，選取 [記錄 (時間點)] 以[還原至特定時間點](#restore-to-a-specific-point-in-time)，或選取 [完整和差異]，以還原到[特定復原點](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 還原時間點僅適用於具有完整和大量記錄復原模式的資料庫記錄備份。 




    
### <a name="restore-to-a-specific-point-in-time"></a>還原至特定時間點

如果您已選取 [記錄 (時間點)] 作為還原類型，請執行下列動作：

1.  在 [還原日期/時間] 下，選取迷你行事曆。 在 [行事曆] 上，粗體日期表示具有復原點，且會醒目提示目前的日期。
2. 選取具有復原點的日期。 無法選取沒有復原點的日期。

    ![開啟行事曆](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

3. 選取日期之後，時間軸圖表會顯示連續範圍中可用的復原點。
4. 請使用時間軸圖表或選取時間，來指定復原的時間。 然後按一下 [確定] 。

    ![開啟行事曆](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

 
4. 在 [進階設定] 功能表上，如果您想要讓資料庫在還原之後保持不運作，請啟用 [使用 NORECOVERY 還原]。
5. 如果您要變更目的地伺服器上的還原位置，請輸入新的目標路徑。
6. 按一下 [確定]。

    ![進階設定功能表](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)


7. 在 [還原] 功能表上，選取 [還原] 以啟動還原作業。
8. 在 [通知] 區域中追蹤還原進度，或藉由選取資料庫功能表上的 [還原作業] 來加以追蹤。

    ![還原作業進度](./media/backup-azure-sql-database/restore-job-notification.png)



### <a name="restore-to-a-specific-restore-point"></a>還原至特定還原點

如果您已選取 [完整和差異] 作為還原類型，請執行下列動作：


1. 從清單中選取復原點，然後按一下 [確定] 以完成還原點程序。

    ![選擇完整復原點](./media/backup-azure-sql-database/choose-fd-recovery-point.png)
        
2. 在 [進階設定] 功能表上，如果您想要讓資料庫在還原之後保持不運作，請啟用 [使用 NORECOVERY 還原]。
3. 如果您要變更目的地伺服器上的還原位置，請輸入新的目標路徑。 
4. 按一下 [確定]。

    ![進階設定功能表](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

7. 在 [還原] 功能表上，選取 [還原] 以啟動還原作業。
8. 在 [通知] 區域中追蹤還原進度，或藉由選取資料庫功能表上的 [還原作業] 來加以追蹤。

    ![還原作業進度](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>後續步驟

[管理和監視](manage-monitor-sql-database-backup.md)由 Azure 備份進行備份的 SQL Server 資料庫。
