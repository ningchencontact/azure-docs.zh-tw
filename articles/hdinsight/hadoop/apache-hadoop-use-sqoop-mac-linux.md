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
ms.openlocfilehash: c839aeae77d7e75fb30d82c410c331d21f5868ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406037"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>使用 Apache Sqoop 在 Apache Hadoop on HDInsight 與 SQL Database 之間匯入及匯出資料

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Apache Sqoop，在 Azure HDInsight 中的 Apache Hadoop 叢集與 Azure SQL Database 或 Microsoft SQL Server 資料庫之間進行匯入和匯出。 本文件中的步驟直接從 Hadoop 叢集的前端節點使用 `sqoop` 命令。 您可以使用 SSH 連接至前端節點，並執行本文件中的命令。 本文是在[HDInsight 中搭配使用 Apache Sqoop 與 Hadoop](./hdinsight-use-sqoop.md)的接續。

## <a name="prerequisites"></a>先決條件

* 完成[設定測試環境](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) [, 從在 HDInsight 中搭配使用 Apache Sqoop 與 Hadoop](./hdinsight-use-sqoop.md)。

* 用來查詢 Azure SQL 資料庫的用戶端。 請考慮使用[SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)或[Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)。

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="sqoop-export"></a>Sqoop export

從 Hive 到 SQL Server。

1. 使用 SSH 連接到 HDInsight 叢集。 將`CLUSTERNAME`取代為您的叢集名稱, 然後輸入命令:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 以`MYSQLSERVER`您 SQL Server 的名稱取代。 若要確認 Sqoop 可以看到您的 SQL Database, 請在開啟的 SSH 連線中輸入下列命令。 當出現提示時, 輸入 SQL Server 登入的密碼。 此命令會傳回資料庫的清單。

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. 請`MYSQLSERVER`以您的 SQL Server 名稱取代, 並`MYDATABASE`以您的 SQL 資料庫名稱取代。 若要將資料從 Hive `hivesampletable`資料表匯出至`mobiledata` SQL Database 中的資料表, 請在開啟的 SSH 連線中輸入下列命令。 當出現提示時, 輸入 SQL Server 登入的密碼

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. 若要確認資料已匯出, 請從您的 SQL 用戶端使用下列查詢, 以查看匯出的資料:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop import

從 SQL Server 到 Azure 儲存體。

1. 請`MYSQLSERVER`以您的 SQL Server 名稱取代, 並`MYDATABASE`以您的 SQL 資料庫名稱取代。 在開啟的 SSH 連線中輸入下列命令, 以將資料從`mobiledata` SQL Database 中的資料表匯入`wasb:///tutorials/usesqoop/importeddata`至 HDInsight 上的目錄。 當出現提示時, 輸入 SQL Server 登入的密碼。 資料中的欄位是以定位字元分隔，行是以換行字元終止。

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. 匯入完成後, 請在開啟的 SSH 連線中輸入下列命令, 以列出新目錄中的資料:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>限制

* 大量匯出 - 使用 Linux 型 HDInsight，Sqoop 連接器用來將資料匯出至 Microsoft SQL Server 或 Azure SQL Database，不支援大量插入。

* 批次處理 - 使用 Linux 型 HDInsight，執行插入時若使用 `-batch` 參數，Sqoop 將會執行多個插入，而不是批次處理插入作業。

## <a name="important-considerations"></a>重要考量︰

* HDInsight 與 SQL Server 必須位於相同的 Azure 虛擬網路。

    如需範例，請參閱[將 HDInsight 連線至內部部署網路](./../connect-on-premises-network.md)文件。

    如需使用 HDInsight 搭配 Azure 虛擬網路的詳細資訊，請參閱[使用 Azure 虛擬網路擴充 HDInsight](../hdinsight-plan-virtual-network-deployment.md)文件。 如需 Azure 虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../../virtual-network/virtual-networks-overview.md)文件。

* SQL Server 也必須設定為允許 SQL 驗證。 如需詳細資訊，請參閱[選擇驗證模式](https://msdn.microsoft.com/ms144284.aspx)文件。

* 您可能必須設定 SQL Server 以接受遠端連線。 如需詳細資訊，請參閱[如何疑難排解 SQL Server Database Engine 連線](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)文件 (英文)。

## <a name="next-steps"></a>後續步驟

現在，您已了解如何使用 Sqoop。 若要深入了解，請參閱：

* [使用 Apache Oozie 搭配 HDInsight](../hdinsight-use-oozie-linux-mac.md)：在 Oozie 工作流程中使用 Sqoop 動作。
* [使用 HDInsight 分析航班延誤資料](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)：使用互動式查詢來分析航班延誤資料, 然後使用 Sqoop 將資料匯出至 Azure SQL 資料庫。
* [將資料上傳至 HDInsight](../hdinsight-upload-data.md)：尋找其他方法將資料上傳至 HDInsight/Azure Blob 儲存體。
