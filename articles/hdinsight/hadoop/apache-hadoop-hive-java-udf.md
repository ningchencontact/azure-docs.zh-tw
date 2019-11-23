---
title: Java user-defined function (UDF) with Apache Hive Azure HDInsight
description: 了解如何建立能配合 Apache Hive 使用的以 Java 為基礎的使用者定義函式 (UDF)。 此範例 UDF 會將文字字串的資料表轉換成小寫。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/20/2019
ms.openlocfilehash: 73a2a612a4eeb4a59f12abf0660fffb092f0547f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327200"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>在 HDInsight 中搭配使用 Java UDF 和 Apache Hive

了解如何建立能配合 Apache Hive 使用的以 Java 為基礎的使用者定義函式 (UDF)。 此範例中的 Java UDF 會將文字字串的資料表轉換成全部小寫。

## <a name="prerequisites"></a>必要條件

* A Hadoop cluster on HDInsight. 請參閱[開始在 Linux 上使用 HDInsight](./apache-hadoop-linux-tutorial-get-started.md)。
* [Java Developer Kit (JDK) 第 8 版](https://aka.ms/azure-jdks)
* 根據 Apache 正確[安裝](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是適用於 Java 專案的專案建置系統。
* 您叢集主要儲存體的 [URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 This would be wasb:// for Azure Storage, abfs:// for Azure Data Lake Storage Gen2 or adl:// for Azure Data Lake Storage Gen1. 如果已對 Azure 儲存體啟用安全傳輸，URI 會是 `wasbs://`。  另請參閱[安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

* 文字編輯器或 Java IDE

    > [!IMPORTANT]  
    > 如果您是在 Windows 用戶端上建立 Python 檔案，就必須使用以 LF 做為行尾結束符號的編輯器。 如果您不確定編輯器是使用 LF 或 CRLF，請參閱[疑難排解](#troubleshooting)一節，以了解有關移除 CR 字元的步驟。

## <a name="test-environment"></a>Test environment

The environment used for this article was a computer running Windows 10.  The commands were executed in a command prompt, and the various files were edited with Notepad. Modify accordingly for your environment.

From a command prompt, enter the commands below to create a working environment:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>建立範例 Java UDF

1. Create a new Maven project by entering the following command:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    This command creates a directory named `exampleudf`, which contains the Maven project.

2. Once the project has been created, delete the `exampleudf/src/test` directory that was created as part of the project by entering the following command:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Open `pom.xml` by entering the command below:

    ```cmd
    notepad pom.xml
    ```

    Then replace the existing `<dependencies>` entry with the following XML:

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

4. Enter the command below to create and open a new file `ExampleUDF.java`:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Then copy and paste the java code below into the new file. Then close the file.

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

In the commands below, replace `sshuser` with the actual username if different. Replace `mycluster` with the actual cluster name.

1. Compile and package the UDF by entering the following command:

    ```cmd
    mvn compile package
    ```

    此命令會建置 UDF 並將它封裝到 `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` 檔案。

2. Use the `scp` command to copy the file to the HDInsight cluster by entering the following command:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Connect to the cluster using SSH by entering the following command:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. From the open SSH session, copy the jar file to HDInsight storage.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>從 Hive 使用 UDF

1. Start the Beeline client from the SSH session by entering the following command:

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

    This query selects the state from the table, convert the string to lower case, and then display them along with the unmodified name. 此輸出看起來類似下列文字：

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

When running the hive job, you may come across an error similar to the following text:

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
