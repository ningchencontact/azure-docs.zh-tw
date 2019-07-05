---
title: 設定 Azure 資料庫中的資料在複寫中，適用於 MariaDB |Microsoft Docs
description: 本文說明如何設定資料在複寫的 Azure 資料庫中適用於 MariaDB。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 21e8a88cc6f03b4d54a6c5299b0b6be36cc32d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444797"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>設定 Azure 資料庫中的資料在複寫中，適用於 MariaDB

本文說明如何設定資料在複寫的 Azure 資料庫中適用於 MariaDB 藉由設定在主要和複本伺服器。 本文假設您有一些經驗與 MariaDB 伺服器和資料庫。

若要建立 MariaDB 服務的 Azure 資料庫的複本，資料在複寫同步處理主要 MariaDB 伺服器內部部署，在虛擬機器 (Vm)、 中或在雲端資料庫服務中的資料。

> [!NOTE]
> 如果您的主要伺服器是 10.2 或更新版本的版本，我們建議您在使用設定資料複寫[全域交易識別碼](https://mariadb.com/kb/en/library/gtid/)。


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>建立 MariaDB 伺服器，以做為複本

1. 建立新的 Azure 資料庫針對 MariaDB 伺服器 (例如 replica.mariadb.database.azure.com)。 伺服器是複本伺服器的資料在複寫中。

    若要深入了解建立伺服器，請參閱[使用 Azure 入口網站建立 Azure Database for MariaDB 伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)。

   > [!IMPORTANT]
   > 您必須建立 MariaDB server 的 Azure 資料庫一般目的或記憶體最佳化的定價層中。

2. 建立相同的使用者帳戶和對應的權限。
    
    使用者帳戶不到複本伺服器會從主要伺服器複寫。 若要提供到複本伺服器的使用者存取權，您必須手動建立所有的帳戶及對應的權限上新建立的 Azure 資料庫針對 MariaDB 伺服器。

## <a name="configure-the-master-server"></a>設定主要伺服器

下列步驟會準備並設定 MariaDB 伺服器裝載在內部，在 VM 中，或在資料中複寫的雲端資料庫服務。 MariaDB 伺服器是資料在複寫中的主機。

1. 開啟二進位記錄功能。
    
    若要查看在主機上是否已啟用二進位記錄，請輸入下列命令：

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果變數[ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin)傳回的值`ON`，在您的伺服器上啟用二進位記錄。

   如果`log_bin`傳回的值`OFF`，編輯**my.cnf**檔案，讓`log_bin=ON`開啟二進位記錄。 重新啟動伺服器，變更才會生效。

2. 設定主要伺服器設定。

    資料在複寫要求參數`lower_case_table_names`的主要和複本伺服器之間保持一致。 `lower_case_table_names`參數設為`1`預設會在適用於 MariaDB 的 Azure 資料庫。

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. 建立新的複寫角色，並設定權限。

   建立使用者帳戶具有複寫權限設定主要伺服器上。 您可以使用 SQL 命令或 MySQL Workbench 來建立帳戶。 如果您計劃與 SSL 複寫，您必須指定此，當您建立使用者帳戶。
   
   若要了解如何在您的主要伺服器上新增使用者帳戶，請參閱[MariaDB 文件](https://mariadb.com/kb/en/library/create-user/)。

   藉由使用下列命令，新的複寫角色可以從任何電腦，而不只是裝載主機本身的機器存取主機。 針對此存取權，指定**syncuser\@'%'** 在命令中建立的使用者。
   
   若要深入了解 MariaDB 文件，請參閱[指定帳戶名稱](https://mariadb.com/kb/en/library/create-user/#account-names)。

   **SQL 命令**

   - 使用 SSL 的複寫

       若要要求所有使用者連線使用 SSL，輸入下列命令來建立使用者：

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - 沒有 SSL 的複寫

       如果 SSL 不需要的所有連線中，輸入下列命令來建立使用者：
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   在中建立 MySQL Workbench 中的複寫角色**管理**窗格中，選取**使用者和權限**。 然後選取**新增帳戶**。
 
   ![使用者和權限](./media/howto-data-in-replication/users_privileges.png)

   輸入中的使用者名稱**登入名稱**欄位。

   ![同步處理使用者](./media/howto-data-in-replication/syncuser.png)
 
   選取 [**系統管理角色**] 面板中，然後在清單**全域權限**，選取**Replication Slave**。 選取 **套用**建立複寫角色。

   ![複寫從屬](./media/howto-data-in-replication/replicationslave.png)


4. 主要伺服器設為唯讀模式。

   傾印資料庫時，則伺服器必須放在唯讀模式。 在唯讀模式時，主要無法處理任何寫入交易。 為了避免業務衝擊，排定在離峰時間的唯讀視窗。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. 取得目前的二進位記錄檔名稱和位移。

   若要判斷目前的二進位記錄檔名稱和位移，請執行命令[ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/)。
    
   ```sql
   show master status;
   ```
   結果應該類似下列資料表：
   
   ![主要狀態結果](./media/howto-data-in-replication/masterstatus.png)

   記下二進位檔案名稱，因為會在稍後步驟中使用它。
   
6. （選擇性，含 GTID 複寫所需），請取得 GTID 位置。

   執行函式[ `BINLOG_GTID_POS` ](https://mariadb.com/kb/en/library/binlog_gtid_pos/) GTID 位置取得相對應的 binlog 檔案名稱和位移。
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>傾印和還原主要伺服器

1. 傾印從主要伺服器的所有資料庫。

   您可以使用 mysqldump （英文），傾印從主要伺服器的所有資料庫。 它不需要傾印 MySQL 程式庫和測試程式庫。

    如需詳細資訊，請參閱 <<c0> [ 傾印和還原](howto-migrate-dump-restore.md)。

2. 設定主要伺服器，以讀取/寫入模式。

   傾印資料庫之後，變更主要為讀取/寫入模式的 MariaDB 伺服器後。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 傾印檔案還原到新的伺服器。

   將傾印檔案還原至在適用於 MariaDB 的 Azure 資料庫服務中建立的伺服器。 請參閱[傾印和還原](howto-migrate-dump-restore.md)如何還原到 MariaDB 伺服器的傾印檔案。

   如果很大的傾印檔案，將它上傳至 Azure 中的 VM 做為複本伺服器在相同區域內。 將它還原到 Azure Database for MariaDB 伺服器從 VM。

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>將主要和複本的伺服器，以啟動資料在複寫連結

1. 設定主要伺服器。

   所有「複寫中的資料」功能都可由已儲存的程序執行完成。 您可在[複寫中的資料已儲存的程序](reference-data-in-stored-procedures.md)中找到所有程序。 可以在 MySQL 殼層或 MySQL Workbench 中執行預存程序。

   若要連結兩部伺服器，並啟動複寫，請登入目標複本伺服器 MariaDB 服務的 Azure 資料庫中。 使用接下來，設定為主要伺服器的外部執行個體`mysql.az_replication_change_master`或`mysql.az_replication_change_master_with_gtid`MariaDB 伺服器的 Azure DB 預存程序。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   或
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host：主要伺服器的主機名稱
   - master_user：主要伺服器的使用者名稱
   - master_password：主要伺服器的密碼
   - master_log_file：執行 `show master status` 產生的二進位記錄檔的名稱
   - master_log_pos：執行 `show master status` 產生的二進位記錄檔的位置
   - master_gtid_pos:無法執行 GTID 位置 `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca：CA 憑證的內容。 如果您不使用 SSL，會傳入空的 string.*
    
    
    \* 我們建議您為變數傳入 master_ssl_ca 參數。 如需詳細資訊，請參閱下列範例。

   **範例**

   - 使用 SSL 的複寫

       建立變數`@cert`藉由執行下列命令：

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       使用 SSL 的複寫設定主要伺服器裝載於網域 companya.com 中，與適用於 MariaDB 的 Azure 資料庫中裝載的複本伺服器之間。 此已儲存的程序可在複本伺服器上執行。
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - 沒有 SSL 的複寫

       沒有 SSL 的複寫設定主要伺服器裝載於網域 companya.com 中，與適用於 MariaDB 的 Azure 資料庫中裝載的複本伺服器之間。 此已儲存的程序可在複本伺服器上執行。

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. 啟動複寫。

   呼叫`mysql.az_replication_start`預存程序開始複寫。

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 檢查複寫狀態。

   在複本伺服器上呼叫 [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) 命令，以檢視複寫狀態。
    
   ```sql
   show slave status;
   ```

   如果`Slave_IO_Running`並`Slave_SQL_Running`處於狀態`yes`，，而`Seconds_Behind_Master`是`0`，複寫是否運作正常。 `Seconds_Behind_Master` 可指定複本的延遲時間。 如果值不是`0`，則複本正在處理更新。

4. 更新對應的伺服器變數，讓資料在複寫更安全 （只有需要不含 GTID 複寫）。
    
    由於在 MariaDB 原生複寫限制，您必須設定[ `sync_master_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info)並[ `sync_relay_log_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info)變數上不含 GTID 案例複寫。

    檢查您的從屬伺服器`sync_master_info`並`sync_relay_log_info`變數，以確保資料在複寫是以穩定，並將變數設定為`1`。
    
## <a name="other-stored-procedures"></a>其他已儲存的程序

### <a name="stop-replication"></a>停止複寫

若要停止主要和複本伺服器之間的複寫，請使用下列已儲存的程序：

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>移除複寫關聯性

若要移除主要與複本伺服器之間的關聯性，請使用下列的預存程序：

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>略過複寫錯誤

略過複寫錯誤，並允許進行複寫，請使用下列的預存程序：
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>後續步驟
深入了解「適用於 MariaDB 的 Azure 資料庫」的[資料輸入複寫](concepts-data-in-replication.md)。
