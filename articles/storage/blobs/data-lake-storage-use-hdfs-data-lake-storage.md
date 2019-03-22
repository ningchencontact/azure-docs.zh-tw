---
title: 使用 HDFS CLI 搭配 Azure Data Lake Storage Gen2
description: 適用於 Data Lake Storage Gen2 的 HDFS CLI 簡介
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: c77981574ff2e507af7012f26f742dda62d952fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57991957"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>使用 HDFS CLI 搭配 Data Lake Storage Gen2

Azure Data Lake Storage Gen2 可讓您管理及存取資料，就如同使用 [Hadoop 分散式檔案系統 (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) 一樣。 無論您是有附加的 HDInsight 叢集，或是執行使用 Azure Databricks 來針對儲存於 Azure 儲存體帳戶中的資料執行分析的 Apache Spark 作業，都可以使用命令列介面 (CLI) 來擷取和操作已載入的資料。

## <a name="hdfs-cli-with-hdinsight"></a>HDFS CLI 搭配 HDInsight

HDInsight 可以存取本機連接至計算節點的分散式檔案系統。 您可以使用與 HDFS 直接互動的殼層，以及 Hadoop 支援的其他檔案系統，來存取此檔案系統。 以下是常用的命令和實用資源的連結。

>[!IMPORTANT]
>HDInsight 叢集的計費，是從建立叢集之後開始算起，並於叢集被刪除時停止計算。 計費是以每分鐘按比例計算，因此不再使用時，請一律刪除您的叢集。 若要了解如何刪除叢集，請參閱我們[針對該主題的文章](../../hdinsight/hdinsight-delete-cluster.md)。 不過，儲存在已啟用 Data Lake Storage Gen2 之儲存體帳戶中的資料，即使在 HDInsight 叢集被刪除後仍會存在。

### <a name="create-a-file-system"></a>建立檔案系統

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* 請將 `<file-system-name>` 預留位置取代為您要為檔案系統指定的名稱。

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置。

### <a name="get-a-list-of-files-or-directories"></a>取得檔案清單或目錄

    hdfs dfs -ls <path>

將 `<path>` 預留位置取代為檔案系統或檔案系統資料夾的 URI。

例如：`hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

### <a name="create-a-directory"></a>建立目錄

    hdfs dfs -mkdir [-p] <path>

將 `<path>` 預留位置取代為根檔案系統名稱或您檔案系統內的資料夾。

例如：`hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

### <a name="delete-a-file-or-directory"></a>刪除檔案或目錄

    hdfs dfs -rm <path>

將 `<path>` 預留位置取代為您想要刪除之檔案或資料夾的 URI。

例如：`hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>在 Linux 上搭配使用 HDFS CLI 和 HDInsight Hadoop 叢集

首先，建立[服務的遠端存取](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services)。 如果您挑選 [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)，則範例 PowerShell 程式碼會看起來像這樣：

```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
您可以在 Azure 入口網站中 HDInsight 叢集刀鋒視窗的「SSH + 叢集登入」區段找到連接字串。 在叢集建立時，就已指定 SSH 認證。

如需有關 HDFS CLI 的詳細資訊，請參閱[官方文件](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)和 [HDFS 使用權限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)。 若要深入了解 Databricks 中的 ACL，請參閱[祕密 CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli)。

## <a name="hdfs-cli-with-azure-databricks"></a>HDFS CLI 搭配 Azure Databricks

Databricks 提供以 Databricks REST API 為基礎所建置的 CLI，讓您能輕鬆使用。 開放原始碼專案裝載於 [GitHub](https://github.com/databricks/databricks-cli)。 以下是常用的命令。

### <a name="get-a-list-of-files-or-directories"></a>取得檔案清單或目錄

    dbfs ls [-l]

### <a name="create-a-directory"></a>建立目錄

    dbfs mkdirs

### <a name="delete-a-file"></a>刪除檔案

    dbfs rm [-r]

與 Databricks 互動的另一種方法是筆記本。 雖然筆記本有主要語言，但您可以在資料格的開頭指定語言魔術命令 %language 來混合語言。 具體而言，%sh 可讓您在筆記本中執行殼層程式碼，與本文中稍早的 HDInsight 範例很像。

### <a name="get-a-list-of-files-or-directories"></a>取得檔案清單或目錄

    %sh ls <args>

### <a name="create-a-directory"></a>建立目錄

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>刪除檔案或目錄

    %sh rm [-skipTrash] URI [URI ...]

啟動 Azure Databricks 中的 Spark 叢集之後，您將建立新的筆記本。 範例筆記本指令碼會看起來像這樣：

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

如需關於 Databricks CLI 的詳細資訊，請參閱[官方文件](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)。 如需有關筆記本的詳細資訊，請參閱文件的[筆記本](https://docs.azuredatabricks.net/user-guide/notebooks/index.html)小節。

## <a name="set-file-and-directory-level-permissions"></a>設定檔案和目錄層級權限

您可以在檔案和目錄層級上設定並取得存取權限。 以下是協助您開始使用的一些命令。 

若要深入了解 Azure Data Lake Gen2 檔案系統的檔案和目錄層級權限，請參閱 [Azure Data Lake Storage Gen2 的存取控制](storage-data-lake-storage-access-control.md)。

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>顯示檔案和目錄的存取控制清單 (ACL)

    hdfs dfs -getfacl [-R] <path>

範例：

`hdfs dfs -getfacl -R /dir`

請參閱 [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

### <a name="set-acls-of-files-and-directories"></a>設定檔案和目錄的 ACL

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

範例：

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

請參閱 [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

### <a name="change-the-owner-of-files"></a>變更檔案的擁有者

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

請參閱 [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

### <a name="change-group-association-of-files"></a>變更檔案的群組關聯

    hdfs dfs -chgrp [-R] <group> <URI>

請參閱 [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

### <a name="change-the-permissions-of-files"></a>變更檔案的權限

    hdfs dfs -chmod [-R] <mode> <URI>

請參閱 [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

您可以在 [Apache Hadoop 2.4.1 檔案系統殼層指南](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)網站上檢視完整的命令清單。

## <a name="next-steps"></a>後續步驟

[在 Azure Databricks 中使用具有 Azure Data Lake Storage Gen2 功能的帳戶](./data-lake-storage-quickstart-create-databricks-account.md) 
