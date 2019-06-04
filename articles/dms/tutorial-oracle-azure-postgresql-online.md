---
title: 教學課程：使用 Azure 資料庫移轉服務在線上將 Oracle 移轉至適用於 PostgreSQL 的 Azure 資料庫 | Microsoft Docs
description: 了解如何使用 Azure 資料庫移轉服務，在線上將內部部署的或虛擬機器上的 Oracle 移轉至適用於 PostgreSQL 的 Azure 資料庫。
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 0b3af3d29e6e938f0301d751a79170c7c1964b45
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243807"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>教學課程：使用 DMS 在線上將 Oracle 遷移至適用於 PostgreSQL 的 Azure 資料庫 (預覽)

您可以使用 Azure 資料庫移轉服務，在最短的停機時間內將資料庫從裝載在內部部署或位於虛擬機器上的 Oracle 資料庫遷移至[適用於 PostgreSQL 的 Azure 資料庫](https://docs.microsoft.com/azure/postgresql/)。 換句話說，應用程式可在最短的停機時間內完成移轉。 在此教學課程中，您會在 Azure 資料庫移轉服務中使用線上移轉活動，將 **HR** 資料庫範例從內部部署的 Oracle 11g 或從 Oracle 11g 的虛擬機器執行個體，遷移至適用於 PostgreSQL 的 Azure 資料庫。

在本教學課程中，您了解如何：
> [!div class="checklist"]
>
> * 使用 ora2pg 工具評估移轉工作。
> * 使用 ora2pg 工具遷移結構描述範例。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 使用 Azure 資料庫移轉服務來建立移轉專案。
> * 執行移轉。
> * 監視移轉。

> [!NOTE]
> 若要使用「Azure 資料庫移轉服務」來執行線上移轉，必須根據「進階」定價層建立執行個體。

> [!IMPORTANT]
> 為了獲得最佳的移轉體驗，Microsoft 建議在目標資料庫所在的同一個 Azure 區域中，建立 Azure 資料庫移轉服務的執行個體。 跨區域或地理位置移動資料可能使移轉程序變慢，並產生錯誤。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文說明如何在線上從 Oracle 遷移至適用於 PostgreSQL 的 Azure 資料庫。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要：

* 下載並安裝 [Oracle 11g Release 2 (Standard Edition、Standard Edition One 或 Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)。
* 從[這裡](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002)下載 **HR** 資料庫範例。
* 在 [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) 或 [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf) 上，下載並安裝 ora2pg。
* [在適用於 PostgreSQL 的 Azure 資料庫中建立執行個體](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)。
* 連線至執行個體，並使用此[文件](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal)中的指令建立資料庫。
* 使用 Azure Resource Manager 部署模型建立 Azure 資料庫移轉服務的 Azure 虛擬網路 (VNet)，以使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 為您的內部部署來源伺服器提供站對站連線能力。 如需建立 VNet 的詳細資訊，請參閱[虛擬網路文件](https://docs.microsoft.com/azure/virtual-network/)，特別是快速入門文章，裡面會提供逐步操作詳細資料。

  > [!NOTE]
  > 在 VNet 設定期間，如果您搭配與 Microsoft 對等互連的網路使用 ExpressRoute，請將下列服務[端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)新增至將佈建服務的子網路：
  > * 目標資料庫端點 (例如，SQL 端點、Cosmos DB 端點等)
  > * 儲存體端點
  > * 服務匯流排端點
  >
  > 此為必要設定，因為 Azure 資料庫移轉服務沒有網際網路連線。

* 確定您的 VNet 網路安全性群組 (NSG) 規則不會對 Azure 資料庫移轉服務封鎖下列輸入通訊埠：443、53、9354、445、12000。 如需 Azure VNet NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)。
* 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 Oracle 伺服器 (依預設會使用 TCP 連接埠 1521)。
* 使用來源資料庫前面的防火牆應用裝置時，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取來源資料庫，以進行移轉。
* 為適用於 PostgreSQL 的 Azure 資料庫建立伺服器層級的[防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，以允許 Azure 資料庫移轉服務存取目標資料庫。 提供用於 Azure 資料庫移轉服務之 VNet 的子網路範圍。
* 啟用來源 Oracle 資料庫的存取權。

  > [!NOTE]
  > 使用者必須有 DBA 角色才能連線至 Oracle 來源。

  * Azure 資料庫移轉服務中的增量同步處理必須有封存重做記錄，才能擷取資料變更。 請遵循下列步驟來設定 Oracle 來源：
    * 藉由執行下列命令來使用 SYSDBA 權限進行登入：

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * 藉由執行下列命令來關閉資料庫執行個體。

      ```
      SHUTDOWN IMMEDIATE;
      ```

      等候確認 `'ORACLE instance shut down'`。

    * 藉由執行下列命令，啟動新的執行個體並掛接 (但不要開啟) 資料庫以啟用或停用封存：

      ```
      STARTUP MOUNT;
      ```

      資料庫必須完成掛接；等候確認 'Oracle instance started'。

    * 藉由執行下列命令來變更資料庫封存模式：

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * 藉由執行下列命令來開啟資料庫進行一般作業：

      ```
      ALTER DATABASE OPEN;
      ```

      您可能必須先重新啟動，才會出現 ARC 檔案。

    * 若要確認，請執行下列命令：

      ```
      SELECT log_mode FROM v$database;
      ```

      您應該會收到回應 `'ARCHIVELOG'`。 如果回應是 `'NOARCHIVELOG'`，則未符合需求。

  * 使用下列其中一個選項啟用補充記錄以便進行複寫。

    * **選項 1**。
      變更資料庫層級的補充記錄，以涵蓋所有具有 PK 和唯一索引的資料表。 偵測查詢會傳回 `'IMPLICIT'`。

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      變更資料表層級的補充記錄。 只會針對擁有資料操作但沒有 PK 或唯一索引的資料表來執行。

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **選項 2**。
      變更資料庫層級的補充記錄以涵蓋所有資料表，然後偵測查詢會傳回 `'YES'`。

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      變更資料表層級的補充記錄。 遵循下列邏輯，對每個資料表只執行一個陳述式。

      如果資料表有主索引鍵：

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      如果資料表有唯一索引：

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      否則，請執行下列命令：

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    若要確認，請執行下列命令：

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    您應該會收到回應 `'YES'`。

> [!IMPORTANT]
> 針對此案例的公開預覽版本，Azure 資料庫移轉服務可支援 Oracle 10g 或 11g 版本。 執行 Oracle 12c 或更新版本的客戶請注意，想要讓 ODBC 驅動程式能夠連線至 Oracle，驗證通訊協定至少必須是 8。 若為 12c 或更新版本的 Oracle 來源，則必須將驗證通訊協定設定如下：
>
> * 更新 SQLNET.ORA：
>
>    ```
>    SQLNET.ALLOWED_LOGON_VERSION_CLIENT = 8
>    SQLNET.ALLOWED_LOGON_VERSION_SERVER = 8
>    ```
>
> * 重新啟動電腦，以使新的設定生效。
> * 變更現有使用者的密碼：
>
>    ```
>    ALTER USER system IDENTIFIED BY {pswd}
>    ```
>
>   如需詳細資訊，請參閱[這裡](http://www.dba-oracle.com/t_allowed_login_version_server.htm)的頁面。
>
> 最後請記住，變更驗證通訊協定可能會影響用戶端的驗證。

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>評估將 Oracle 遷移至適用於 PostgreSQL 的 Azure 資料庫所需進行的工作

建議您使用 ora2pg 來評估從 Oracle 遷移至適用於 PostgreSQL 的 Azure 資料庫時所需進行的工作。 請使用 `ora2pg -t SHOW_REPORT` 指示詞來建立報告，於其中列出所有的 Oracle 物件、估計的移轉成本 (以開發人員工作天數計算)，以及某些可能要在轉換過程中特別注意的資料庫物件。

大多數客戶會花大量時間檢閱評估報告，並考慮自動和手動進行轉換所需的工作。

若要設定及執行 ora2pg 以建立評估報告，請參閱[從 Oracle 遷移至適用於 PostgreSQL 的 Azure 資料庫逐步指南](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)的**移轉前：評估**一節。 [這裡](http://ora2pg.darold.net/report.html)有 ora2pg 評估報告範例可供參考。

## <a name="export-the-oracle-schema"></a>匯出 Oracle 結構描述

建議您使用 ora2pg 將 Oracle 結構描述和其他 Oracle 物件 (類型、程序、函式等) 轉換成可與適用於 PostgreSQL 的 Azure 資料庫相容的結構描述。 ora2pg 含有許多指示詞，可協助您預先定義某些資料類型。 例如，您可以使用 `DATA_TYPE` 指示詞，來將所有 NUMBER(*,0) 取代為 bigint 而非 NUMERIC(38)。

您可以執行 ora2pg 以將每個資料庫物件匯出到 .sql 檔案中。 然後，您可以先檢閱 .sql 檔案再使用 psql 將其匯入到適用於 PostgreSQL 的 Azure 資料庫中，或者，您也可以在 PgAdmin 中執行 .SQL 指令碼。

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

例如︰

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

若要設定及執行 ora2pg 以轉換結構描述，請參閱[從 Oracle 遷移至適用於 PostgreSQL 的 Azure 資料庫逐步指南](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)的**移轉：結構描述和資料**一節。

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>在適用於 PostgreSQL 的 Azure 資料庫中設定結構描述

根據預設，Oracle 會將 schema.table.column 保持在全都大寫的狀態，PostgreSQL 則會將 schema.table.column 保持在小寫狀態。 若要讓 Azure 資料庫移轉服務開始將資料從 Oracle 移至適用於 PostgreSQL 的 Azure 資料庫，schema.table.column 的大小寫格式必須和 Oracle 來源相同。

例如，如果 Oracle 來源的結構描述為 “HR”.”EMPLOYEES”.”EMPLOYEE_ID”，則 PostgreSQL 的結構描述必須使用相同格式。

若要確保 schema.table.column 的大小寫格式在 Oracle 和適用於 PostgreSQL 的 Azure 資料庫中是相同的，建議您使用下列步驟。

> [!NOTE]
> 您可以使用不同的方法來衍生大寫結構描述。 我們正努力改善此步驟並讓其自動執行。

1. 使用 ora2pg 匯出小寫的結構描述。 在資料表建立 sql 指令碼中，請以手動方式建立大寫的 "SCHEMA" 結構描述。
2. 將其餘 Oracle 物件 (例如，觸發程序、序列、程序、類型和函式) 匯入到適用於 PostgreSQL 的 Azure 資料庫。
3. 若要讓 TABLE 和 COLUMN 變成大寫，請執行下列指令碼：

   ```
   -- INPUT: schema name
   set schema.var = “HR”;

   -- Generate statements to rename tables and columns
   SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
   UNION ALL 
   SELECT 2, 'alter table "'||c.relname||'" rename '||a.attname||' to "'||upper(a.attname)||'";'
   FROM pg_class c
   JOIN pg_attribute a ON a.attrelid = c.oid
   JOIN pg_type t ON a.atttypid = t.oid
   LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
    AND r.conname = a.attname
   WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
   UNION ALL
   SELECT 3, 'alter table '||c.relname||' rename to "'||upper(c.relname)||'";'
   FROM pg_catalog.pg_class c
    LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
   WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
   ORDER BY 1;
   ```

* 在目標資料庫中放入外部索引鍵，以便能夠執行完整載入。 如需可用來放入外部索引鍵的指令碼，請參閱[此處](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)文章的**遷移結構描述範例**一節。
* 使用 Azure 資料庫移轉服務來執行完整載入和同步處理。
* 當作為目標的適用於 PostgreSQL 的 Azure 資料庫執行個體中的資料與來源一致時，請在 Azure 資料庫移轉服務中執行資料庫完全移轉。
* 若要讓 SCHEMA、TABLE 和 COLUMN 變成小寫 (如果適用於 PostgreSQL 的 Azure 資料庫的結構描述應該使用小寫才能進行應用程式查詢)，請執行下列指令碼：

  ```
  -- INPUT: schema name
  set schema.var = hr;
  
  -- Generate statements to rename tables and columns
  SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
  UNION ALL
  SELECT 2, 'alter table "'||c.relname||'" rename "'||a.attname||'" to '||lower(a.attname)||';'
  FROM pg_class c
  JOIN pg_attribute a ON a.attrelid = c.oid
  JOIN pg_type t ON a.atttypid = t.oid
  LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
     AND r.conname = a.attname
  WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
  UNION ALL
  SELECT 3, 'alter table "'||c.relname||'" rename to '||lower(c.relname)||';'
  FROM pg_catalog.pg_class c
     LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
  WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
  ORDER BY 1;
  ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]  ，然後選取 [訂用帳戶]  。

   ![顯示入口網站訂用帳戶](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]  。

    ![顯示資源提供者](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. 搜尋移轉，然後在 [Microsoft.DataMigration]  的右邊，選取 [註冊]  。

    ![註冊資源提供者](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>建立 DMS 執行個體

1. 在 Azure 入口網站中，選取 [+ 建立資源]  ，搜尋「Azure 資料庫移轉服務」，然後從下拉式清單選取 [Azure 資料庫移轉服務]  。

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務]  畫面上，選取 [建立]  。

    ![建立 Azure 資料庫移轉服務執行個體](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. 在 [建立移轉服務]  畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取現有 VNet 或建立新的 VNet。

    VNet 會為 Azure 資料庫移轉服務提供來源 Oracle 和目標適用於 PostgreSQL 的 Azure 資料庫執行個體的存取權。

    如需有關如何在 Azure 入口網站中建立 VNet 的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](https://aka.ms/DMSVnet)一文。

5. 選取定價層。

    如需成本和定價層的詳細資訊，請參閱[定價分頁](https://aka.ms/dms-pricing)。

    ![設定 Azure 資料庫移轉服務執行個體設定](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. 選取 [建立]  以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]  ，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]  。

    ![找出 Azure 資料庫移轉服務的所有執行個體](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. 在 [Azure 資料庫移轉服務]  畫面上，搜尋您建立的 Azure 資料庫移轉服務執行個體名稱，然後選取該執行個體。

    ![找出您的 Azure 資料庫移轉服務執行個體](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. 選取 [+ 新增移轉專案]  。
4. 在 [新增移轉專案]  畫面上指定專案名稱，並在 [來源伺服器類型]  文字方塊中中選取 [Oracle]  ，然後在 [目標伺服器類型]  文字方塊中選取 [適用於 PostgreSQL 的 Azure 資料庫]  。
5. 在 [選擇活動類型]  區段中，選取 [線上資料移轉]  。

   ![建立資料庫移轉服務專案](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > 或者，您可以選擇 [僅建立專案]  以立即建立移轉專案，並於後續再執行移轉。

6. 選取 [儲存]  ，並記下要成功使用 Azure 資料庫移轉服務來執行線上移轉的需求，然後選取 [建立及執行活動]  。

## <a name="specify-source-details"></a>指定來源詳細資料

* 在 [新增來源詳細資料]  畫面上，指定來源 Oracle 執行個體的連線詳細資料。

  ![新增來源詳細資料畫面](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>上傳 Oracle OCI 驅動程式

1. 選取 [儲存]  ，然後在 [安裝 OCI 驅動程式]  畫面上登入 Oracle 帳戶，並從[這裡](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst)下載驅動程式 **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37,128,586 Byte(s)) (SHA1 總和檢查碼：865082268)。
2. 將驅動程式下載到共用資料夾。

   請確定該資料夾有與指定了最低唯讀存取權的使用者名稱共用。 Azure 資料庫移轉服務會藉由模擬您所指定的使用者名稱來存取並讀取共用，以將 OCI 驅動程式上傳至 Azure。

   您所指定的使用者名稱必須是 Windows 使用者帳戶。

   ![OCI 驅動程式安裝](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>指定目標詳細資料

1. 選取 [儲存]  ，然後在 [目標詳細資料]  畫面上指定目標「適用於 PostgreSQL 的 Azure 資料庫」伺服器的連線詳細資料；此伺服器是 **HR** 結構描述部署所在的預先佈建「適用於 PostgreSQL 的 Azure 資料庫」執行個體。

    ![目標詳細資料畫面](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. 選取 [儲存]  ，然後在 [對應到目標資料庫]  畫面上，對應要進行移轉的來源和目標資料庫。

    如果目標資料庫包含與來源資料庫相同的資料庫名稱，Azure 資料庫移轉服務依預設會選取目標資料庫。

    ![對應到目標資料庫](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. 選取 [儲存]  ，在 [移轉摘要]  畫面的 [活動名稱]  文字方塊中，指定移轉活動的名稱，然後檢閱摘要，以確定來源和目標詳細資料都與您先前的指定相符。

    ![移轉摘要](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>執行移轉

* 選取 [執行移轉]  。

  [移轉活動] 視窗隨即出現，且活動的 [狀態]  為 [正在初始化]  。

## <a name="monitor-the-migration"></a>監視移轉

1. 在移轉活動畫面上，選取 [重新整理]  以更新顯示，直到移轉的 [狀態]  顯示為 [執行中]  為止。

     ![活動狀態 - 執行中](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. 在 [資料庫名稱]  下方選取特定資料庫，以取得 [載入完整資料]  和 [累加式資料同步]  作業的移轉狀態。

    完整資料載入會顯示初始載入移轉狀態，而累加式資料同步會顯示異動資料擷取 (CDC) 狀態。

     ![活動狀態 - 完整載入已完成](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![活動狀態 - 累加式資料同步](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>執行完全移轉

初始完整載入完成後，資料庫會標示為 [已可執行完全移轉]  。

1. 當您準備好要完成資料庫移轉後，請選取 [開始完全移轉]  。

2. 確實停止所有傳入來源資料庫的交易；等到 [暫止的變更]  計數器顯示為 **0** 為止。

   ![開始完全移轉](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. 選取 [確認]  ，然後選取 [套用]  。
4. 當資料庫移轉狀態顯示為 [已完成]  時，請將應用程式連線至新的目標「適用於 PostgreSQL 的 Azure 資料庫」執行個體。

 > [!NOTE]
 > 因為 PostgreSQL 預設會使用小寫的 schema.table.column，所以您可以使用本文稍早的**在適用於 PostgreSQL 的 Azure 資料庫中設定結構描述**區段中所提供的指令碼，將格式從大寫還原為小寫。

## <a name="next-steps"></a>後續步驟

* 如需在線上移轉至「適用於 PostgreSQL 的 Azure 資料庫」時的已知問題與限制，請參閱[適用於 PostgreSQL 的 Azure 資料庫線上移轉的已知問題與因應措施](known-issues-azure-postgresql-online.md)一文。
* 如需 Azure 資料庫移轉服務的相關資訊，請參閱[什麼是 Azure 資料庫移轉服務？](https://docs.microsoft.com/azure/dms/dms-overview)一文。
* 如需適用於 PostgreSQL 的 Azure 資料庫的相關資訊，請參閱[什麼是 Azure Database for PostgreSQL？](https://docs.microsoft.com/azure/postgresql/overview)一文。
