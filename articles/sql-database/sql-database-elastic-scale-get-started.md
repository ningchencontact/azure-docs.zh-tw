---
title: "開始使用彈性資料庫工具-Azure |Microsoft 文件"
description: "基本的 Azure SQL Database，包括簡單執行範例應用程式的彈性資料庫工具功能的詳細說明。"
services: sql-database
documentationcenter: 
manager: jstrauss
author: anumjs
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: anjangsh
ms.openlocfilehash: 615e5b0bf299a844ea2d37476fc704c48e17b363
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2017
---
# <a name="get-started-with-elastic-database-tools"></a>開始使用彈性資料庫工具
本文件介紹您的開發人員經驗[彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)藉由協助您執行範例應用程式。 範例應用程式會建立簡單的分區化應用程式，並且瀏覽 Azure SQL Database 的彈性資料庫工具功能的重要功能。 它著重在[分區對應管理](sql-database-elastic-scale-shard-map-management.md)、[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)和[多分區查詢](sql-database-elastic-scale-multishard-querying.md)的使用案例。 適用於.NET 以及 Java 用戶端程式庫。 

## <a name="elastic-database-tools-for-java"></a>For Java 的彈性資料庫工具
### <a name="prerequisites"></a>必要條件
* Java Developer Kit (JDK)，版本 1.8 版或更新版本
* [Maven](http://maven.apache.org/download.cgi)
* 在 Azure 或本機的 SQL Server 執行個體的邏輯伺服器

### <a name="download-and-run-the-sample-app"></a>下載及執行範例應用程式
若要建置的 JAR 檔案，並開始使用範例專案，執行下列作業： 
1. 複製[GitHub 儲存機制](https://github.com/Microsoft/elastic-db-tools-for-java)包含用戶端程式庫，以及範例應用程式。 

2. 編輯_./sample/src/main/resources/resource.properties_檔案來進行下列設定：
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. 若要建置範例專案，在_。 / 樣本的_目錄中，執行下列命令：

    ```
    mvn install
    ```
    
4. 若要啟動範例專案，在_。 / 樣本的_目錄中，執行下列命令： 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. 若要深入了解用戶端程式庫功能，試驗不同的選項。 請隨意探索程式碼，以了解範例應用程式的實作。

    ![進度 - Java][5]
    
恭喜！ 您已成功建置，並使用 Azure SQL database 的彈性資料庫工具執行第一個的分區化應用程式。 請使用 Visual Studio 或 SQL Server Management Studio 連接到您的 SQL 資料庫，以快速瀏覽範例所建立的分區。 您會看見範例所建立的新範例分區資料庫和分區對應管理員資料庫。 

若要將 Maven 專案中加入用戶端程式庫，加入下列相依性 POM 檔案中：

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>適用於.NET 的彈性資料庫工具 
### <a name="prerequisites"></a>必要條件
* 含 C# 的 Visual Studio 2012 或更新版本。 請在 [Visual Studio 下載](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)上下載免費版本。
* NuGet 2.7 或更新版本。 若要取得最新版本，請參閱[安裝 NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)。

### <a name="download-and-run-the-sample-app"></a>下載及執行範例應用程式
若要安裝程式庫，請移至 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。 程式庫是使用範例應用程式所安裝，如下一節所述。

若要下載及執行範例，請遵循下列步驟： 

1. 從 MSDN 下載 [Azure SQL 的彈性 DB 工具：開始使用範例](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6)。 將範例解壓縮至您選擇的位置。

2. 若要建立專案，從 *C#* 目錄開啟 *ElasticScaleStarterKit.sln* 方案。

3. 在範例專案的方案中，開啟 *app.config* 檔案。 然後依照檔案中的指示，將您的 Azure SQL Database 伺服器名稱和登入資訊 （使用者名稱和密碼）。

4. 建置並執行應用程式。 當系統提示您時，讓 Visual Studio 來還原方案的 NuGet 封裝。 這個動作會從 NuGet 下載最新版的彈性資料庫用戶端程式庫。

5. 若要深入了解用戶端程式庫功能，試驗不同的選項。 請注意輸出，應用程式便會在主控台中的步驟，並隨意瀏覽在幕後的程式碼。
   
    ![Progress][4]

恭喜！ 您已成功建立，而且使用彈性資料庫工具上的 SQL 資料庫執行第一個的分區化應用程式。 請使用 Visual Studio 或 SQL Server Management Studio 連接到您的 SQL 資料庫，以快速瀏覽範例所建立的分區。 您會看見範例所建立的新範例分區資料庫和分區對應管理員資料庫。

> [!IMPORTANT]
> 建議您一律使用最新版的 Management Studio，如此就能與 Azure 及 SQL Database 更新保持同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

## <a name="key-pieces-of-the-code-sample"></a>程式碼範例的主要部分
* **管理分區和分區對應**: 程式碼說明如何使用分區、 範圍和中的對應*ShardManagementUtils.cs*檔案。 如需詳細資訊，請參閱[使用分區對應管理員相應放大資料庫](http://go.microsoft.com/?linkid=9862595)。  

* **資料依存路由**： 路由至正確的分區交易的所示*DataDependentRoutingSample.cs*檔案。 如需詳細資訊，請參閱[資料相依路由](http://go.microsoft.com/?linkid=9862596)。 

* **查詢透過多個分區**： 跨分區查詢如下所示*MultiShardQuerySample.cs*檔案。 如需詳細資訊，請參閱[多分區查詢](http://go.microsoft.com/?linkid=9862597)。

* **加入空白的分區**： 反覆加入新的空白分區中的程式碼會執行*CreateShardSample.cs*檔案。 如需詳細資訊，請參閱[使用分區對應管理員相應放大資料庫](http://go.microsoft.com/?linkid=9862595)。

## <a name="other-elastic-scale-operations"></a>其他 Elastic Scale 作業
* **分割現有分區**： 分割合併工具所提供的功能分割分區。 如需詳細資訊，請參閱[在向外延展的雲端資料庫之間移動資料](sql-database-elastic-scale-overview-split-and-merge.md)。

* **合併現有分區**： 分區合併也會使用分割合併工具中執行。 如需詳細資訊，請參閱[在向外延展的雲端資料庫之間移動資料](sql-database-elastic-scale-overview-split-and-merge.md)。   

## <a name="cost"></a>成本
彈性資料庫工具程式庫是免費的。 當您使用彈性資料庫工具時，您就會產生任何額外的費用，超過您的 Azure 使用量的成本。 

例如，範例應用程式會建立新資料庫。 這項功能的成本取決於您選擇 SQL Database 版本和您的應用程式的 Azure 使用量。

如需價格資訊，請參閱 [SQL Database 定價詳細資料](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="next-steps"></a>後續步驟
如需彈性資料庫工具的詳細資訊，請參閱下列文章：

* 程式碼範例： 
  * 彈性資料庫工具 ([.NET](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)、[Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Azure SQL 的實體架構整合的彈性資料庫工具](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [指令碼中心的分區彈性](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* 部落格：[Elastic Scale 公告 (英文)](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy：[使用分區化與彈性資料庫用戶端程式庫視訊實作相應放大](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) 
* 第 9 頻道：[Elastic Scale 概觀影片 (英文)](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* 討論論壇：[Azure SQL Database 論壇](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* 若要測量效能︰ [分區對應管理員的效能計數器](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

