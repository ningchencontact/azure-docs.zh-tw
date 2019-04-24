---
title: 在 HDInsight 中搭配使用 Java 使用者定義函式 (UDF) 和 Apache Hive - Azure
description: 了解如何建立能配合 Apache Hive 使用的以 Java 為基礎的使用者定義函式 (UDF)。 此範例 UDF 會將文字字串的資料表轉換成小寫。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 03/21/2019
ms.date: 04/29/2019
ms.author: v-yiso
ms.openlocfilehash: b8417fe4c15259a7fd485254cf9edd2c8c082e92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60537627"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>在 HDInsight 中搭配使用 Java UDF 和 Apache Hive

了解如何建立能配合 Apache Hive 使用的以 Java 為基礎的使用者定義函式 (UDF)。 此範例中的 Java UDF 會將文字字串的資料表轉換成全部小寫。

## <a name="prerequisites"></a>必要條件

* 在 HDInsight 上 Hadoop 叢集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。
* [Java Developer Kit (JDK) 第 8 版](https://aka.ms/azure-jdks)
* 根據 Apache 正確[安裝](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是適用於 Java 專案的專案建置系統。
* 群集主存储的 [URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 对于 Azure 存储，此值为 wasb://；对于Azure Data Lake Storage Gen2，此值为 abfs://；对于 Azure Data Lake Storage Gen1，此值为 adl://。 如果为 Azure 存储或 Data Lake Storage Gen2 启用了安全传输，则 URI 分别是 wasbs:// 或 abfss://。另请参阅[安全传输](../../storage/common/storage-require-secure-transfer.md)。

* 文字編輯器或 Java IDE

    > [!IMPORTANT]  
    > 如果您是在 Windows 用戶端上建立 Python 檔案，就必須使用以 LF 做為行尾結束符號的編輯器。 如果您不確定編輯器是使用 LF 或 CRLF，請參閱[疑難排解](#troubleshooting)一節，以了解有關移除 CR 字元的步驟。

## <a name="test-environment"></a>测试环境
本文使用的环境是一台运行 Windows 10 的计算机。  命令在命令提示符下执行，各种文件使用记事本进行编辑。 據此修改為您的環境。

在命令提示符下，输入以下命令以创建工作环境：

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>建立範例 Java UDF

1. 輸入下列命令來建立新的 Maven 專案：

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    此命令會建立名為`exampleudf`，其中包含 Maven 專案。

2. 一旦建立專案之後，刪除`exampleudf/src/test`輸入下列命令來建立為專案一部分的目錄：

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. 输入以下命令打开 `pom.xml`：

    ```cmd
    notepad pom.xml
    ```

    然後取代現有`<dependencies>`具有下列 XML 項目：

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    這些項目能指定 HDInsight 3.6 隨附之 Hadoop 和 Hive 的版本。 在 [HDInsight 元件版本設定](../hdinsight-component-versioning.md) 文件中，您可以找到有關 HDInsight 隨附之 Hadoop 和 Hive 的版本資訊。

    在檔案結尾處 `</project>` 之前新增 `<build>` 區段。 此區段應該包含下列 XML：

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    這些項目會定義如何建置專案。 具體來說，定義專案使用的 Java 版本以及如何建置 uberjar 以部署至叢集。

    完成變更後，儲存檔案。

4. 输入以下命令，以创建并打开新文件 `ExampleUDF.java`：

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    将以下 Java 代码复制并粘贴到新文件中。 然后关闭该文件。

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    此程式碼會實作接受字串值並傳回小寫版本之字串的 UDF。

## <a name="build-and-install-the-udf"></a>建置及安裝 UDF

在以下命令中，请将 `sshuser` 替换为实际用户名（如果两者不同）。 将 `mycluster` 替换为实际群集名称。

1. 編譯及封裝 UDF 中輸入下列命令：

    ```cmd
    mvn compile package
    ```

    此命令會建置 UDF 並將它封裝到 `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` 檔案。

2. 使用`scp`命令，以將檔案複製到 HDInsight 叢集，藉由輸入下列命令：

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight
    ```

3. 連接到輸入下列命令來使用 SSH 的叢集：

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.cn
    ```

4. 從開啟的 SSH 工作階段，將 jar 檔案複製到 HDInsight 儲存體。

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>從 Hive 使用 UDF

1. 輸入下列命令，從 SSH 工作階段啟動 Beeline 用戶端：

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    此命令假設您使用預設值 **admin** 來當做叢集的登入帳戶。

2. 一旦到達 `jdbc:hive2://localhost:10001/>` 提示後，請輸入下列命令以將 UDF 新增至 Hive，並將它公開為函式。

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. 使用 UDF 將從資料表擷取的值轉換成小寫字串。

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    此查詢會選取資料表中的狀態，將字串轉換為較低情況下，，然後將它們顯示及未修改的名稱。 此輸出看起來類似下列文字：

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>疑難排解

執行 Hive 作業時，您可能會遇到類似以下文字的錯誤：

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

這個問題可能是由 Python 檔案中的行尾結束符號所引起。 許多 Windows 編輯器預設都是使用 CRLF 做為行尾結束符號，但是 Linux 應用程式通常預期使用 LF。

若要在檔案上傳至 HDInsight 之前移除 CR 字元，您可以使用下列 PowerShell 陳述式︰

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>後續步驟

如需 Hive 的其他操作方法，請參閱 [搭配 HDInsight 使用 Apache Hive](hdinsight-use-hive.md)。

如需 Hive 使用者定義函式的詳細資訊，請造訪 apache.org 並參閱 Hive wiki 的 [Apache Hive 運算子和使用者定義函式](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)一節。
