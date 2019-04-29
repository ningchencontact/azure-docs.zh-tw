---
title: 採用 Apache Hadoop 的 Apache Sqoop - Azure HDInsight
description: 了解如何使用 Apache Sqoop 在 Apache Hadoop on HDInsight 與 Azure SQL Database 之間進行匯入和匯出。
keywords: hadoop sqoop,sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 6dcb6853daf34fede590011d165c0ba9001cbac6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128969"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>使用 Apache Sqoop 在 Apache Hadoop on HDInsight 與 SQL Database 之間匯入及匯出資料

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Apache Sqoop，在 Azure HDInsight 中的 Apache Hadoop 叢集與 Azure SQL Database 或 Microsoft SQL Server 資料庫之間進行匯入和匯出。 本文件中的步驟直接從 Hadoop 叢集的前端節點使用 `sqoop` 命令。 您可以使用 SSH 連接至前端節點，並執行本文件中的命令。 這篇文章是將延續[在 HDInsight 中搭配 Hadoop 使用 Apache Sqoop](./hdinsight-use-sqoop.md)。

## <a name="prerequisites"></a>必要條件

* 完成[設定測試環境](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)從[在 HDInsight 中搭配 Hadoop 使用 Apache Sqoop](./hdinsight-use-sqoop.md)。

* 若要查詢 Azure SQL database 用戶端。 請考慮使用[SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)或是[Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)。

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="sqoop-export"></a>Sqoop export

從 SQL server 的 Hive。

1. 使用 SSH 連接到 HDInsight 叢集。 取代`CLUSTERNAME`與叢集的名稱，然後輸入命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 取代`MYSQLSERVER`您的 SQL Server 的名稱。 若要確認 sqoop 看得見您的 SQL Database，請開啟 SSH 連線中輸入下列命令。 SQL Server 登入出現提示時輸入的密碼。 此命令會傳回一份資料庫。

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. 取代`MYSQLSERVER`您的 SQL Server 的名稱和`MYDATABASE`您 SQL 資料庫的名稱。 若要將資料從 Hive 匯出`hivesampletable`資料表`mobiledata`在 SQL Database 資料表中，輸入下列命令，在您開啟的 SSH 連線。 SQL Server 登入出現提示時輸入的密碼

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. 若要確認資料已匯出，使用下列查詢從您的 SQL 用戶端若要檢視匯出的資料：

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop import

從 SQL Server 到 Azure 儲存體。

1. 取代`MYSQLSERVER`您的 SQL Server 的名稱和`MYDATABASE`您 SQL 資料庫的名稱。 輸入下列命令在您開啟的 SSH 連線匯入資料`mobiledata`到資料表中的 SQL 資料庫，`wasb:///tutorials/usesqoop/importeddata`在 HDInsight 上的目錄。 SQL Server 登入出現提示時輸入的密碼。 資料中的欄位是以定位字元分隔，行是以換行字元終止。

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. 匯入完成後，請開啟 SSH 連線中輸入下列命令來列出新的目錄中的資料：

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>限制

* 大量匯出 - 使用 Linux 型 HDInsight，Sqoop 連接器用來將資料匯出至 Microsoft SQL Server 或 Azure SQL Database，不支援大量插入。

* 批次處理 - 使用 Linux 型 HDInsight，執行插入時若使用 `-batch` 參數，Sqoop 將會執行多個插入，而不是批次處理插入作業。

## <a name="important-considerations"></a>重要考量︰

* HDInsight 與 SQL Server 必須位於相同的 Azure 虛擬網路。

    如需範例，請參閱[將 HDInsight 連線至內部部署網路](./../connect-on-premises-network.md)文件。

    如需使用 HDInsight 搭配 Azure 虛擬網路的詳細資訊，請參閱[使用 Azure 虛擬網路擴充 HDInsight](../hdinsight-extend-hadoop-virtual-network.md)文件。 如需 Azure 虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../../virtual-network/virtual-networks-overview.md)文件。

* SQL Server 也必須設定為允許 SQL 驗證。 如需詳細資訊，請參閱[選擇驗證模式](https://msdn.microsoft.com/ms144284.aspx)文件。

* 可能需要将 SQL Server 配置为接受远程连接。 如需詳細資訊，請參閱[如何疑難排解 SQL Server Database Engine 連線](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)文件 (英文)。

## <a name="next-steps"></a>後續步驟

現在，您已了解如何使用 Sqoop。 若要深入了解，請參閱：

* [使用 Apache Oozie 搭配 HDInsight](../hdinsight-use-oozie-linux-mac.md)：在 Oozie 工作流程中使用 Sqoop 動作。
* [使用 HDInsight 分析航班延誤資料](../hdinsight-analyze-flight-delay-data-linux.md)：使用 Apache Hive 來分析航班誤點資料，然後使用 Sqoop 將資料匯出至 Azure SQL 資料庫。
* [將資料上傳至 HDInsight](../hdinsight-upload-data.md)：尋找其他方法將資料上傳至 HDInsight/Azure Blob 儲存體。
