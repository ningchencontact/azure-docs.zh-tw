---
title: 將 Apache Spark 連線至 Azure Cosmos DB
description: 了解可讓您將 Apache Spark 連線到 Azure Cosmos DB 的 Azure Cosmos DB Spark 連接器。
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: ramkris
ms.openlocfilehash: 60afd8128224050d456699e798d814a259e106ae
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543816"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>使用 Apache Spark 至 Azure Cosmos DB 連接器來加速巨量資料分析

您可以執行[Spark](https://spark.apache.org/)使用 Cosmos DB Spark 連接器的 Azure Cosmos DB 中儲存的資料作業。 Cosmos 可用服務層的批次和串流處理，以及以低延遲存取。

您可以使用的連接器[Azure Databricks](https://azure.microsoft.com/services/databricks)或是[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)，在 Azure 上提供受控的 Spark 叢集。 下表顯示支援的 Spark 版本。

| 元件 | 版本 |
|---------|-------|
| Apache Spark | 2.4.x、 2.3.x、 2.2.x 版本和 2.1.x |
| Scala | 2.11 |
| Azure Databricks 執行階段版本 | > 3.4 |

> [!WARNING]
> 此連接器支援 Azure Cosmos DB 的核心 (SQL) 的 API。
> 適用於 MongoDB API 的 Cosmos DB，使用[MongoDB Spark 連接器](https://docs.mongodb.com/spark-connector/master/)。
> 適用於 Cosmos DB Cassandra API，使用[Cassandra Spark 連接器](https://github.com/datastax/spark-cassandra-connector)。
>

## <a name="quickstart"></a>快速入門

* 請遵循的步驟[開始使用 Java SDK](sql-api-async-java-get-started.md)設定 Cosmos DB 帳戶，並填入一些資料。
* 請遵循的步驟[開始使用 Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html)設定 Azure Databricks 工作區和叢集。
* 您現在可以建立新的 Notebook，並匯入 Cosmos DB 連接器程式庫。 跳至[使用 Cosmos DB 」 連接器](#bk_working_with_connector)如需有關如何設定您的工作區。
* 下一節會對如何讀取及寫入使用連接器中的程式碼片段。

### <a name="reading-from-cosmos-db"></a>從 Cosmos DB 讀取

下列程式碼片段示範如何建立以讀取在 PySpark 中的 Cosmos DB 的 Spark 資料框架。

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

並以 Scala 相同的程式碼片段：

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="writing-to-cosmos-db"></a>寫入 Cosmos DB

下列程式碼片段示範如何寫入在 PySpark 中的 Cosmos DB 中的資料框架。

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

並以 Scala 相同的程式碼片段：

```scala
// Configure connection to the sink collection
val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Upsert the dataframe to Cosmos DB
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

多個程式碼片段和端對端範例，請參閱[Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks)。

## <a name="bk_working_with_connector"></a> 使用連接器

您可以建置連接器，在 Github 中，來源，或從 Maven 下載 uber jar，以下連結中。

| Spark | Scala | 最新版本 |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.3.5](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>使用 Databricks notebook

建立使用 Databricks 工作區的指導方針，Azure Databricks 指南中的程式庫 >[使用 Azure Cosmos DB Spark 連接器](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> 請注意，**使用 Azure Cosmos DB Spark 連接器**頁面目前不是最新狀態。 而不是下載成六個不同的程式庫的六個不同的 jar，您可以從 maven 在下載 uber jar https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar)及安裝此一 jar/程式庫。
> 

### <a name="using-spark-cli"></a>使用 spark-cli

若要搭配使用 spark cli 的連接器 (也就是`spark-shell`， `pyspark`， `spark-submit`)，您可以使用`--packages`參數使用的連接器[maven 座標](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)。

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5"

```

### <a name="using-jupyter-notebooks"></a>使用 Jupyter notebook

如果您使用 HDInsight 中的 Jupyter notebook，您可以使用 spark magic`%%configure`資料格，即可指定連接器的 maven 座標。

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> 請注意，包含`spark.jars.excludes`特有移除連接器、 Apache Spark 與 Livy 之間的潛在衝突。

### <a name="build-the-connector"></a>建置連接器

目前，此連接器專案會使用`maven`因此若要建置沒有相依性，您可以執行：

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>使用我們的範例

[Cosmos DB Spark GitHub 存放庫](https://github.com/Azure/azure-cosmosdb-spark)具有下列 notebook 範例和指令碼，您可以嘗試。

* **航班準點率 Spark 與 Cosmos DB （西雅圖） 與** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html):Spark 連線到 Cosmos DB 使用 HDInsight 的 Jupyter notebook 服務，來展示支援 Spark SQL、 GraphFrames 和使用 ML 管線的預測航班誤點。
* **[連接 Spark 與 Cosmos DB 變更摘要](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark%2Band%2BCosmos%2BDB%2BChange%2BFeed.ipynb)**:如何將 Spark 連線到 Cosmos DB 變更摘要快速展示。
* **Twitter Apache Spark 和 Azure Cosmos DB 變更摘要的來源**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **使用 Apache Spark 至 Cosmos DB 圖形查詢**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[連接到 Azure Cosmos DB 的 Azure Databricks](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** 使用`azure-cosmosdb-spark`。  以下連結也是 Azure Databricks 新版[航班準點率 notebook](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)。
* **[Lambda 架構使用 Azure Cosmos DB 和 HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**:您可以減少維護使用 Cosmos DB 與 Spark 的巨量資料管線的作業額外負荷。

## <a name="more-information"></a>相關資訊

我們有了更多資訊`azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki)包括：

* [Azure Cosmos DB Spark 連接器使用者指南](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [彙總範例](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>組態和設定

* [Spark 連接器設定](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [「 Spark 至 Cosmos DB 連接器設定](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup)（以進度）
* [設定 Power BI 直接查詢到 Azure Cosmos DB 透過 Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>疑難排解

* [使用 Cosmos DB 的彙總](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [已知問題](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>效能

* [效能秘訣](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [查詢測試回合](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [寫入測試回合](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>變更摘要

* [使用 Azure Cosmos DB 變更摘要和 Apache Spark 的 Stream 處理變更](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [變更摘要的示範](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [結構化的 Stream 示範](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>監視

* [使用 application insights 監視的 Spark 作業](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>後續步驟

如果您還沒這麼做，請從 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 存放庫下載 Spark 至 Azure Cosmos DB 連接器。 探索存放庫中的下列額外資源：

* [彙總範例](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [指令碼和 Notebook 範例](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples) (英文)

您也可以檢閱 [Apache Spark SQL、DataFrame 和 Dataset 指南](https://spark.apache.org/docs/latest/sql-programming-guide.html) (英文) 和 [Azure HDInsight 上的 Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) 文章。
