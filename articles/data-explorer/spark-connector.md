---
title: 使用 Azure Data Explorer 連接器適用於 Apache Spark Azure 資料總管和 Spark 叢集之間移動資料。
description: 本主題說明如何以 Azure 資料總管和 Apache Spark 叢集之間移動資料。
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 854e29b67b6e24c583a98b5851bf17551cfcbf61
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441343"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Azure 資料總管連接器適用於 Apache Spark （預覽）

[Apache Spark](https://spark.apache.org/)進行大規模資料處理以統一的分析引擎。 Azure 的資料總管是快速且受到完整管理的資料分析服務來執行即時分析大量資料上。 

Azure 適用於 Spark 的資料總管連接器實作資料來源和跨 Azure 資料總管和 Spark 叢集，才能使用這兩個功能，將資料移動的資料接收器。 您可以使用 Azure 資料總管和 Apache Spark，建置快速、可調整、以資料導向的案例為目標的應用程式，例如機器學習 (ML)、擷取-轉換-載入 (ETL) 和 Log Analytics。 寫入至 Azure Data Explorer 可以完成批次和資料流處理模式中。
讀取 Azure 資料總管支援資料行剪除 」 與 「 述詞下推，篩選出 Azure 資料總管 中的資料時，減少傳送的資料數量。

Azure 資料總管 Spark 連接器[開放原始碼專案](https://github.com/Azure/azure-kusto-spark)，可以在任何 Spark 叢集上執行。

> [!NOTE]
> 雖然下列範例的一些參考[Azure Databricks](https://docs.azuredatabricks.net/) Spark 叢集時，Azure 資料總管 Spark 連接器不會在 Databricks 或任何其他 Spark 通訊的直接相依性。

## <a name="prerequisites"></a>必要條件

* [建立 Azure 資料總管叢集與資料庫](/azure/data-explorer/create-cluster-database-portal) 
* 建立 Spark 叢集
* 安裝 Azure 資料總管連接器程式庫和程式庫中列出[相依性](https://github.com/Azure/azure-kusto-spark#dependencies)包括下列[Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library)程式庫：
    * [Kusto 資料用戶端](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto 內嵌用戶端](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* 預先建置的程式庫[Spark 2.4，Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>如何建置 Spark 連接器

可以從建立 Spark 連接器[來源](https://github.com/Azure/azure-kusto-spark)如下所述。

> [!NOTE]
> 此為選用步驟。 如果您使用預先建置的程式庫移至[Spark 叢集設定](#spark-cluster-setup)。

### <a name="build-prerequisites"></a>建置的必要條件

* 安裝 Java 1.8 SDK
* [Maven 3.x](https://maven.apache.org/download.cgi)安裝
* Apache Spark 版本 2.4.0 為基礎或更高版本

> [!TIP]
> 2.3.x 版本也支援，但可能需要一些變更，在 pom.xml 相依性。

針對使用 Maven 專案定義的 Scala/Java 應用程式連結您的應用程式與下列成品 （最新版本可能不同）：

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

若要建置 jar，執行所有測試，並安裝本機 Maven 儲存機制的 jar:

```
mvn clean install
```

如需詳細資訊，請參閱 <<c0> [ 連接器使用量](https://github.com/Azure/azure-kusto-spark#usage)。

## <a name="spark-cluster-setup"></a>Spark 叢集設定

> [!NOTE]
> 建議使用最新的 Azure 資料總管 Spark 連接器版本，執行下列步驟：

1. 設定下列的 Spark 叢集設定，以使用 Spark 2.4 和 Scala 2.11 的 Azure Databricks 叢集： 

    ![Databricks 叢集設定](media/spark-connector/databricks-cluster.png)

1. 匯入 Azure 資料總管連接器程式庫：

    ![匯入 Azure 資料總管程式庫](media/spark-connector/db-create-library.png)

1. 新增其他相依性：

    ![新增相依性](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > 找到正確的 java 版本版本，每個 Spark 版本[此處](https://github.com/Azure/azure-kusto-spark#dependencies)。

1. 請確認所有必要的安裝程式庫：

    ![確認已安裝的程式庫](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Azure 資料總管 Spark 連接器可讓您使用 Azure Active Directory (Azure AD) 驗證[Azure AD 應用程式](#azure-ad-application-authentication)， [Azure AD 存取權杖](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)，[裝置驗證](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) （適用於非生產案例中），或是[Azure 金鑰保存庫](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault)。 使用者必須安裝 azure 金鑰保存庫的套件，並提供應用程式認證，才能存取金鑰保存庫資源。

### <a name="azure-ad-application-authentication"></a>Azure AD 應用程式驗證

大部分的簡單且常見的驗證方法。 Azure 資料總管 Spark 連接器使用方式建議使用這個方法。

|properties  |說明  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD 應用程式 （用戶端） 識別碼。      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD 驗證授權單位。 Azure AD 目錄 （租用戶） 識別碼。        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    用戶端的 azure AD 應用程式金鑰。     |

### <a name="azure-data-explorer-privileges"></a>Azure 資料總管權限

在 Azure 資料總管叢集上，必須被授與下列權限：

* Azure AD 應用程式必須有讀取 （資料來源），請*檢視器*目標資料庫上的權限或*管理員*目標資料表上的權限。
* 供寫入 （資料接收），Azure AD 應用程式必須有*擷取器*目標資料庫的權限。 它也必須擁有*使用者*目標資料庫，才能建立新的資料表上的權限。 如果目標資料表已存在， *admin*可以設定目標資料表上的權限。
 
如需有關 Azure 資料總管主體角色的詳細資訊，請參閱 <<c0> [ 角色為基礎的授權](/azure/kusto/management/access-control/role-based-authorization)。 如需管理安全性角色，請參閱[安全性角色管理](/azure/kusto/management/security-roles)。

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark 接收：寫入至 Azure 的資料總管

1. 設定接收參數：

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47"
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. 寫入 Azure 資料總管叢集的 Spark 資料框架以批次：

    ```scala
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoOptions.KUSTO_CLUSTER, cluster)
      .option(KustoOptions.KUSTO_DATABASE, database)
      .option(KustoOptions.KUSTO_TABLE, table)
      .option(KustoOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey) 
      .option(KustoOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .save()
    ```

1. 寫入資料流處理資料：

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    
    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false")
    
    // Write to a Kusto table fro streaming source
    val kustoQ = csvDf
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(Map(
            KustoOptions.KUSTO_CLUSTER -> cluster,
            KustoOptions.KUSTO_TABLE -> table,
            KustoOptions.KUSTO_DATABASE -> database,
            KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId))
          .trigger(Trigger.Once)
    
    kustoQ.start().awaitTermination(TimeUnit.MINUTES.toMillis(8))
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark 來源：讀取 Azure 資料總管

1. 讀取時少量的資料，定義資料的查詢：

    ```scala
    val conf: Map[String, String] = Map(
          KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
          KustoOptions.KUSTO_QUERY -> s"$table | where (ColB % 1000 == 0) | distinct ColA"      
        )
    
    // Simplified syntax flavor
    import org.apache.spark.sql._
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    import org.apache.spark.SparkConf
    
    val df = spark.read.kusto(cluster, database, "", conf)
    display(df)
    ```

1. 當讀取大量資料，就必須提供暫時性的 blob 儲存體。 提供儲存體容器 SAS 金鑰，或儲存體帳戶名稱、 帳戶金鑰和容器名稱。 這個步驟只是所需的目前預覽版本的 Spark 連接器。

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    在上述範例中，我們不存取 Key Vault 使用連接器的介面。 或者，我們會使用簡單的方法，來使用 Databricks 祕密。

1. 從 Azure 資料總管讀取：

    ```scala
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
