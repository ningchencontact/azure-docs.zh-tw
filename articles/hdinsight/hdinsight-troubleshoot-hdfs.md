---
title: 針對 Azure HDInsight 中的 HDFS 進行疑難排解
description: 取得有關使用 HDFS 和 Azure HDInsight 的常見問題解答。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.custom: seodec18
ms.openlocfilehash: 1b244b3d402d7896ade2fa2e84881a0079ef6bba
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515650"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>使用 Azure HDInsight 針對 Apache Hadoop HDFS 問題進行疑難排解

了解在 Apache Ambari 中使用 Hadoop 分散式檔案系統 (HDFS) 承載時最常發生的問題及其解決方法。

## <a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>如何從叢集內部存取本機 HDFS？

### <a name="issue"></a>問題

從命令列和應用程式程式碼存取本機 HDFS，而不是從 HDInsight 叢集內部使用 Azure Blob 儲存體或 Azure Data Lake Storage 進行存取。   

### <a name="resolution-steps"></a>解決步驟

1. 在命令提示字元中依照字面使用 `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...`，如下列命令所示：

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. 從原始程式碼依照字面使用 URI `hdfs://mycluster/`，如下列範例應用程式所示：

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. 使用下列命令在 HDInsight 叢集上執行已編譯的 .jar 檔案 (例如名為 `java-unit-tests-1.0.jar` 的檔案)：

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶, 以改善客戶體驗。 將 Azure 社區連接到正確的資源: 解答、支援和專家。

* 如果您需要更多協助, 您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**], 或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊, 請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權, 而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。