---
title: 使用適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus) (預覽) 設計即時儀表板教學課程
description: 本教學課程示範如何在適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus) (預覽) 上建立、填入及查詢分散式資料表。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 9f3473d83678ffea888dad736a9620006b2961f7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406399"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>教學課程：使用適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus) (預覽) 設計即時分析儀表板

在本教學課程中，您可以使用適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus) (預覽) 了解如何：

> [!div class="checklist"]
> * 建立 Hyperscale (Citus) 伺服器群組
> * 使用 psql 公用程式建立結構描述
> * 進行跨節點的資料表分區化
> * 產生範例資料
> * 執行彙總
> * 查詢原始和彙總資料
> * 使資料過期

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-an-azure-database-for-postgresql"></a>建立適用於 PostgreSQL 的 Azure 資料庫

請依照下列步驟來建立「適用於 PostgreSQL 的 Azure 資料庫」伺服器：
1. 按一下 Azure 入口網站左上角的 [建立資源]。
2. 從 [新增] 頁面中選取 [資料庫]，然後從 [資料庫] 頁面中選取 [適用於 PostgreSQL 的 Azure 資料庫]。
3. 針對部署選項，按一下 [Hyperscale (Citus) 伺服器群組 - 預覽] 下方的 [建立] 按鈕。
4. 在新伺服器詳細資料表單中填寫下列資訊︰
   - 資源群組：按一下此欄位的文字方塊下方的 [新建] 連結。 輸入名稱，例如 **myresourcegroup**。
   - 伺服器群組名稱：**mydemoserver** (伺服器的名稱 (對應至 DNS 名稱)，而且必須是全域唯一的)。
   - 系統管理員使用者名稱：**myadmin** (稍後用於連線到資料庫)。
   - 密碼：長度必須至少有 8 個字元，且必須包含下列類別中其中三種的字元 – 英文大寫字母、英文小寫字母、數字 (0-9) 和非英數字元 (!、$、#、% 等)。
   - 位置：使用最接近使用者的位置，讓他們能以最快速度存取資料。

   > [!IMPORTANT]
   > 必須要有您在此處指定的伺服器系統管理員登入和密碼，稍後在本教學課程中才能登入伺服器及其資料庫。 請記住或記錄此資訊，以供稍後使用。

5. 按一下 [設定伺服器群組]。 讓該區段中的設定維持不變，然後按一下 [儲存]。
6. 按一下 [檢閱 + 建立]，然後按一下 [建立] 以佈建伺服器。 佈建需要幾分鐘的時間。
7. 此頁面會重新導向以監視部署。 當即時狀態從 [您的部署正在進行中] 變更為 [您的部署已完成] 時，按一下頁面左側的 [輸出] 功能表項目。
8. [輸出] 頁面將包含協調器主機名稱，且旁邊會有按鈕可將此值複製到剪貼簿。 請記錄此資訊，以供後續使用。

## <a name="configure-a-server-level-firewall-rule"></a>設定伺服器層級防火牆規則

「適用於 PostgreSQL 的 Azure 資料庫」服務會使用伺服器層級的防火牆。 根據預設，防火牆會防止所有外部應用程式和工具連線到伺服器和伺服器上的任何資料庫。 我們必須新增規則，以針對特定 IP 位址範圍開啟防火牆。

1. 在您先前複製協調器節點主機名稱的 [輸出] 區段中，按一下 [上一頁] 返回 [概觀] 功能表項目。

2. 在資料清單中尋找部署的縮放群組，然後按一下該群組。 (其名稱前面會加上 "sg-"。)

3. 按一下左側功能表中 [安全性] 下方的 [防火牆]。

4. 按一下[+ 為目前的用戶端 IP 位址新增防火牆規則] 連結。 最後，按一下 [儲存] 按鈕。

5. 按一下 [檔案] 。

   > [!NOTE]
   > Azure PostgreSQL 伺服器會透過連接埠 5432 進行通訊。 如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 5432 的輸出流量。 若情況如此，除非 IT 部門開啟連接埠 5432，否則您無法連線至 Azure SQL Database 伺服器。
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>在 Cloud Shell 中使用 psql 連線到資料庫

現在，我們將使用 [psql](https://www.postgresql.org/docs/current/app-psql.html) 命令列公用程式來連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器。
1. 透過頂端瀏覽窗格上的終端機圖示啟動 Azure Cloud Shell。

   ![適用於 PostgreSQL 的 Azure 資料庫 - Azure Cloud Shell 終端機圖示](./media/tutorial-design-database-hyperscale-realtime/psql-cloud-shell.png)

2. Azure Cloud Shell 會在您的瀏覽器中開啟，讓您能夠輸入 bash 命令。

   ![適用於 PostgreSQL 的 Azure 資料庫 - Azure Shell Bash 提示字元](./media/tutorial-design-database-hyperscale-realtime/psql-bash.png)

3. 在 Cloud Shell 提示字元處，使用 psql 命令來連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器。 下列格式可用來透過 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 公用程式連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器：
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   例如，下列命令會使用存取認證，連線到 PostgreSQL 伺服器 **mydemoserver.postgres.database.azure.com** 上名為 **citus** 的預設資料庫。 在系統提示時輸入您的伺服器管理員密碼。

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>使用 psql 公用程式建立結構描述

使用 psql 連線到適用於 PostgreSQL 的 Azure 資料庫 - Hyperscale (Citus) (預覽) 後，您可以完成一些基本工作。 本教學課程會引導您擷取 Web 分析中的流量資料，然後彙總資料，以便根據該資料提供即時儀表板。

我們會建立一個資料表，該資料表將取用所有未經處理的 Web 流量資料。 在 psql 終端機中執行下列命令：

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

我們也會建立一個可保存每分鐘彙總的資料表，以及一個可維護最後一個彙總位置的資料表。 同時在 Psql 中執行下列命令：

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

您現在可以使用此 psql 命令來查看資料表清單中新建立的資料表：

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>進行跨節點的資料表分區化

超大規模部署會根據使用者指定的資料行值，在不同的節點上儲存資料表資料列。 此「散發資料行」表示跨節點的資料分區化方式。

讓我們將散發資料行設定為 site\_id，即分區索引鍵。 在 psql 中，執行下列函式：

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>產生範例資料

我們的伺服器群組現在應已準備好擷取一些資料。 我們可以在本機從我們的 `psql` 連線執行下列命令，以持續插入資料。

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('https://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

查詢大約會每 0.25 秒新增一個資料列。 如散發資料行 `site_id` 所引導，資料列會儲存在不同的背景工作角色節點上。

   > [!NOTE]
   > 讓資料產生查詢保持執行，並針對本教學課程中的剩餘命令，開啟第二個 psql 連線。
   >

## <a name="query"></a>查詢

超大規模裝載選項可讓多個節點平行處理查詢以加快速度。 例如，資料庫會在背景工作角色節點上計算 SUM 和 COUNT 等彙總，並將結果結合成最終的答案。

以下的查詢可計算每分鐘的 Web 要求以及一些統計資料。
嘗試在 psql 中執行該查詢並觀察結果。

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="performing-rollups"></a>執行彙總

以上查詢在早期階段可正常運作，但其效能會隨著您的資料擴充而下降。 即使使用分散式處理，預先計算此資料的速度會比重複重新計算還要快。

我們可以定期將未經處理的資料彙總到彙總資料表中，以確保儀表板保持快速。 在此情況下，我們會彙總到一分鐘彙總資料表中，但您也可有 5 分鐘、15 分鐘、一個小時等的彙總。

因為我們會持續執行此彙總，所以我們即將建立一個函式來執行它。 在 psql 中執行下列命令以建立 `rollup_http_request` 函式。

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

備妥我們的函式，加以執行來彙總資料：

```sql
SELECT rollup_http_request();
```

然後將資料放在預先彙總的表單中，我們即可查詢彙總資料表，以取得與稍早相同的報告。 執行下列命令：

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>使舊資料過期

彙總讓查詢速度變快，但我們仍需要使舊資料過期，以避免不受控制的儲存體成本。 只需決定您要針對每種細微性保留資料多久，並使用標準查詢來刪除過期的資料。 在下列範例中，我們已決定將未經處理的資料保留一天，並將每分鐘的彙總保留一個月：

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

在生產環境中，您可將這些查詢包裝在函式中，並且在 cron 作業中每分鐘呼叫該函式。

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在伺服器群組中建立了 Azure 資源。 如果您認為未來不需要這些資源，請刪除伺服器群組。 在您伺服器群組的 [概觀] 頁面中，按一下 [刪除] 按鈕。 當快顯頁面上出現提示時，請確認伺服器群組的名稱，然後按一下最後一個 [刪除] 按鈕。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何佈建 Hyperscale (Citus) 伺服器群組。 您已使用 psql 連線到該群組、建立結構描述，以及分散資料。 您已了解如何查詢未經處理格式的資料、定期彙總該資料、查詢彙總的資料表，以及使舊資料過期。

接下來，了解超大規模的概念。
> [!div class="nextstepaction"]
> [Hyperscale 節點類型](https://aka.ms/hyperscale-concepts)