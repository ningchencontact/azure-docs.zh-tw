---
title: 使用適用于 Apache Spark 的 Azure 資料總管連接器，在 Azure 資料總管和 Spark 叢集之間移動資料。
description: 本主題說明如何在 Azure 資料總管和 Apache Spark 叢集之間移動資料。
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 6a95cbad161906bd12a608880ac694d6bdf1ed27
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383058"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>適用于 Apache Spark 的 Azure 資料總管連接器（預覽）

[Apache Spark](https://spark.apache.org/)是適用于大規模資料處理的整合分析引擎。 Azure 資料總管是快速、完全受控的資料分析服務，可即時分析大量資料。 

適用于 Spark 的 Azure 資料總管連接器會執行資料來源和資料接收，以便在 Azure 資料總管和 Spark 叢集之間移動資料，以使用這兩者的功能。 您可以使用 Azure 資料總管和 Apache Spark，建置快速、可調整、以資料導向的案例為目標的應用程式，例如機器學習 (ML)、擷取-轉換-載入 (ETL) 和 Log Analytics。 寫入 Azure 資料總管可在批次和串流模式中完成。
從 Azure 資料總管讀取支援「資料行剪除」和「述詞下推」，藉由在 Azure 資料總管中篩選出資料來減少傳輸的資料量。

Azure 資料總管 Spark 連接器是可在任何 Spark 叢集上執行的[開放原始碼專案](https://github.com/Azure/azure-kusto-spark)。 Azure 資料總管 Spark 連接器可讓 Azure 資料總管有效的資料存放區，供標準 Spark 來源和接收作業（例如 write、read 和 Writestream.format）使用。 

> [!NOTE]
> 雖然以下的部分範例參考[Azure Databricks](https://docs.azuredatabricks.net/) Spark 叢集，但 Azure 資料總管 Spark 連接器並不會直接相依于 Databricks 或任何其他 spark 散發。

## <a name="prerequisites"></a>必要條件

* [建立 Azure 資料總管叢集與資料庫](/azure/data-explorer/create-cluster-database-portal) 
* 建立 Spark 叢集
* 安裝 Azure 資料總管連接器程式庫和相依性（[包括下列](https://github.com/Azure/azure-kusto-spark#dependencies) [Kusto JAVA SDK](/azure/kusto/api/java/kusto-java-client-library)程式庫）中所列的程式庫：
    * [Kusto 資料用戶端](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto 內嵌用戶端](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* [Spark 2.4、Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)的預先建立程式庫

## <a name="how-to-build-the-spark-connector"></a>如何建立 Spark 連接器

Spark 連接器可以從[來源](https://github.com/Azure/azure-kusto-spark)建立，如下所述。

> [!NOTE]
> 此為選用步驟。 如果您使用的是預先建立的程式庫，請移至[Spark 叢集設定](#spark-cluster-setup)。

### <a name="build-prerequisites"></a>組建必要條件

* 已安裝 JAVA 1.8 SDK
* 已安裝[Maven](https://maven.apache.org/download.cgi) 3。x
* Apache Spark 2.4.0 或更高版本

> [!TIP]
> 2.3. x 版本也受到支援，但可能需要在 pom .xml 相依性中進行一些變更。

針對使用 Maven 專案定義的 Scala/JAVA 應用程式，請將您的應用程式與下列成品連結（最新版本可能不同）：

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>建置命令

若要建置 jar 並執行所有測試：

```
mvn clean package
```

若要建立 jar、執行所有測試，並將 jar 安裝到您的本機 Maven 存放庫：

```
mvn clean install
```

如需詳細資訊，請參閱[連接器使用](https://github.com/Azure/azure-kusto-spark#usage)方式。

## <a name="spark-cluster-setup"></a>Spark 叢集設定

> [!NOTE]
> 建議您在執行下列步驟時使用最新的 Azure 資料總管 Spark 連接器版本：

1. 根據使用 Spark 2.4 和 Scala 2.11 的 Azure Databricks 叢集，設定下列 Spark 叢集設定： 

    ![Databricks 叢集設定](media/spark-connector/databricks-cluster.png)

1. 匯入 Azure 資料總管連接器程式庫：

    ![匯入 Azure 資料總管程式庫](media/spark-connector/db-create-library.png)

1. 新增其他相依性（如果是從 maven 使用，則不需要）：

    ![新增相依性](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > 在[這裡](https://github.com/Azure/azure-kusto-spark#dependencies)可找到每個 Spark 版本的正確 java 發行版本。

1. 確認已安裝所有必要的程式庫：

    ![確認已安裝程式庫](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>驗證

Azure 資料總管 Spark 連接器可讓您使用[Azure AD 應用程式](#azure-ad-application-authentication)、 [Azure AD 存取權杖](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)、[裝置驗證](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication)（適用于非生產案例）或[Azure 金鑰，向 Azure Active Directory （Azure AD）進行驗證保存庫](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault)。 使用者必須安裝 azure keyvault 套件並提供應用程式認證，才能存取 Key Vault 資源。

### <a name="azure-ad-application-authentication"></a>Azure AD 應用程式驗證

最簡單且常見的驗證方法。 建議將此方法用於 Azure 資料總管 Spark 連接器使用方式。

|屬性  |描述  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD 應用程式（用戶端）識別碼。      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD 的驗證授權單位。 Azure AD Directory （租使用者）識別碼。        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Azure AD 用戶端的應用程式金鑰。     |

### <a name="azure-data-explorer-privileges"></a>Azure 資料總管許可權

必須在 Azure 資料總管叢集上授與下列許可權：

* 若要讀取（資料來源），Azure AD 應用程式必須具有目標資料庫的*檢視器*許可權，或目標資料表上的系統*管理員*許可權。
* 對於寫入（資料接收），Azure AD 應用程式必須在目標資料庫上具有*擷取器*許可權。 它也必須擁有目標資料庫的*使用者*權力，才能建立新的資料表。 如果目標資料表已經存在，則可以設定目標資料表上的系統*管理員*許可權。
 
如需 Azure 資料總管主體角色的詳細資訊，請參閱以[角色為基礎的授權](/azure/kusto/management/access-control/role-based-authorization)。 如需管理安全性角色，請參閱[安全性角色管理](/azure/kusto/management/security-roles)。

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark 接收：寫入 Azure 資料總管

1. 設定接收參數：

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. 將 Spark 資料框架寫入 Azure 資料總管叢集作為批次：

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    val conf = Map(
            KustoSinkOptions.KUSTO_CLUSTER -> cluster,
            KustoSinkOptions.KUSTO_TABLE -> table,
            KustoSinkOptions.KUSTO_DATABASE -> database,
            KustoSinkOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId)
    
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .options(conf)
      .save()
      
    ```
    
   或使用簡化的語法：
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. 寫入串流資料：

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark 來源：從 Azure 資料總管讀取

1. 讀取少量資料時，請定義資料查詢：

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. 讀取大量資料時，必須提供暫時性 blob 儲存體。 提供儲存體容器 SAS 金鑰，或儲存體帳戶名稱、帳戶金鑰和容器名稱。 只有目前的 Spark 連接器預覽版本才需要此步驟。

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    在上述範例中，我們不會使用連接器介面來存取 Key Vault。 或者，我們使用較簡單的方法來使用 Databricks 秘密。

1. 從 Azure 資料總管讀取：

    ```scala
     val conf3 = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
          KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
