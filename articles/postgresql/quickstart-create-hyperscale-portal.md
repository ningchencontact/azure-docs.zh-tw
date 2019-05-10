---
title: 適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus) (預覽) 快速入門
description: 在適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus) (預覽) 上建立及查詢分散式資料表的快速入門。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 30de4da43569abf4d7bd668fd0fa481ecac23f4d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080026"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus) (預覽)

Azure Database for PostgreSQL 是一種受控服務，您用來在雲端執行、管理及調整高可用性的 PostgreSQL 資料庫。 本快速入門說明如何使用 Azure 入口網站，建立適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus) (預覽) 伺服器群組。 您將探索分散式資料：進行跨節點的資料表分區化、擷取範例資料，以及在多個節點上執行查詢。

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
   - 伺服器群組名稱：輸入新伺服器群組的唯一名稱，該名稱也會用於伺服器子網域。
   - 系統管理員使用者名稱：輸入唯一的使用者名稱 (稍後用於連線到資料庫)。
   - 密碼：長度必須至少有 8 個字元，且必須包含下列類別中其中三種的字元 – 英文大寫字母、英文小寫字母、數字 (0-9) 和非英數字元 (!、$、#、% 等)。
   - 位置：使用最接近使用者的位置，讓他們能以最快速度存取資料。

   > [!IMPORTANT]
   > 需要伺服器系統管理員登入以及您在此處指定的密碼，稍後才能在本快速入門中登入伺服器及其資料庫。 請記住或記錄此資訊，以供稍後使用。

5. 按一下 [設定伺服器群組]。 讓該區段中的設定維持不變，然後按一下 [儲存]。
6. 按一下 [檢閱 + 建立]，然後按一下 [建立] 以佈建伺服器。 佈建需要幾分鐘的時間。
7. 此頁面會重新導向以監視部署。 當即時狀態從 [您的部署正在進行中] 變更為 [您的部署已完成] 時，按一下頁面左邊的 [輸出] 功能表項目。
8. [輸出] 頁面將包含協調器主機名稱，其旁邊有個按鈕可將此值複製到剪貼簿。 請記錄此資訊，以供稍後使用。

## <a name="configure-a-server-level-firewall-rule"></a>設定伺服器層級防火牆規則

「適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus) (預覽)」服務會使用伺服器層級的防火牆。 根據預設，防火牆會防止所有外部應用程式和工具連線到協調器節點及其中的任何資料庫。 我們必須新增規則，以針對特定 IP 位址範圍開啟防火牆。

1. 從您先前複製協調器節點主機名稱的 [輸出] 區段，按一下 [概觀] 功能表項目。

2. 您部署的縮放群組名稱前面會加上 "sg-"。 在資源清單中找到它並且按一下。

3. 按一下左側功能表中 [安全性] 底下的 [防火牆]。

4. 按一下[+ 為目前的用戶端 IP 位址新增防火牆規則] 連結。 最後，按一下 [儲存] 按鈕。

5. 按一下 [檔案] 。

   > [!NOTE]
   > Azure PostgreSQL 伺服器會透過連接埠 5432 進行通訊。 如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 5432 的輸出流量。 若情況如此，除非 IT 部門開啟連接埠 5432，否則您無法連線至 Azure SQL Database 伺服器。
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>在 Cloud Shell 中使用 psql 連線到資料庫

現在，我們將使用 [psql](https://www.postgresql.org/docs/current/app-psql.html) 命令列公用程式來連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器。
1. 透過頂端瀏覽窗格上的終端機圖示啟動 Azure Cloud Shell。

   ![適用於 PostgreSQL 的 Azure 資料庫 - Azure Cloud Shell 終端機圖示](./media/quickstart-create-hyperscale-portal/psql-cloud-shell.png)

2. Azure Cloud Shell 會在您的瀏覽器中開啟，讓您能夠輸入 bash 命令。

   ![適用於 PostgreSQL 的 Azure 資料庫 - Azure Shell Bash 提示字元](./media/quickstart-create-hyperscale-portal/psql-bash.png)

3. 在 Cloud Shell 提示字元處，使用 psql 命令來連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器。 下列格式可用來透過 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 公用程式連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器：
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   例如，下列命令會使用存取認證，連線到 PostgreSQL 伺服器 **mydemoserver.postgres.database.azure.com** 上名為 **citus** 的預設資料庫。 在系統提示時輸入您的伺服器管理員密碼。

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="create-and-distribute-tables"></a>建立和散發資料表

使用 psql 連線到 Hyperscale 協調器節點後，您可以完成一些基本工作。

Hyperscale 伺服器內有三種類型的資料表：

- 分散式或分區化資料表 (已分散有助於調整效能與平行處理)
- 參考資料表 (維護多份)
- 本機資料表 (通常用於內部管理資料表)

在本快速入門中，我們主要著重於分散式資料表並且熟悉它們。

我們即將使用的資料模型十分簡單：來自 GitHub 的使用者和事件資料。 事件包括分支建立、組織相關 git 認可等等。

在您透過 psql 連線後，我們會建立我們的資料表。 在 psql 主控台中執行：

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

`github_events` 的 `payload` 欄位具有 JSONB 資料類型。 JSONB 是 Postgres 中二進位格式的 JSON 資料類型。 這可讓您在單一資料行中輕鬆地儲存更有彈性的結構描述。

Postgres 可以建立此類型的 `GIN` 索引，該索引將會檢索每個索引鍵和其值。 使用索引，即可利用各種條件快速又輕鬆地查詢承載。 我們會繼續建立幾個索引，再載入我們的資料。 在 psql 中：

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

接著，我們會採用協調器節點上的 Postgres 資料表，並告知 Hyperscale 跨 背景工作角色進行這些資料表的分區化。 為了這麼做，我們會針對每個資料表執行查詢，並指定要在其中進行分區化的索引鍵。 在目前的範例中，我們會在 `user_id` 上將 events 和 users 資料表分區化：

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

我們準備好載入資料。 下載兩個範例檔案 [users.csv](https://examples.citusdata.com/users.csv) 和 [events.csv](https://examples.citusdata.com/events.csv)。 下載檔案之後，使用 psql 連線到資料庫，小心從包含所下載檔案的目錄執行 psql。 使用 `\copy` 命令載入資料：

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>執行查詢

現在輪到有趣的部分，實際執行一些查詢。 我們會從簡單的 `count (*)` 著手，以查看我們所載入的資料量：

```sql
SELECT count(*) from github_events;
```

運作良好。 我們很快會回到這種彙總，但現在讓我們看看其他幾個查詢。 JSONB `payload` 資料行內有不少資料，但資料會因事件類型而有所不同。 `PushEvent` 事件包含的大小包括推送的相異認可數目。 我們可使用它來尋找每小時的認可總數：

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

到目前為止，查詢已獨家參與 github\_events，但我們可以結合此資訊與 github\_users。 因為我們已將相同識別碼 (`user_id`) 上的 users 和 events 分區化，所以使用者識別碼相符的這兩個資料表上的資料列將[共置](http://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation)於相同的資料庫節點上，並可輕鬆地聯結。

如果我們在 `user_id` 上聯結，Hyperscale 可以將聯結執行向下推送到分區，進而以平行方式在背景工作角色節點上執行。 例如，讓我們找出建立最多存放庫的使用者：

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在伺服器群組中建立了 Azure 資源。 如果您認為未來不需要這些資源，請刪除伺服器群組。 在您伺服器群組的 [概觀] 頁面中，按一下 [刪除] 按鈕。 當快顯頁面上出現系統提示時，確認伺服器群組的名稱，然後按一下最後一個 [刪除] 按鈕。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何佈建 Hyperscale (Citus) 伺服器群組。 您已使用 psql 連線到該群組、建立結構描述，以及分散資料。

接下來，遵循教學課程以建置可擴充的多租用戶應用程式。
> [!div class="nextstepaction"]
> [設計多租用戶資料庫](https://aka.ms/hyperscale-tutorial-multi-tenant)
