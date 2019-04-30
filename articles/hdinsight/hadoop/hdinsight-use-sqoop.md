---
title: 使用 Azure HDInsight (Apache Hadoop) 執行 Apache Sqoop 作業
description: 了解如何從工作站使用 Azure PowerShell，在 Hadoop 叢集與 Azure SQL Database 之間執行 Sqoop 匯入和匯出。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 6764d8d812789c9f54fa59e10b2a3e416e583a9c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129394"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>搭配使用 Apache Sqoop 與 HDInsight 中的 Hadoop
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

了解如何在 HDInsight 中使用 Apache Sqoop 匯入和匯出的 HDInsight 叢集與 Azure SQL database 之間的資料。

雖然 Apache Hadoop 是自然的選擇來處理非結構化及半結構化資料，例如記錄和檔案，但也可能需要處理結構化的資料儲存在關聯式資料庫中。

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html)是 Hadoop 叢集和關聯式資料庫之間傳送資料的工具。 此工具可讓您從 SQL Server、MySQL 或 Oracle 等關聯式資料庫管理系統 (RDBMS)，將資料匯入至 Hadoop 分散式檔案系統 (HDFS)，使用 MapReduce 或 Apache Hive 轉換 Hadoop 中的資料，然後將資料匯回 RDBMS。 在本文中，您使用 SQL Server 資料庫為關聯式資料庫。

> [!IMPORTANT]  
> 這篇文章會設定要執行資料傳輸的測試環境。 您其中一個區段中的方法，然後選擇此環境的資料傳輸方法[執行 Sqoop 作業](#run-sqoop-jobs)下, 面會詳細。

如需 HDInsight 叢集支援的 Sqoop 版本，請參閱[什麼是 HDInsight 所提供叢集版本的新功能？](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>了解案例

HDInsight 叢集附有某些範例資料。 您將用到以下兩個範例：

* Apache Log4j 記錄檔，位於`/example/data/sample.log`。 下列記錄擷取自此檔案：

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* 名為的 Hive 資料表`hivesampletable`，會參考此資料檔案位於`/hive/warehouse/hivesampletable`。 此資料表包含某些行動裝置資料。
  
  | 欄位 | 数据类型 |
  | --- | --- |
  | clientid |string |
  | querytime |string |
  | market |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | state |string |
  | country |string |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

在本文中，您可以使用這兩個資料集測試 Sqoop 匯入和匯出。

## <a name="create-cluster-and-sql-database"></a>設定測試環境
使用 Azure Resource Manager 範本在 Azure 入口網站建立叢集、 SQL database 和其他物件。 範本可在[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)。 Resource Manager 範本會呼叫 bacpac 套件，以部署到 SQL database 的資料表結構描述。  Bacpac 套件位於公用 Blob 容器中 (https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac)。 如果您想要針對 Bacpac 檔案使用私用容器，請在範本中使用下列值︰

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> 使用範本或 Azure 入口網站匯入的方式只支援從 Azure Blob 儲存體匯入 BACPAC 檔案。

1. 選取下列影像，以開啟 Resource Manager 範本在 Azure 入口網站。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 輸入下列屬性：

    |欄位 |Value |
    |---|---|
    |訂用帳戶 |從下拉式清單中選取您的 Azure 訂用帳戶。|
    |資源群組 |從下拉式清單中，選取您的資源群組，或建立新的帳戶|
    |位置 |從下拉式清單中選取的區域。|
    |叢集名稱 |輸入 Hadoop 叢集的名稱。 使用僅小寫字母。|
    |叢集登入使用者名稱 |將預先填入的值保持`admin`。|
    |叢集登入密碼 |輸入密碼。|
    |Ssh 使用者名稱 |將預先填入的值保持`sshuser`。|
    |Ssh 密碼 |輸入密碼。|
    |Sql 系統管理員登入 |將預先填入的值保持`sqluser`。|
    |Sql 管理員密碼 |輸入密碼。|
    |_artifacts 位置 | 使用默认值（除非想要在其他位置使用自己的 bacpac 文件）。|
    |_artifacts 位置 Sas 權杖 |保留空白。|
    |Bacpac 檔案名稱 |使用默认值（除非想要使用自己的 bacpac 文件）。|
    |位置 |使用預設值。|

    Azure SQL 伺服器名稱會是`<ClusterName>dbserver`。 資料庫名稱會是`<ClusterName>db`。 預設儲存體帳戶名稱會是`e6qhezrh2pdqu`。

3. 選取 [我同意上方所述的條款及條件]。

4. 選取 [購買]。 您會看到新的圖格，標題為「提交範本部署的部署」。 大約需要 20 分鐘的時間來建立叢集和 SQL Database。

## <a name="run-sqoop-jobs"></a>執行 Sqoop 工作

HDInsight 可以使用各種方法執行 Sqoop 工作。 請使用下表決定適合您的方法，然後跟著連結逐項閱讀介紹。

| **使用此方法**  | ...一個 **互動式** 殼層 | ...**批次** 處理 | ...從此 **用戶端作業系統** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux、Unix、Mac OS X 或 Windows |
| [.NET SDK for Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (目前) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? | Windows |

## <a name="limitations"></a>限制

* 大量匯出 - 使用 Linux 型 HDInsight，用來將資料匯出至 Microsoft SQL Server 或 Azure SQL Database 的 Sqoop 連接器目前不支援大量插入。
* 批次處理 - 使用以 Linux 為基礎的 HDInsight，執行插入時若使用 `-batch` 參數，Sqoop 將會執行多個插入，而不是將插入作業批次處理。

## <a name="next-steps"></a>後續步驟
現在，您已了解如何使用 Sqoop。 若要深入了解，請參閱：

* [搭配 HDInsight 使用 Apache Hive](../hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Apache Pig](../hdinsight-use-pig.md)
* [將資料上傳至 HDInsight](../hdinsight-upload-data.md)：尋找其他方法將資料上傳至 HDInsight/Azure Blob 儲存體。
