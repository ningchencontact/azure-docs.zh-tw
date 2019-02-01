---
title: 教學課程：使用 Spark 以 Azure Databricks 存取 Azure Data Lake Storage Gen2 預覽版資料 | Microsoft Docs
description: 本教學課程說明如何在 Azure Databricks 叢集上執行 Spark 查詢，以存取 Azure Data Lake Storage Gen2 儲存體帳戶中的資料。
services: storage
author: dineshmurthy
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: dineshm
ms.openlocfilehash: 31d18d7ea4ee195f7ffcfa04fb247b5dfd525c6a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251467"
---
# <a name="tutorial-access-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>教學課程：使用 Spark 以 Azure Databricks 存取 Data Lake Storage Gen2 預覽版資料

本教學課程說明如何將 Azure Databricks 叢集連線至已啟用 Azure Data Lake Storage Gen2 (預覽) 功能之 Azure 儲存體帳戶中所儲存的資料。 此連線可讓您以原生方式從叢集對資料執行查詢和分析。

在本教學課程中，您將：

> [!div class="checklist"]
> * 建立 Databricks 叢集
> * 將非結構化的資料內嵌到儲存體帳戶
> * 在 Blob 儲存體中對資料執行分析

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

本教學課程示範如何取用及查詢航班資料 (來源為[美國運輸部](https://transtats.bts.gov/DL_SelectFields.asp))。 

1. 選取 [Prezipped file] \(預先壓縮的檔案\) 核取方塊以選取所有資料欄位。
2. 選取 [下載]，然後將結果儲存到您的機器。
3. 記下所下載資料的檔案名稱與路徑；在稍後的步驟中，您將需要此資訊。

若要完成本教學課程，您需要有一個具有分析功能的儲存體帳戶。 建議您完成該主題的相關[快速入門](data-lake-storage-quickstart-create-account.md)以建立一個帳戶。 

## <a name="set-aside-storage-account-configuration"></a>設定儲存體帳戶組態

您將需要儲存體帳戶的名稱，以及檔案系統端點 URI。

若要在 Azure 入口網站中取得您的儲存體帳戶名稱，請選擇 [所有服務]，並以*儲存體*一詞篩選。 然後，選取 [儲存體帳戶]，並找出您的儲存體帳戶。

若要取得檔案系統端點 URI，請選擇 [屬性]，並在 [屬性] 窗格中尋找 [主要 ADLS 檔案系統端點] 欄位的值。

將這兩個值貼到文字檔中。 您很快就會用到這些值。

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>建立服務主體

依照下列主題中的指引建立服務主體：[操作說明：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

在執行該文章中的步驟時，您必須執行一些特定動作。

:heavy_check_mark:在執行該文章的[建立 Azure Active Directory 應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)一節中的步驟時，請確實將 [建立] 對話方塊的 [登入 URL] 欄位設為您剛才收集到的端點 URI。

:heavy_check_mark:在執行該文章的[將應用程式指派給角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)一節中的步驟時，請確實將您的應用程式指派給 [Blob 儲存體參與者角色]。

:heavy_check_mark:在執行該文章的[取得值以便登入](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)一節中的步驟時，請將租用戶識別碼、應用程式識別碼和驗證金鑰值貼到文字檔中。 您很快就會用到這些資料。

## <a name="create-a-databricks-cluster"></a>建立 Databricks 叢集

下一個步驟是建立 Databricks 叢集以建立資料工作區。

1. 從 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源]。
2. 在搜尋欄位中輸入 **Azure Databricks**。
3. 選取 [Azure Databricks] 刀鋒視窗上的 [建立]。
4. 將 Databricks 服務命名為 **myFlightDataService**(請務必在建立服務時勾選 [釘選到儀表板] 核取方塊)。
5. 選取 [啟動工作區] 以在新的瀏覽器視窗中開啟工作區。
6. 選取左側導覽列中的 [叢集]。
7. 選取 [建立叢集]。
8. 在 [叢集名稱] 欄位中，輸入 **myFlightDataCluster**。
9. 在 [背景工作類型] 欄位中選取 [Standard_D8s_v3]。
10. 將 [背景工作數下限] 值變更為 [4]。
11. 選取頁面頂端的 [建立叢集]。 (此程序可能最多需要 5 分鐘的時間來完成)。
12. 當此程序完成時，選取導覽列左上方的 [Azure Databricks]。
13. 在頁面下半部的 [新增] 區段下選取 [Notebook]。
14. 在 [名稱] 欄位中輸入您所選擇的名稱，然後選取 [Python] 作為語言。
15. 其他所有欄位可保留其預設值。
16. 選取 [建立] 。
17. 將下列程式碼區塊複製並貼到第一個資料格中，但先不要執行此程式碼。

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<application-id>",
           "fs.azure.account.oauth2.client.secret": "<authentication-id>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```
18. 在此程式碼區塊中，將此程式碼區塊中的 `storage-account-name`、`application-id`、`authentication-id` 和 `tenant-id` 預留位置值取代為您在執行[備妥儲存體帳戶組態](#config)和[建立服務主體](#service-principal)小節中的步驟時所收集到的值。 請將 `file-system-name` 預留位置取代為您要為檔案系統指定的任何名稱。

19. 按 **SHIFT + ENTER** 鍵以執行此區塊中的程式碼。

## <a name="ingest-data"></a>擷取資料

### <a name="copy-source-data-into-the-storage-account"></a>將來源資料複製到儲存體帳戶

下一個工作是使用 AzCopy 將資料從 .csv 檔案複製到 Azure 儲存體。 開啟命令提示字元視窗，並輸入下列命令。 請務必以您在上一個步驟中保留的對應值取代 `<DOWNLOAD_FILE_PATH>``<ACCOUNT_NAME>` 及 `<ACCOUNT_KEY>` 預留位置。

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>使用 Databricks Notebook 將 CSV 轉換成 Parquet

在瀏覽器中重新開啟 Databricks，然後執行下列步驟：

1. 選取導覽列左上方的 [Azure Databricks]。
2. 在頁面下半部的 [新增] 區段下選取 [Notebook]。
3. 在 [名稱] 欄位中輸入 **CSV2Parquet**。
4. 其他所有欄位可保留其預設值。
5. 選取 [建立] 。
6. 將下列程式碼貼到 [Cmd 1] 資料格中。 (此程式碼會在編輯器中自動儲存)。

    ```python
    # Use the previously established DBFS mount point to read the data
    # create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time_On_Time*.csv")
    # read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
    print("Done")
    ```

## <a name="explore-data"></a>探索資料

返回 Databricks 工作區，然後選取左側導覽列中的 [最近] 圖示。

1. 選取 [Flight Data Analytics] \(航班資料分析\) Notebook。
2. 按 **Ctrl + Alt + N** 來建立新的資料格。

在 **Cmd 1** 中輸入下列每個程式碼區塊，然後按 **Cmd + Enter** 來執行 Python 指令碼。

若要取得透過 AzCopy 上傳的 CSV 檔案清單，請執行下列指令碼：

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/temp/"))
```

若要建立新檔案，並列出 *parquet/flights* 資料夾中的檔案，請執行此指令碼：

```python
dbutils.fs.put("/mnt/flightdata/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/temp/parquet/flights")
```

藉由這些程式碼範例，您已使用具有 Data Lake Storage Gen2 功能的儲存體帳戶所儲存的資料，探索了 HDFS 的階層式本質。

## <a name="query-the-data"></a>查詢資料

接下來，您可以開始查詢您上傳到儲存體帳戶的資料。 在 **Cmd 1** 中輸入下列每個程式碼區塊，然後按 **Cmd + Enter** 來執行 Python 指令碼。

### <a name="run-simple-queries"></a>執行簡單查詢

若要建立資料來源的資料框架，請執行下列指令碼：

> [!IMPORTANT]
> 請務必將 **<YOUR_CSV_FILE_NAME>** 預留位置取代為您在本教學課程剛開始時下載的檔案名稱。

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferably run this in a separate cmd cell
display(flightDF)
```

若要對資料執行分析查詢，請執行下列指令碼：

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

### <a name="run-complex-queries"></a>執行複雜查詢

若要執行下列更複雜的查詢，請在 Notebook 中一次執行一個區段，並檢查結果。

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="clean-up-resources"></a>清除資源

當已不再需要資源時，請刪除資源群組及所有相關資源。 若要這麼做，請選取儲存體帳戶的資源群組，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

[!div class="nextstepaction"] 
> [使用 Azure HDInsight 上的 Apache Hive 來擷取、轉換和載入資料](data-lake-storage-tutorial-extract-transform-load-hive.md)

