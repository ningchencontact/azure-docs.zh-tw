---
title: 使用 Apache Maven 來建置 Azure HDInsight 的 Java HBase 用戶端
description: 了解如何使用 Apache Maven 建置以 Java 為基礎的 Apache HBase 應用程式，然後將它部署至 Azure HDInsight 上的 HBase。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 04/16/2019
ms.openlocfilehash: a4c601e81390efa3bb53a6f07225bb6e939bc9bb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726451"
---
# <a name="build-java-applications-for-apache-hbase"></a>建置 Apache HBase 的 Java 應用程式

了解如何在 Java 中建立 [Apache HBase](https://hbase.apache.org/) 應用程式。 然後使用此應用程式搭配 Azure HDInsight 上的 HBase。

此文件中的步驟使用 [Apache Maven](https://maven.apache.org/) \(英文\) 來建立及建置專案。 Maven是軟體專案管理和理解工具，可讓您建置 Java 專案的軟體、文件及報告。

## <a name="prerequisites"></a>必要條件

* 在 HDInsight 上 Apache HBase 叢集。 請參閱[開始使用 Apache HBase](./apache-hbase-tutorial-get-started-linux.md)。

* [Java Developer Kit (JDK) 第 8 版](https://aka.ms/azure-jdks)。

* 根據 Apache 正確[安裝](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是適用於 Java 專案的專案建置系統。

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

* 如果使用 PowerShell，您必須[AZ 模組](https://docs.microsoft.com/powershell/azure/overview)。

* 文字編輯器。 這篇文章會使用 「 Microsoft 記事本 」。

> [!IMPORTANT]  
> Azure PowerShell cmdlet [Get AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster)並[Get AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput)目前無法運作時[安全傳輸](../../storage/common/storage-require-secure-transfer.md)啟用儲存體帳戶.

## <a name="test-environment"></a>测试环境
本文使用的环境是一台运行 Windows 10 的计算机。  命令在命令提示符下执行，各种文件使用记事本进行编辑。 據此修改為您的環境。

在命令提示符下，输入以下命令以创建工作环境：

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>建立 Maven 專案

1. 輸入下列命令來建立名為的 Maven 專案**hbaseapp**:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    此命令會在目前的位置建立名為 `hbaseapp` 的目錄，其內含基本 Maven 專案。 第二個命令將工作目錄變更至`hbaseapp`。 第三条命令创建稍后要使用的新目录 `conf`。 `hbaseapp` 目錄包含下列項目：

    * `pom.xml`:「專案物件模型」(](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)POM) 包含用來建置專案的資訊和組態詳細資料。
    * `src\main\java\com\microsoft\examples`:包含應用程式的程式碼。
    * `src\test\java\com\microsoft\examples`:包含應用程式的測試。

2. 移除產生的範例程式碼。 输入以下命令，删除生成的测试和应用程序文件 `AppTest.java` 与 `App.java`：

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>更新專案物件模型

Pom.xml 檔案的完整參考，請參閱 https://maven.apache.org/pom.html。  输入以下命令打开 `pom.xml`：

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>新增相依性

在 `pom.xml` 的 `<dependencies>` 节中添加以下文本：

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

此區段指出專案需要 **hbase-client** 和 **phoenix-core** 元件。 在編譯期間，會從預設的 Maven 儲存機制下載這些相依性。 您可以使用 [Maven 中央儲存機制搜尋](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) ，進一步了解此相依性的詳細資訊。

> [!IMPORTANT]  
> hbase-client 的版本號碼必須符合隨附於 HDInsight 叢集的 Apache HBase 版本。 您可以使用下表來尋找正確的版本號碼。

| HDInsight 叢集版本 | 要使用的 Apache HBase 版本 |
| --- | --- |
| 3.6 | 1.1.2 |
| 4.0 | 2.0.0 |

如需有關 HDInsight 版本和元件的詳細資訊，請參閱 [HDInsight 隨附哪些不同的 Apache Hadoop 元件](../hdinsight-component-versioning.md)。

### <a name="build-configuration"></a>建置組態

Maven 外掛程式可讓您自訂專案的建置階段。 此區段會用來新增外掛程式、資源，和其他組建組態選項。

將下列程式碼加入`pom.xml`檔案，然後儲存並關閉檔案。 此文字必須位在檔案中的 `<project>...</project>` 標籤內，例如在 `</dependencies>` 和 `</project>` 之間。

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
    <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
        <include>hbase-site.xml</include>
        </includes>
    </resource>
    </resources>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
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

此區段會設定包含 HBase 組態資訊的資源(`conf/hbase-site.xml`)。

> [!NOTE]  
> 您也可以透過程式碼來設定組態值。 請參閱 `CreateTable` 範例中的註解。

此區段也會設定 [Apache Maven Compiler 外掛程式](https://maven.apache.org/plugins/maven-compiler-plugin/)和 [Apache Maven Shade 外掛程式](https://maven.apache.org/plugins/maven-shade-plugin/)。 Compiler 外掛程式用來編譯拓撲。 该阴影插件用于防止在由 Maven 构建的 JAR 程序包中复制许可证。 此外掛程式是用於防止 HDInsight 叢集在執行階段發生「重複的授權檔案」錯誤。 使用 maven-shade-plugin 搭配 `ApacheLicenseResourceTransformer` 實作可防止此錯誤。

maven-shade-plugin 也會產生 uber jar，其中含有應用程式需要的所有相依性。

### <a name="download-the-hbase-sitexml"></a>下載 hbase-site.xml

使用下列命令將 HBase 組態，從 HBase 叢集複製到 `conf` 目錄。 取代`CLUSTERNAME`與您的 HDInsight 叢集名稱，然後輸入命令：

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>建立應用程式

### <a name="implement-a-createtable-class"></a>實作 CreateTable 類別

輸入下面的命令來建立並開啟新的檔案`CreateTable.java`。 選取 **是**在提示字元中，以建立新的檔案。

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

将以下 Java 代码复制并粘贴到新文件中。 然后关闭该文件。

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class CreateTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Example of setting zookeeper values for HDInsight
    // in code instead of an hbase-site.xml file
    //
    // config.set("hbase.zookeeper.quorum",
    //            "zookeepernode0,zookeepernode1,zookeepernode2");
    //config.set("hbase.zookeeper.property.clientPort", "2181");
    //config.set("hbase.cluster.distributed", "true");
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

    // create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // create the table...
    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
    // ... with two column families
    tableDescriptor.addFamily(new HColumnDescriptor("name"));
    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
    admin.createTable(tableDescriptor);

    // define some people
    String[][] people = {
        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
        { "4", "Rae", "Schroeder", "rae@contoso.com" },
        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

    HTable table = new HTable(config, "people");

    // Add each person to the table
    //   Use the `name` column family for the name
    //   Use the `contactinfo` column family for the email
    for (int i = 0; i< people.length; i++) {
        Put person = new Put(Bytes.toBytes(people[i][0]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
        person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
        table.put(person);
    }
    // flush commits and close the table
    table.flushCommits();
    table.close();
    }
}
```

此程式碼`CreateTable`類別，這會建立一個名為資料表`people`並填入一些預先定義的使用者。

### <a name="implement-a-searchbyemail-class"></a>實作 SearchByEmail 類別

輸入下面的命令來建立並開啟新的檔案`SearchByEmail.java`。 選取 **是**在提示字元中，以建立新的檔案。

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

将以下 Java 代码复制并粘贴到新文件中。 然后关闭该文件。

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.filter.RegexStringComparator;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.util.GenericOptionsParser;

public class SearchByEmail {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Use GenericOptionsParser to get only the parameters to the class
    // and not all the parameters passed (when using WebHCat for example)
    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
    if (otherArgs.length != 1) {
        System.out.println("usage: [regular expression]");
        System.exit(-1);
    }

    // Open the table
    HTable table = new HTable(config, "people");

    // Define the family and qualifiers to be used
    byte[] contactFamily = Bytes.toBytes("contactinfo");
    byte[] emailQualifier = Bytes.toBytes("email");
    byte[] nameFamily = Bytes.toBytes("name");
    byte[] firstNameQualifier = Bytes.toBytes("first");
    byte[] lastNameQualifier = Bytes.toBytes("last");

    // Create a regex filter
    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
    // Attach the regex filter to a filter
    //   for the email column
    SingleColumnValueFilter filter = new SingleColumnValueFilter(
        contactFamily,
        emailQualifier,
        CompareOp.EQUAL,
        emailFilter
    );

    // Create a scan and set the filter
    Scan scan = new Scan();
    scan.setFilter(filter);

    // Get the results
    ResultScanner results = table.getScanner(scan);
    // Iterate over results and print  values
    for (Result result : results ) {
        String id = new String(result.getRow());
        byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
        String firstName = new String(firstNameObj);
        byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
        String lastName = new String(lastNameObj);
        System.out.println(firstName + " " + lastName + " - ID: " + id);
        byte[] emailObj = result.getValue(contactFamily, emailQualifier);
        String email = new String(emailObj);
        System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
    }
    results.close();
    table.close();
    }
}
```

`SearchByEmail`類別可用來查詢資料列的電子郵件地址。 因為此類別使用規則運算式篩選器，您可以在使用此類別時提供字串或規則運算式。

### <a name="implement-a-deletetable-class"></a>實作 DeleteTable 類別

輸入下面的命令來建立並開啟新的檔案`DeleteTable.java`。 選取 **是**在提示字元中，以建立新的檔案。

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

将以下 Java 代码复制并粘贴到新文件中。 然后关闭该文件。

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;

public class DeleteTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // Disable, and then delete the table
    admin.disableTable("people");
    admin.deleteTable("people");
    }
}
```

`DeleteTable`類別會清理此範例中停用並卸除所建立的資料表建立的 HBase 資料表`CreateTable`類別。

## <a name="build-and-package-the-application"></a>建置和封裝應用程式

1. 從 `hbaseapp` 目錄，使用下列命令來建置含有應用程式的 JAR 檔案：

    ```cmd
    mvn clean package
    ```

    此命令會建置應用程式並封裝到 .jar 檔案中。

2. 命令完成時，`hbaseapp/target` 目錄就會包含名為 `hbaseapp-1.0-SNAPSHOT.jar` 的檔案。

   > [!NOTE]  
   > `hbaseapp-1.0-SNAPSHOT.jar` 檔案是 uber jar。 它包含執行應用程式需要的所有相依性。

## <a name="upload-the-jar-and-run-jobs-ssh"></a>上傳 JAR 並執行作業 (SSH)

下列步驟使用 `scp`，將 JAR 複製到 HDInsight 叢集上 Apache HBase 的主要前端節點。 接著，會使用 `ssh` 命令連接到該叢集並直接在前端節點上執行範例。

1. 將 jar 上傳到叢集。 取代`CLUSTERNAME`與您的 HDInsight 叢集名稱，然後輸入下列命令：

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. 連接到 HBase 叢集。 取代`CLUSTERNAME`與您的 HDInsight 叢集名稱，然後輸入下列命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

 3. 若要建立 HBase 資料表使用的 Java 應用程式，請使用下列命令，在您開啟 ssh 連線：

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    此命令會建立名為 **people** 的 HBase 資料表，並填入資料。

4. 若要搜尋資料表中儲存的電子郵件地址，請使用以下命令：

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    您會收到下列結果：

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. 若要刪除資料表，請使用下列命令：

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>上傳 JAR 並執行作業 (PowerShell)

下列步驟使用 Azure PowerShell [AZ 模組](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)將 JAR 上傳至您的 Apache HBase 叢集預設儲存體。 接著，會使用 HDInsight Cmdlet 從遠端執行範例。

1. 安裝和設定 AZ 模組中，建立名為的檔案之後`hbase-runner.psm1`。 使用下列文字做為此檔案的內容：

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    此檔案包含兩個模組：

   * **Add-HDInsightFile** - 用來將檔案上傳到叢集
   * **Start-HBaseExample** - 用來執行稍早建立的類別

2. 儲存`hbase-runner.psm1`檔案中`hbaseapp`目錄。

3. 使用 Azure PowerShell 註冊模組。 開啟新的 Azure PowerShell 視窗，並編輯下列命令取代`CLUSTERNAME`您叢集的名稱。 然後輸入下列命令：

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. 使用下列命令將 `hbaseapp-1.0-SNAPSHOT.jar` 上傳到您的叢集。

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    出現提示時，輸入叢集登入 (admin) 名稱和密碼。 此命令會將 `hbaseapp-1.0-SNAPSHOT.jar` 上傳至您叢集的主要儲存體中的 `example/jars` 位置。

5. 若要使用 `hbaseapp` 建立資料表，請使用下列命令：

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    出現提示時，輸入叢集登入 (admin) 名稱和密碼。

    此命令會在您 HDInsight 叢集上的 HBase 中建立名為 **people** 的資料表。 此命令不會在主控台視窗中顯示任何輸出。

6. 若要在資料表中搜尋項目，請使用下列命令：

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    出現提示時，輸入叢集登入 (admin) 名稱和密碼。

    此命令會使用 `SearchByEmail` 類別來搜尋 `contactinformation` 資料行系列和 `email` 資料行包含字串 `contoso.com` 的任何資料列。 您應該會得到下列結果：

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    使用 **fabrikam.com** 做為 `-emailRegex` 值會傳回電子郵件欄位中含有 **fabrikam.com** 的使用者。 您也可以使用規則運算式作為搜尋字詞。 例如，**^r** 會傳回開頭為字母 'r' 的電子郵件地址。

7. 若要刪除資料表，請使用下列命令：

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>使用 Start-HBaseExample 時沒有結果或傳回非預期的結果

請使用 `-showErr` 參數，以檢視執行工作時所產生的標準錯誤 (STDERR)。

## <a name="next-steps"></a>後續步驟

[了解如何搭配 Apache HBase 使用 SQuirreL SQL](apache-hbase-phoenix-squirrel-linux.md)
