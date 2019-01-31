---
title: 教學課程：使用 Azure HDInsight 上的 Apache Hive 來執行擷取、轉換、載入 (ETL) 作業
description: 在本教學課程中，您將了解如何從原始 CSV 資料集擷取資料、使用 Azure HDInsight 上的 Apache Hive 加以轉換，然後使用 Sqoop 將已轉換的資料載入 Azure SQL Database 中。
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jamesbak
ms.openlocfilehash: 70ad37aa0ccbab762aa6e5cfb05d385e8b2a86ee
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244006"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>教學課程：使用 Azure HDInsight 上的 Apache Hive 來擷取、轉換和載入資料

在本教學課程中，您將會執行 ETL 作業：擷取、轉換及載入資料。 您會取用原始 CSV 資料檔案，並將其匯入 Azure HDInsight 叢集中，然後使用 Apache Hive 加以轉換，再使用 Apache Sqoop 將其載入 Azure SQL Database 中。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 擷取資料並將其上傳至 HDInsight 叢集。
> * 使用 Apache Hive 轉換資料。
> * 使用 Sqoop 將資料載入至 Azure SQL Database。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

* **HDInsight 上的 Linux 型 Hadoop 叢集**：若要建立新的 Linux 型 HDInsight 叢集，請參閱[使用 Hadoop、Spark 及 Kafka 等工具在 HDInsight 中設定叢集](./data-lake-storage-quickstart-create-connect-hdi-cluster.md)。

* **Azure SQL Database**：您會使用 Azure SQL Database 做為目的地資料存放區。 如果您沒有 SQL 資料庫，請參閱[在 Azure 入口網站中建立 Azure SQL Database](../../sql-database/sql-database-get-started.md)。

* **Azure CLI**：如果您未安裝 Azure CLI，請參閱 [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

* **SSH 用戶端**：如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Hadoop)](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

> [!IMPORTANT]
> 此文中的步驟需要使用 Linux 的 HDInsight 叢集。 Linux 是 Azure HDInsight 版本 3.4 或更新版本唯一使用的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 淘汰](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement)。

### <a name="download-the-flight-data"></a>下載航班資料

本教學課程將使用來自運輸統計處的航班資料示範如何執行 ETL 作業。 您必須下載這項資料，才能完成本教學課程。

1. 移至[創新技術研究管理部運輸統計處](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)。

1. 在此頁面上選取下列值：

   | Name | 值 |
   | --- | --- |
   | **篩選年份** |2013 |
   | **篩選期間** |一月 |
   | **欄位** |Year、FlightDate、UniqueCarrier、Carrier、FlightNum、OriginAirportID、Origin、OriginCityName、OriginState、DestAirportID、Dest、DestCityName、DestState、DepDelayMinutes、ArrDelay、ArrDelayMinutes、CarrierDelay、WeatherDelay、NASDelay、SecurityDelay、LateAircraftDelay。 |

1. 清除所有其他欄位。

1. 選取 [下載]。 您會取得含有您所選資料欄位的 .zip 檔案。

## <a name="extract-and-upload-the-data"></a>擷取並上傳資料

在本節中，您會使用 `scp` 將資料上傳至 HDInsight 叢集。

開啟命令提示字元，並使用下列命令將 .zip 檔案上傳至 HDInsight 叢集前端節點：

```bash
scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
```

* 將 \<FILE_NAME> 取代為 .zip 檔案的名稱。
* 將 \<SSH_USER_NAME> 取代為 HDInsight 叢集的 SSH 登入。
* 將 \<CLUSTER_NAME> 取代為 HDInsight 叢集的名稱。

如果您使用密碼來驗證您的 SSH 登入，系統會提示您輸入密碼。 

如果您使用的是公用金鑰，您可能必須使用 `-i` 參數並指定對應的私密金鑰路徑。 例如： `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`。

完成上傳之後，使用 SSH 連線至叢集。 在命令提示字元中輸入下列命令：

```bash
ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
```

使用以下命令解壓縮 .zip 檔案：

```bash
unzip <FILE_NAME>.zip
```

此命令會解壓縮一個 .csv 檔案 (約為 60 MB)。

使用下列命令建立目錄，然後將 .csv 檔案複製到該目錄︰

```bash
hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
```

使用下列命令建立 Data Lake Storage Gen2 檔案系統。

```bash
hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
```

## <a name="transform-the-data"></a>轉換資料

在本節中，您會使用 Beeline 來執行 Apache Hive 作業。

在執行 Apache Hive 作業的過程中，您會將 .csv 檔案中的資料匯入至名為 **delays** 的 Apache Hive 資料表。

從 HDInsight 叢集既有的 SSH 提示字元中，使用下列命令建立並編輯名為 **flightdelays.hql** 的新檔案：

```bash
nano flightdelays.hql
```

使用下列文字做為此檔案的內容：

```hiveql
 DROP TABLE delays_raw;
 -- Creates an external table over the csv file
 CREATE EXTERNAL TABLE delays_raw (
    YEAR string,
    FL_DATE string,
    UNIQUE_CARRIER string,
    CARRIER string,
    FL_NUM string,
    ORIGIN_AIRPORT_ID string,
    ORIGIN string,
    ORIGIN_CITY_NAME string,
    ORIGIN_CITY_NAME_TEMP string,
    ORIGIN_STATE_ABR string,
    DEST_AIRPORT_ID string,
    DEST string,
    DEST_CITY_NAME string,
    DEST_CITY_NAME_TEMP string,
    DEST_STATE_ABR string,
    DEP_DELAY_NEW float,
    ARR_DELAY_NEW float,
    CARRIER_DELAY float,
    WEATHER_DELAY float,
    NAS_DELAY float,
    SECURITY_DELAY float,
    LATE_AIRCRAFT_DELAY float)
 -- The following lines describe the format and location of the file
 ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
 LINES TERMINATED BY '\n'
 STORED AS TEXTFILE
 LOCATION 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data';

-- Drop the delays table if it exists
DROP TABLE delays;
-- Create the delays table and populate it with data
-- pulled in from the CSV file (via the external table defined previously)
CREATE TABLE delays
LOCATION abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/processed
AS
SELECT YEAR AS year,
    FL_DATE AS flight_date,
    substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
    substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
    substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
    ORIGIN_AIRPORT_ID AS origin_airport_id,
    substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
    substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
    substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
    DEST_AIRPORT_ID AS dest_airport_id,
    substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
    substring(DEST_CITY_NAME,2) AS dest_city_name,
    substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
    DEP_DELAY_NEW AS dep_delay_new,
    ARR_DELAY_NEW AS arr_delay_new,
    CARRIER_DELAY AS carrier_delay,
    WEATHER_DELAY AS weather_delay,
    NAS_DELAY AS nas_delay,
    SECURITY_DELAY AS security_delay,
    LATE_AIRCRAFT_DELAY AS late_aircraft_delay
FROM delays_raw;
```

若要儲存檔案，請選取 Esc 鍵，然後輸入 `:x`。

若要啟動 Hive 並執行 **flightdelays.hql** 檔案，請使用下列命令：

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
```

在 __flightdelays.hql__ 指令碼執行完畢之後，請使用下列命令來開啟互動式 Beeline 工作階段：

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
```

當您收到 `jdbc:hive2://localhost:10001/>` 提示字元時，請使用以下查詢從匯入的航班延誤資料中擷取資料：

```hiveql
INSERT OVERWRITE DIRECTORY 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output'
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
SELECT regexp_replace(origin_city_name, '''', ''),
    avg(weather_delay)
FROM delays
WHERE weather_delay IS NOT NULL
GROUP BY origin_city_name;
```

此查詢會擷取因氣候因素而延誤的城市清單，以及平均延誤時間，並會儲存到 `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`。 稍後，Sqoop 會從此位置讀取該資料，並匯出到 Azure SQL Database。

若要結束 Beeline，請在提示字元中輸入 `!quit` 。

## <a name="create-a-sql-database-table"></a>建立 SQL 資料庫資料表

執行這項作業時，需要您在 SQL 資料庫中的伺服器名稱。 請完成下列步驟以找出您的伺服器名稱。

1. 移至 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [SQL 資料庫]。

1. 篩選您選擇要使用的資料庫名稱。 伺服器名稱會列在 [伺服器名稱] 資料行中。

1. 篩選您要使用的資料庫名稱。 伺服器名稱會列在 [伺服器名稱] 資料行中。

    ![取得 Azure SQL 伺服器詳細資料](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "取得 Azure SQL 伺服器詳細資料")

    連接至 SQL Database 並建立資料表的方法有很多種。 下列步驟會從 HDInsight 叢集使用 [FreeTDS](http://www.freetds.org/) 。

若要安裝 FreeTDS，請從 SSH 連線對叢集使用下列命令：

```bash
sudo apt-get --assume-yes install freetds-dev freetds-bin
```

安裝完成後，請使用下列命令連線至 SQL 資料庫伺服器。

* 將 \<SERVER_NAME> 取代為 SQL 資料庫伺服器名稱。
* 將 \<ADMIN_LOGIN> 取代為 SQL 資料庫的管理員登入資料。
* 將 \<DATABASE_NAME> 取代為資料庫名稱。

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
```

出現提示時，請輸入 SQL 資料庫管理員的登入密碼。

您會收到如以下文字的輸出：

```
locale is "en_US.UTF-8"
locale charset is "UTF-8"
using default charset "UTF-8"
Default database being set to sqooptest
1>
```

出現 `1>` 提示時，請輸入下列陳述式：

```hiveql
CREATE TABLE [dbo].[delays](
[origin_city_name] [nvarchar](50) NOT NULL,
[weather_delay] float,
CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
([origin_city_name] ASC))
GO
```

輸入 `GO` 陳述式後，將評估先前的陳述式。
此查詢會建立名為 **delays** 的資料表 (具有叢集索引)。

使用下列查詢來確認資料表已建立：

```hiveql
SELECT * FROM information_schema.tables
GO
```

輸出大致如下：

```
TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
databaseName       dbo             delays        BASE TABLE
```

Enter `exit` at the `1>` 以結束 tsql 公用程式。

## <a name="export-and-load-the-data"></a>匯出和載入資料

在前幾節中，您在位置 `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` 上複製了已轉換的資料。 在本節中，您將使用 Sqoop 將資料從 `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` 匯出至您在 Azure SQL Database 中建立的資料表。

使用下列命令以確認 Sqoop 看得見您的 SQL 資料庫：

```bash
sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
```

此命令會傳回一份資料庫清單，包括您在其中建立 **delays** 資料表的資料庫。

使用下列命令，將資料從 **hivesampletable** 資料表匯出至 **delays** 資料表：

```bash
sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
```

Sqoop 會連線至包含 **delays** 資料表的資料庫，並將資料從 `/tutorials/flightdelays/output` 目錄匯出至 **delays** 資料表。

在 `sqoop` 命令完成後，請使用 tsql 公用程式連線至資料庫：

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
```

使用下列陳述式來確認資料已匯出到 **delays** 資料表：

```sql
SELECT * FROM delays
GO
```

您應會看到資料表中的資料清單。 此資料表包含城市名稱以及該城市的平均航班延誤時間。

輸入 `exit` 以結束 tsql 公用程式。

## <a name="clean-up-resources"></a>清除資源

本教學課程中使用的所有資源都是既有的。 不需要執行清除。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用 HDInsight 中的資料，請參閱下列文章：

> [!div class="nextstepaction"]
> [使用 Azure Databrick 擷取、轉換和載入資料](./data-lake-storage-use-hdi-cluster.md)
