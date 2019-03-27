---
title: 使用 Apache Pig - Azure HDInsight
description: 了解如何在 HDInsight 上搭配 Apache Hadoop 使用 Pig。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.openlocfilehash: 2b0f3a4394267a808b6e6e0a73abfaf1e698ff1c
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445029"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>在 HDInsight 上搭配 Apache Hadoop 使用 Apache Pig

了解如何使用 [Apache Pig](https://pig.apache.org/) 搭配 HDInsight。

Apache Pig 是一個平台，可使用名為 *Pig Latin* 的程序性語言建立 Apache Hadoop 的程式。 若要建立 *MapReduce* 解決方案，Pig 是 Java 的替代選項，而且也已包含在 Azure HDInsight 中。 使用下表了解可搭配 HDInsight 使用 Pig 的各種方式︰

| **使用此方法**  | ...一個 **互動式** 殼層 | ...**批次** 處理 | ...搭配此 **叢集作業系統** | ...從此 **用戶端作業系統** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ | Linux |Linux、Unix、Mac OS X 或 Windows |
| [REST API](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux 或 Windows |Linux、Unix、Mac OS X 或 Windows |
| [.NET SDK for Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux 或 Windows |Windows (目前) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux 或 Windows | Windows |

> [!IMPORTANT]  
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 淘汰](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

## <a id="why"></a>為何要使用 Apache Pig

在 Hadoop 中使用 MapReduce 處理資料的其中一項挑戰是，只藉由使用 map 和 reduce 函數實作處理邏輯。 如果是複雜的處理，您經常必須將處理分成多個鏈結在一起的 MapReduce 作業，才能獲得想要的結果。

Pig 可讓您將處理定義為一系列轉換，使資料流過以產生所需的輸出。

Pig Latin 語言可讓您從原始輸入描述資料流 (經過一或多個轉換後) 以產生所需的輸出。 Pig Latin 程式遵循此一般模式：

* **載入**：從檔案系統讀取要處理的資料。

* **轉換**：處理資料。

* **傾印或儲存**：將資料輸出至畫面，或儲存資料以供處理。

### <a name="user-defined-functions"></a>使用者定義函式

Pig Latin 还支持使用用户定义函数 (UDF) 来调用外部组件，以便实现难以在 Pig Latin 中建模的逻辑。

如需 Pig Latin 的詳細資訊，請參閱 [Pig Latin 參考手冊 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) (英文) 和 [Pig Latin 參考手冊 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html) (英文)。

如需搭配 Pig 使用 UDF 的範例，請參閱以下文件：

* [在 HDInsight 中使用 Apache DataFu 搭配 Apache Pig](apache-hadoop-use-pig-datafu-udf.md) - DataFu 是由 Apach 維護的 UDF 實用集合
* [在 HDInsight 中使用 Python 搭配 Apache Pig 和 Apache Hive](python-udf-hdinsight.md)
* [在 HDInsight 中使用 C# 搭配 Apache Hive 和 Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>範例資料

HDInsight 提供各種範例資料集，儲存在 `/example/data` 和 `/HdiSamples` 目錄中。 這些目錄位於您的叢集預設儲存體中。 本文件中的 Pig 範例使用 `/example/data/sample.log` 中的 *log4j* 檔案。

该文件中的每个日志都包含一行字段，其中包含一个 `[LOG LEVEL]` 字段，用于显示类型和严重性，例如：

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

在上一個範例中，記錄層級是「錯誤」。

> [!NOTE]  
> 您也可以使用 [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) 記錄工具產生 log4j 檔案，然後將該檔案上傳至 Blob。 如需指示，請參閱 [將資料上傳至 HDInsight](../hdinsight-upload-data.md) 。 如需關於如何搭配 HDInsight 使用 Azure 儲存體 Blob 的詳細資訊，請參閱 [搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-hadoop-use-blob-storage.md)。

## <a id="job"></a>範例作業

以下 Pig Latin 作業會從 HDInsight 叢集的預設儲存體載入 `sample.log` 檔案。 然後該工作會執行一系列轉換，進而產生輸入資料中每個記錄層級出現次數的計數。 結果會寫入 STDOUT。

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

下圖顯示每個轉換如何處理資料的摘要。

![转换的图形表示形式][image-hdi-pig-data-transformation]

## <a id="run"></a>執行 Pig Latin 工作

HDInsight 可以使用各種方法執行 Pig Latin 工作。 請使用下表決定適合您的方法，然後跟著連結逐項閱讀介紹。

| **使用此方法**  | ...一個 **互動式** 殼層 | ...**批次** 處理 | ...使用此**群集操作系统** | ...從這個**用戶端** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ | Linux |Linux、Unix、Mac OS X 或 Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux 或 Windows |Linux、Unix、Mac OS X 或 Windows |
| [.NET SDK for Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux 或 Windows |Windows (目前) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux 或 Windows | Windows |

> [!IMPORTANT]  
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 淘汰](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

## <a name="pig-and-sql-server-integration-services"></a>Pig 和 SQL Server Integration Services

您可以使用 SQL Server Integration Services (SSIS) 執行 Pig 作業。 Azure Feature Pack for SSIS 提供适用于 HDInsight 上的 Pig 作业的以下组件。

* [Azure HDInsight Pig 工作][pigtask]

* [Azure 訂用帳戶連接管理員][connectionmanager]

在[這裡][ssispack]深入了解適用於 SSIS 的 Azure Feature Pack。

## <a id="nextsteps"></a>后续步骤
現在您已學會如何搭配 HDInsight 使用 Pig，接著請使用下列連結來探索 Azure HDInsight 的其他使用方式。

* [將資料上傳至 HDInsight](../hdinsight-upload-data.md)
* [搭配 HDInsight 使用 Apache Hive][hdinsight-use-hive]
* [搭配 HDInsight 使用 Apache Sqoop](hdinsight-use-sqoop.md)
* [使用 Apache Oozie 搭配 HDInsight](../hdinsight-use-oozie-linux-mac.md)
* [搭配 HDInsight 使用 MapReduce 作業][hdinsight-use-mapreduce]

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
