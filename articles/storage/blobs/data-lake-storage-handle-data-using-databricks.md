---
title: 教學課程：了解如何使用 Azure Databricks 執行擷取、載入和傳輸作業
description: 在本教學課程中，您將了解如何將資料從 Azure Data Lake Storage Gen2 預覽版擷取至 Azure Databricks 並轉換資料，然後將資料載入 Azure SQL 資料倉儲中。
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 12/06/2018
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 6b2812e31174c4e5d61ae9941563e39357de9522
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107084"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>教學課程：使用 Azure Databrick 擷取、轉換和載入資料

在本教學課程中，您將使用 Azure Databricks 執行 ETL (擷取、轉換及載入資料) 作業。 您會將資料從已啟用 Azure Data Lake Storage Gen2 的 Azure 儲存體帳戶移至 Azure SQL 資料倉儲。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立 Azure Databricks 工作區。
> * 在 Azure Databricks 中建立 Spark 叢集。
> * 建立具有 Azure Data Lake Storage Gen2 功能的帳戶。
> * 將資料上傳至 Azure Data Lake Storage Gen2。
> * 在 Azure Databricks 中建立 Notebook。
> * 從 Data Lake Storage Gen2 擷取資料。
> * 轉換 Azure Databricks 中的資料。
> * 將資料載入到 Azure SQL 資料倉儲。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

* 建立 Azure SQL 資料倉儲、建立伺服器層級的防火牆規則，並以伺服器管理員的身分連線至伺服器。依照[快速入門：建立 Azure SQL 資料倉儲](../../sql-data-warehouse/create-data-warehouse-portal.md)一文中的指示操作。
* 建立 Azure SQL 資料倉儲的資料庫主要金鑰。 請依照[建立資料庫主要金鑰](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key)一文中的指示操作。
* [建立 Azure Data Lake Storage Gen2 帳戶](data-lake-storage-quickstart-create-account.md)。
* 從 [U-SQL 範例和問題追蹤](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json)存放庫下載 (**small_radio_json.json**)，並記下檔案的儲存路徑。
* 登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-the-workspace"></a>建立工作區

在本節中，您將使用 Azure 入口網站建立 Azure Databricks 工作區。

1. 在 Azure 入口網站中，選取 [建立資源] > [分析] > [Azure Databricks]。

    ![Azure 入口網站上的 Databricks](./media/data-lake-storage-handle-data-using-databricks/azure-databricks-on-portal.png "Azure 入口網站上的 Databricks")

1. 在 [Azure Databricks 服務] 下方提供下列值，以建立 Databricks 工作區：

    |屬性  |說明  |
    |---------|---------|
    |**工作區名稱**     | 提供您 Databricks 工作區的名稱。        |
    |**訂用帳戶**     | 從下拉式清單中選取您的 Azure 訂用帳戶。        |
    |**資源群組**     | 指定您是要建立新的資源群組，還是使用現有資源群組。 資源群組是存放 Azure 方案相關資源的容器。 如需詳細資訊，請參閱 [Azure 資源群組概觀](../../azure-resource-manager/resource-group-overview.md)。 |
    |**位置**     | 選取 [美國西部 2]。        |
    |定價層     |  選取 [標準]。     |

    ![建立 Azure Databricks 工作區](./media/data-lake-storage-handle-data-using-databricks/create-databricks-workspace.png "建立 Azure Databricks 工作區")

1. 選取 [釘選到儀表板]，然後選取 [建立]。

1. 建立帳戶需要幾分鐘的時間。 建立帳戶期間，入口網站右側會顯示 [提交 Azure Databricks 部署] 圖格。 若要監視作業狀態，請檢視頂端的進度列。

    ![Databricks 部署圖格](./media/data-lake-storage-handle-data-using-databricks/databricks-deployment-tile.png "Databricks 部署圖格")

## <a name="create-the-spark-cluster"></a>建立 Spark 叢集

若要執行本教學課程中的作業，您需要 Spark 叢集。 使用下列步驟建立 Spark 叢集。

1. 在 Azure 入口網站中，移至您所建立的 Databricks 工作區，然後選取 [啟動工作區]。

1. 系統會將您重新導向至 Azure Databricks 入口網站。 在入口網站中選取 [叢集]。

    ![Azure 上的 Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Azure 上的 Databricks")

1. 在 [新增叢集] 頁面上，提供值以建立叢集。

    ![在 Azure 上建立 Databricks Spark 叢集](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "在 Azure 上建立 Databricks Spark 叢集")

1. 填寫下列欄位的值，然後接受其他欄位的預設值：

    * 輸入叢集的名稱。
    * 在本文中，請使用 **5.1** 執行階段建立叢集。
    * 確定您已選取 [在停止活動\_\_分鐘後終止] 核取方塊。 若未使用叢集，請提供據以終止叢集的持續時間 (以分鐘為單位)。

1. 選取 [建立叢集]。

叢集執行後，您就可以將 Notebook 連結至叢集，並執行 Spark 作業。

## <a name="create-a-file-system"></a>建立檔案系統

若要將資料儲存在 Data Lake Storage Gen2 儲存體帳戶中，您必須建立檔案系統。

首先，您必須在 Azure Databricks 工作區中建立 Notebook，然後執行程式碼片段，以在儲存體帳戶中建立檔案系統。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您所建立的 Azure Databricks 工作區，然後選取 [啟動工作區]。

1. 在左側選取 [工作區]。 從 [工作區] 下拉式清單選取 [建立] > [Notebook]。

    ![在 Databricks 中建立 Notebook](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "在 Databricks 中建立 Notebook")

1. 在 [建立 Notebook] 對話方塊中，輸入 Notebook 的名稱。 選取 [Scala] 作為語言，然後選取您先前建立的 Spark 叢集。

    ![為 Databricks 中的 Notebook 提供詳細資料](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "為 Databricks 中的 Notebook 提供詳細資料")

    選取 [建立] 。

1. 在第一個 Notebook 資料格中輸入下列程式碼，然後執行程式碼。 請以您自己的值取代範例中括號內顯示的預留位置：

    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
     
    dbutils.fs.mount(
        source = "abfss://<file-system-name>@<account-name>.dfs.core.windows.net/[<directory-name>]",
        mount_point = "/mnt/<mount-name>",
        extra_configs = configs)
    ```

1. 選取 Shift+Enter 鍵以執行程式碼。

現在，會為儲存體帳戶建立檔案系統。

## <a name="upload-the-sample-data"></a>上傳範例資料

下一個步驟是將範例資料檔案上傳至儲存體帳戶，以便稍後在 Azure Databricks 中進行轉換。

將您下載的範例資料上傳到儲存體帳戶中。 用來將資料上傳到儲存體帳戶的方法，會因為您是否已啟用階層命名空間而有所不同。

您可以使用 Azure Data Factory、distp 或 AzCopy (第 10 版) 來執行上傳。 AzCopy 第 10 版目前僅供預覽使用。 若要使用 AzCopy，請將下列程式碼貼到命令視窗中：

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
```

## <a name="extract-the-data"></a>擷取資料

若要使用 Databricks 中的範例資料，您必須從儲存體帳戶中擷取資料。

返回 Databricks Notebook，然後在 Notebook 的新資料格中輸入下列程式碼。

在空的程式碼資料格中新增下列程式碼片段。 請將括號內顯示的預留位置取代為您先前從儲存體帳戶儲存的值。

```scala
dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
```

選取 Shift+Enter 鍵以執行程式碼。

您現在可以將 json 檔案範例以資料框架的形式載入 Azure Databricks。 在新的資料格中貼上下列程式碼。 請將括號內顯示的預留位置取代為您自己的值。

```scala
val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
```

選取 Shift+Enter 鍵以執行程式碼。

執行下列程式碼，以查看資料框架的內容：

```scala
df.show()
```

您會看到如下列程式碼片段的輸出：

```bash
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
|               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
| El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
| Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
| Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
...
...
```

您現在已將資料從 Azure Data Lake Storage Gen2 擷取至 Azure Databricks。

## <a name="transform-the-data"></a>轉換資料

原始範例資料 **small_radio_json.json** 檔案擷取了廣播電台的聽眾，並且有不同資料行。 在本節中，您會將資料轉換為僅從資料集擷取特定資料行。

首先，您僅從已建立的資料框架中擷取 **firstName**、**lastName**、**gender**、**location** 和 **level** 資料行。

```scala
val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
```

您會取得如下列程式碼片段所示的輸出：

```bash
+---------+----------+------+--------------------+-----+
|firstname|  lastname|gender|            location|level|
+---------+----------+------+--------------------+-----+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
+---------+----------+------+--------------------+-----+
```

您可以進一步轉換此資料，將 **level** 資料行重新命名為 **subscription_type**。

```scala
val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
renamedColumnsDF.show()
```

您會取得如下列程式碼片段所示的輸出。

```bash
+---------+----------+------+--------------------+-----------------+
|firstname|  lastname|gender|            location|subscription_type|
+---------+----------+------+--------------------+-----------------+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
+---------+----------+------+--------------------+-----------------+
```

## <a name="load-the-data"></a>載入資料

在本節中，您會將已轉換的資料上傳至 Azure SQL 資料倉儲。 您可以使用適用於 Azure Databricks 的 Azure SQL 資料倉儲連接器，直接將資料框架以資料表形式上傳至 SQL 資料倉儲。

在 Azure Databricks 和 Azure SQL 資料倉儲之間上傳資料時，SQL 資料倉儲連接器會使用 Azure Blob 儲存體作為暫時儲存體。 因此，一開始您需提供要連線到儲存體帳戶的組態。 您必須已建立屬於本文必要條件的帳戶。

提供可從 Azure Databricks 存取 Azure 儲存體帳戶的組態。

```scala
val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
val fileSystemName = "<FILE_SYSTEM_NAME>"
val accessKey =  "<ACCESS_KEY>"
```

指定在 Azure Databricks 與 Azure SQL 資料倉儲之間移動資料時所要使用的暫存資料夾。

```scala
val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
```

執行下列程式碼片段，儲存組態中的 Azure Blob 儲存體存取金鑰。 此動作可確保您無須在 Notebook 中以純文字保留存取金鑰。

```scala
val acntInfo = "fs.azure.account.key."+ storageURI
sc.hadoopConfiguration.set(acntInfo, accessKey)
```

提供用來連線至 Azure SQL 資料倉儲執行個體的值。 您必須已建立屬於必要條件的 SQL 資料倉儲。

```scala
//SQL Data Warehouse related settings
val dwDatabase = "<DATABASE NAME>"
val dwServer = "<DATABASE SERVER NAME>" 
val dwUser = "<USER NAME>"
val dwPass = "<PASSWORD>"
val dwJdbcPort =  "1433"
val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
```

執行下列程式碼片段，將已轉換的資料框架 **renamedColumnsDF** 以資料表形式載入 SQL 資料倉儲中。 此程式碼片段會在 SQL 資料庫中建立名為 **SampleTable** 的資料表。

```scala
spark.conf.set(
    "spark.sql.parquet.writeLegacyFormat",
    "true")
    
renamedColumnsDF.write
    .format("com.databricks.spark.sqldw")
    .option("url", sqlDwUrlSmall) 
    .option("dbtable", "SampleTable")
    .option( "forward_spark_azure_storage_credentials","True")
    .option("tempdir", tempDir)
    .mode("overwrite")
    .save()
```

連線至 SQL 資料庫，並確認您看到名為 **SampleTable** 的資料庫。

![確認範例資料表](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "確認範例資料表")

執行 SELECT 查詢，以確認資料表的內容。 資料表中應包含與 **renamedColumnsDF** 資料框架相同的資料。

![確認範例資料表內容](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "確認範例資料表內容")

## <a name="clean-up-resources"></a>清除資源

完成本教學課程之後，您可以終止叢集。 請從 Azure Databricks 工作區的左側選取 [叢集]。 針對要終止的叢集，在 [動作] 下方指向省略符號 (...)，然後選取 [終止] 圖示。

![停止 Databricks 叢集](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "停止 Databricks 叢集")

如果您不手動終止叢集，叢集會自動停止，但前提是您已在建立叢集時選取 [在停止活動\_\_分鐘後終止] 核取方塊。 在這種情況下，叢集將會在停止運作達指定時間後自動停止。

## <a name="next-steps"></a>後續步驟

前往下一個教學課程，了解如何使用 Azure 事件中樞將即時資料串流到 Azure Databricks 中。

> [!div class="nextstepaction"]
>[使用事件中樞將資料串流至 Azure Databricks](../../azure-databricks/databricks-stream-from-eventhubs.md)
