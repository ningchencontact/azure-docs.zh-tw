---
title: 使用適用於 PostgreSQL 的 Azure 資料庫 – 超大規模 (Citus) (預覽) 設計多租用戶資料庫的教學課程
description: 本教學課程說明如何在適用於 PostgreSQL 的 Azure 資料庫 – 超大規模 (Citus) (預覽) 上建立、填入及查詢分散式資料表。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: b135baf73e21cd524b6e8fad35452362f36cf0c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079543"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>教學課程：使用適用於 PostgreSQL 的 Azure 資料庫 – 超大規模 (Citus) (預覽) 設計多租用戶資料庫

在本教學課程中，您將使用適用於 PostgreSQL 的 Azure 資料庫 – 超大規模 (Citus) (預覽) 了解如何：

> [!div class="checklist"]
> * 建立超大規模 (Citus) 伺服器群組
> * 使用 psql 公用程式建立結構描述
> * 進行跨節點的資料表分區
> * 內嵌範例資料
> * 查詢租用戶資料
> * 在租用戶之間共用資料
> * 自訂每一租用戶的結構描述

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
   - 伺服器群組名稱：輸入新伺服器群組的唯一名稱，該名稱也會用於伺服器子網域。
   - 管理員使用者名稱：輸入唯一的使用者名稱 (稍後將用來連線到資料庫)。
   - 密碼：長度必須至少有 8 個字元，且必須包含下列三種類別的字元 – 英文大寫字母、英文小寫字母、數字 (0-9) 和非英數字元 (!、$、#、% 等)。
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

2. 在資料清單中尋找部署的調整群組，然後按一下該群組。 (其名稱前面會加上 "sg-"。)

3. 按一下左側功能表中 [安全性] 下方的 [防火牆]。

4. 按一下[+ 為目前的用戶端 IP 位址新增防火牆規則] 連結。 最後，按一下 [儲存] 按鈕。

5. 按一下 [檔案] 。

   > [!NOTE]
   > Azure PostgreSQL 伺服器會透過連接埠 5432 進行通訊。 如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 5432 的輸出流量。 若情況如此，除非 IT 部門開啟連接埠 5432，否則您無法連線至 Azure SQL Database 伺服器。
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>在 Cloud Shell 中使用 psql 連線到資料庫

現在，我們將使用 [psql](https://www.postgresql.org/docs/current/app-psql.html) 命令列公用程式來連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器。
1. 透過頂端瀏覽窗格上的終端機圖示啟動 Azure Cloud Shell。

   ![適用於 PostgreSQL 的 Azure 資料庫 - Azure Cloud Shell 終端機圖示](./media/tutorial-design-database-hyperscale-multi-tenant/psql-cloud-shell.png)

2. Azure Cloud Shell 會在您的瀏覽器中開啟，讓您能夠輸入 bash 命令。

   ![適用於 PostgreSQL 的 Azure 資料庫 - Azure Shell Bash 提示字元](./media/tutorial-design-database-hyperscale-multi-tenant/psql-bash.png)

3. 在 Cloud Shell 提示字元處，使用 psql 命令來連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器。 下列格式可用來透過 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 公用程式連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器：
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   例如，下列命令會使用存取認證，連線到 PostgreSQL 伺服器 **mydemoserver.postgres.database.azure.com** 上名為 **citus** 的預設資料庫。 在系統提示時輸入您的伺服器管理員密碼。

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>使用 psql 公用程式建立結構描述

使用 psql 連線到適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus) (預覽) 後，您可以完成一些基本工作。 本教學課程會引導您建立可讓廣告商追蹤其行銷活動的 Web 應用程式。

此應用程式可供多家公司使用，因此我們將建立一個資料表來保存公司，並且為其行銷活動建立另一個資料表。 在 psql 主控台中，執行下列命令：

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

每個行銷活動都會付費以執行廣告。 完成上述程式碼後也請在 psql 中執行下列程式碼，以新增廣告的資料表：

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

最後，我們會追蹤每個廣告的點擊率和廣告曝光數的相關統計資料：

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

現在，您可以在 psql 中執行下列命令，以查看在資料表清單中新建的資料表：

```postgres
\dt
```

多租用戶應用程式只能就個別的租用戶強制執行唯一性，這正是所有主要和外部索引鍵都包含公司識別碼的原因。

## <a name="shard-tables-across-nodes"></a>進行跨節點的資料表分區

超大規模部署會根據使用者指定的資料行值，在不同的節點上儲存資料表資料列。 這個「散發資料行」標記會標示哪個租用戶擁有哪些資料列。

我們要將散發資料行設定為 company\_id，即租用戶識別碼。 在 psql 中，執行下列函式：

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>內嵌範例資料

現在，在 psql 外部的一般命令列中，下載範例資料集：

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

回到 psql 內，進行資料的大量載入。 請務必在您下載資料檔案的相同目錄中執行 psql。

```sql
\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

這項資料現在會分散至背景工作節點。

## <a name="query-tenant-data"></a>查詢租用戶資料

當應用程式要求單一租用戶的資料時，資料庫可在單一背景工作節點上執行查詢。 單一租用戶查詢會依單一租用戶識別碼進行篩選。 例如，下列查詢會篩選 `company_id = 5` 的廣告和曝光數。 請嘗試在 psql 中執行該查詢，並查看結果。

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>在租用戶之間共用資料

到目前為止，所有資料表都已藉由 `company_id` 散發，但某些資料並非原本就特別「屬於」任何租用戶，而可以共用。 例如，範例廣告平台中的所有公司可能都會想要根據 IP 位址取得其對象的地理資訊。

請建立資料表來保存共用的地理資訊。 請在 psql 中執行此作業：

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

接著，使 `geo_ips` 成為「參考資料表」，用以儲存每個背景工作節點上的資料表複本。

```sql
SELECT create_reference_table('geo_ips');
```

使用範例資料加以載入。 請務必從您下載資料集的目錄內執行此 psql。

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

在所有節點上，聯結點擊率資料表與 geo\_ips 都有其效用。
以下聯結可用來尋找每個點擊廣告的使用者所在的位置
290. 請嘗試在 psql 中執行查詢。

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>自訂每一租用戶的結構描述

每個租用戶都可能需要儲存其他人不需要的特殊資訊。 不過，所有租用戶會共用具有相同資料庫結構描述的通用基礎結構。 其他資料的去處為何？

方法之一是使用開放式資料行類型，例如 PostgreSQL 的 JSONB。  我們的結構描述在 `clicks` 中有名為 `user_data` 的 JSONB 欄位。
一家公司 (例如公司五) 可使用資料行來追蹤使用者是否位於行動裝置上。

以下查詢可找出哪些人點擊了更多項目：行動裝置或傳統訪客。

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

我們也可以建立[部分索引](https://www.postgresql.org/docs/current/static/indexes-partial.html)，為單一公司最佳化此查詢。

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

在更廣泛的層面，我們可以建立資料行中每個索引鍵和值的 [GIN 索引](https://www.postgresql.org/docs/current/static/gin-intro.html)。

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在伺服器群組中建立了 Azure 資源。 如果您認為未來不需要這些資源，請刪除伺服器群組。 在您伺服器群組的 [概觀] 頁面中，按一下 [刪除] 按鈕。 當快顯頁面上出現提示時，請確認伺服器群組的名稱，然後按一下最後一個 [刪除] 按鈕。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何佈建超大規模 (Citus) 伺服器群組。 您已使用 psql 連線到該群組、建立結構描述，並散發資料。 您已了解如何在租用戶內部和之間查詢資料，以及如何自訂每個租用戶的結構描述。

接下來，請了解超大規模的概念。
> [!div class="nextstepaction"]
> [超大規模節點類型](https://aka.ms/hyperscale-concepts)
