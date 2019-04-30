---
title: Apache Storm 範例 Java 拓撲 - Azure HDInsight
description: 了解如何藉由建立範例字數統計拓撲，以使用 Java 建立 Apache Storm 拓撲。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: apache storm,apache storm 範例,storm java,storm 拓撲範例
ms.service: hdinsight
ms.topic: conceptual
origin.date: 03/14/2019
ms.date: 04/29/2019
ms.author: v-yiso
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 43f68908c8549c2f1d8322b5c4ad3985618cfe6e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125165"
---
# <a name="create-an-apache-storm-topology-in-java"></a>在 Java 中建立 Apache Storm 拓撲

了解如何為 [Apache Storm](https://storm.apache.org/) 建立以 Java 為基礎的拓撲。 在此处，我们将创建一个实现单词计数应用程序的 Storm 拓扑。 您會使用 [Apache Maven](https://maven.apache.org/) 來建置和封裝專案。 然后，了解如何使用 [Apache Storm Flux](https://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) 框架定义拓扑。

完成這份文件中的步驟之後，您就可以將拓撲部署到 Apache Storm on HDInsight。

> [!NOTE]  
> 在此文件中建立的 Storm 拓樸範例完整版位於 [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount)。

## <a name="prerequisites"></a>必要條件

* [Java Developer Kit (JDK) 第 8 版](https://aka.ms/azure-jdks)

* 根據 Apache 正確[安裝](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是適用於 Java 專案的專案建置系統。

## <a name="test-environment"></a>测试环境
本文使用的环境是一台运行 Windows 10 的计算机。  命令在命令提示符下执行，各种文件使用记事本进行编辑。

在命令提示符下，输入以下命令以创建工作环境：

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>建立 Maven 專案

输入以下命令，创建名为 **WordCount** 的 Maven 项目：

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

此命令會在目前的位置建立名為 `WordCount` 的目錄，其內含基本 Maven 專案。 第二条命令将现有工作目录更改为 `WordCount`。 第三条命令创建稍后要使用的新目录 `resources`。  `WordCount` 目錄包含下列項目：

* `pom.xml`:包含適用於 Maven 專案的設定。
* `src\main\java\com\microsoft\example`:包含應用程式的程式碼。
* `src\test\java\com\microsoft\example`:包含應用程式的測試。  

### <a name="remove-the-generated-example-code"></a>移除所產生的範例程式碼

输入以下命令，删除生成的测试和应用程序文件 `AppTest.java` 与 `App.java`：

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>新增 Maven 存放庫

HDInsight 是以 Hortonworks Data Platform (HDP) 為基礎，所以我們建議使用 Hortonworks 存放庫來為您的 Apache Storm 專案下載相依性。  

输入以下命令打开 `pom.xml`：

```cmd
notepad pom.xml
```

然后，在 `<url> https://maven.apache.org</url>` 行的后面添加以下 XML：

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>加入屬性

Maven 可讓您定義稱為屬性的專案層級值。 在 `pom.xml` 中的 `</repositories>` 行后面添加以下文本：

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

現在，您可以在 `pom.xml` 的其他區段中使用此值。 例如，在指定 Storm 元件的版本時，您可以使用 `${storm.version}` 而不是將值硬式編碼。

## <a name="add-dependencies"></a>新增相依性

新增 Storm 元件的相依性。 在 `pom.xml` 的 `<dependencies>` 节中添加以下文本：

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

在編譯期間，Maven 會使用此資訊來查閱 Maven 存放庫中的 `storm-core`。 它會先查看本機電腦上的儲存機制。 如果檔案不存在，Maven 會從公用 Maven 存放庫進行下載，並將它們儲存在本機存放庫中。

> [!NOTE]  
> 请注意该部分中的 `<scope>provided</scope>` 行。 這項設定會指示 Maven 從所建立的任何 JAR 檔案中排除 **storm-core**，因為它是由系統所提供。

## <a name="build-configuration"></a>生成配置

Maven 外掛程式可讓您自訂專案的建置階段。 例如，如何編譯專案或如何將它封裝成 JAR 檔案。 在 `pom.xml` 中，紧靠在 `</project>` 行的上面添加以下文本：

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

此區段會用來新增外掛程式、資源，和其他組建組態選項。 有关 `pom.xml` 文件的完整参考，请参阅 [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html)。

### <a name="add-plug-ins"></a>新增外掛程式

* **Exec Maven 外掛程式**

    在以 Java 中實作的 Apache Storm 拓撲中，[Exec Maven 外掛程式](https://www.mojohaus.org/exec-maven-plugin/)十分有用，因為它可讓您輕鬆地在開發環境上以本機執行拓撲。 將下列內容加入 `pom.xml` 檔案的 `<plugins>` 區段，以包括 Exec Maven 外掛程式：
    
    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
            <goals>
                <goal>exec</goal>
            </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads> 
        </configuration>
    </plugin>
    ```

* **Apache Maven Compiler Plugin**

    另一個有用的外掛程式是 [Apache Maven 編譯器外掛程式](https://maven.apache.org/plugins/maven-compiler-plugin/)，其可用來變更編譯選項。 更改 Maven 用作应用程序源和目标的 Java 版本。
    
  * 針對 HDInsight __3.4 或更早版本__，請將資源和目標 Java 版本設為 __1.7__。
    
  * 針對 HDInsight __3.5__，請將來源和目標 Java 版本設為 __1.8__。
    
    在 `pom.xml` 檔案的 `<plugins>` 區段中新增下列文件，以包括 Apache Maven 編譯器外掛程式。 這個範例會指定 1.8，使得目標 HDInsight 版本為 3.5。
    
    ```xml
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
      <source>1.8</source>
      <target>1.8</target>
      </configuration>
    </plugin>
    ```

### <a name="configure-resources"></a>配置资源

resources 區段可讓您包含非程式碼資源，例如拓撲中元件所需的組態檔。 本示例在 `pom.xml` 文件的 `<resources>` 节中添加以下文本。

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

這個範例會將專案根目錄 (`${basedir}`) 中的 resources 目錄新增為包含資源的位置，並且包含稱為 `log4j2.xml` 的檔案。 此文件用于配置拓扑所要记录的信息。

## <a name="create-the-topology"></a>建立拓撲

Java 型 Apache Storm 拓撲包含三個您必須編寫 (或參考) 為相依性的元件。

* **Spout**：讀取來自外部來源的資料，並將資料流發出到拓撲。

* **Bolt**：執行 Spout 或其他 Bolt 所發出之串流的處理，並發出一或多個串流。

* **拓撲**：定義如何排列 Spout 和 Bolt，並提供拓撲的進入點。

### <a name="create-the-spout"></a>建立 Spout

若要減少設定外部資料來源的需求，下列 Spout 只會發出隨機的句子。 它是隨附於 [Storm-Starter 範例](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter)的 Spout 修正版。  雖然此拓撲只使用一個 Spout，但是其他拓撲可能會有將資料從不同來源送入拓撲的數個 Spout。

输入以下命令，以创建并打开新文件 `RandomSentenceSpout.java`：

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

将以下 Java 代码复制并粘贴到新文件中。  然后关闭该文件。

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> 如需從外部資料來源讀取之 Spout 的範例，請參閱下列其中一個範例：
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java)：從 Twitter 讀取的範例 Spout。
> * [Storm-kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka)：從 Kafka 讀取的 Spout。


### <a name="create-the-bolts"></a>建立 Bolt

Bolt 會處理資料的處理。 Bolt 可以包辦任何作業，例如計算、持續性或與外部元件交談。 此拓撲會使用兩個 Bolt：

* **SplitSentence**：將 **RandomSentenceSpout** 所發出的句子分割成個別字組。

* **WordCount**：計算每個字組的出現次數。


#### <a name="splitsentence"></a>SplitSentence

输入以下命令，以创建并打开新文件 `SplitSentence.java`：

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

将以下 Java 代码复制并粘贴到新文件中。  然后关闭该文件。

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

输入以下命令，以创建并打开新文件 `WordCount.java`：

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

将以下 Java 代码复制并粘贴到新文件中。  然后关闭该文件。

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>定義拓撲

拓撲會將 Spout 和 Bolt 一起繫結至圖形，其中定義元件之間的資料流動方式。 它还提供 Storm 在群集内创建组件的实例时使用的并行度提示。

以下映像是此拓撲之元件圖的基本圖。

![顯示 Spout 和 Bolt 排列的圖表](./media/apache-storm-develop-java-topology/wordcount-topology.png)

若要实现该拓扑，请输入以下命令，以创建并打开新文件 `WordCountTopology.java`：

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

将以下 Java 代码复制并粘贴到新文件中。  然后关闭该文件。

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>設定記錄

Storm 使用 [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) 來記錄資訊。 如果您未設定記錄，拓撲會發出診斷資訊。 若要控制所要记录的内容，请输入以下命令，在 `resources` 目录中创建名为 `log4j2.xml` 的文件：

```cmd
notepad resources\log4j2.xml
```

将以下 XML 文本复制并粘贴到新文件中。  然后关闭该文件。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
<Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT">
        <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
</Appenders>
<Loggers>
    <Logger name="com.microsoft.example" level="trace" additivity="false">
        <AppenderRef ref="STDOUT"/>
    </Logger>
    <Root level="error">
        <Appender-Ref ref="STDOUT"/>
    </Root>
</Loggers>
</Configuration>
```

此 XML 會設定 `com.microsoft.example` 類別的新記錄器，而該類別中會包含此範例拓撲的元件。 此記錄器的層級是設為 trace，這樣會擷取此拓撲中元件發出的任何記錄資訊。

`<Root level="error">` 區段會設定記錄的根層級 (不在 `com.microsoft.example` 中的所有項目)，只記錄錯誤資訊。

如需設定 Log4j 2 記錄的詳細資訊，請參閱 [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html)。

> [!NOTE]  
> Storm 0.10.0 和更新版本是使用 Log4j 2.x。 舊版的 Storm 使用 Log4j 1.x，它們使用不同格式的記錄設定。 如需有關舊版組態資訊，請參閱 [https://wiki.apache.org/logging-log4j/Log4jXmlFormat](https://wiki.apache.org/logging-log4j/Log4jXmlFormat)。

## <a name="test-the-topology-locally"></a>在本機測試拓撲

保存文件之后，请使用以下命令在本地测试拓扑。

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

它執行時，拓撲將顯示啟動資訊。 以下文本是单词计数输出的示例：

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

此範例記錄指出 '和' 這個字已發出 113 次。 只要拓扑运行，计数就会持续增加，因为 Spout 会连续发出相同的句子。

在發出單字和計算次數之間有 5 秒的間隔。 **WordCount** 元件設定為只在計時 Tuple 抵達時發出資訊。 它會要求計時 Tuple 每隔五秒才傳送。

## <a name="convert-the-topology-to-flux"></a>將拓撲轉換為 Flux

[Flux](https://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) 是可在 Storm 0.10.0 和更新版本中使用的新架構，可讓您區隔設定與實作。 您的元件仍會以 Java 定義，但拓撲則會使用 YAML 檔案來定義。 您可以將預設拓撲定義與您的專案一起封裝，或在提交拓撲時使用獨立檔案。 將拓撲提交至 Storm 時，您可以使用環境變數或組態檔來填入 YAML 拓撲定義中的值。

YAML 檔案會定義要用於拓撲的元件以及其間的資料流程。 您可以包含 YAML 檔案作為 jar 檔案的一部分，或者您可以使用外部 YAML 檔案。

如需有關 Flux 的詳細資訊，請參閱 [Flux 架構 (https://storm.apache.org/releases/1.0.6/flux.html)](https://storm.apache.org/releases/1.0.6/flux.html)。

> [!WARNING]  
> 由於發生與 Storm 1.0.1 有關的 [Bug (https://issues.apache.org/jira/browse/STORM-2055)](https://issues.apache.org/jira/browse/STORM-2055)，因此，您可能需要安裝 [Storm 開發環境](https://storm.apache.org/releases/current/Setting-up-development-environment.html)，以便在本機執行 Flux 拓撲。

1. 以前，`WordCountTopology.java` 会定义拓扑，但使用 Flux 时无需这样做。 使用以下命令删除该文件：

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

2. 输入以下命令，以创建并打开新文件 `topology.yaml`：

    ```cmd
    notepad resources\topology.yaml
    ```

    将以下文本复制并粘贴到新文件中。  然后关闭该文件。

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
      topology.workers: 1     # Hint for the number of workers to create

    spouts:                 # Spout definitions
    - id: "sentence-spout"
      className: "com.microsoft.example.RandomSentenceSpout"
      parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
      className: "com.microsoft.example.SplitSentence"
      parallelism: 1
        
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 10
      parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
      from: "sentence-spout"       # The stream emitter
      to: "splitter-bolt"          # The stream consumer
      grouping:                    # Grouping type
        type: SHUFFLE
    
    - name: "Splitter -> Counter"
      from: "splitter-bolt"
      to: "counter-bolt"
      grouping:
        type: FIELDS
        args: ["word"]           # field(s) to group on
    ```

3. 输入以下命令打开 `pom.xml`，并做出下面所述的修改：

    ```cmd
    notepad pom.xml
    ```

   * 在 `<dependencies>` 區段新增下列新的相依性︰

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   * 對 `<plugins>` 區段新增下列外掛程式。 此外掛程式會負責建立專案的封裝 (jar 檔案)，並在建立封裝時套用一些 Flux 特定的轉換。

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
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
        ```

   * 在 **exec-maven-plugin** `<configuration>` 节中，将 `<mainClass>` 的值从 `${storm.topology}` 更改为 `org.apache.storm.flux.Flux`。 此設定可讓 Flux 負責執行在開發時於本機執行的拓撲。

   * 将以下内容添加到 `<resources>` 节中的 `<includes>`。 此 XML 包括會將拓撲定義為專案一部分的 YAML 檔案。

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>在本機測試 Flux 拓撲

1. 输入以下命令，以使用 Maven 编译并执行 Flux 拓扑：

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > 如果您的拓撲使用 Storm 1.0.1 位元，此命令就會失敗。 此失敗是由 [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055) 所造成。 請改為[在開發環境中安裝 Storm](https://storm.apache.org/releases/current/Setting-up-development-environment.html)，並使用下列步驟：
    >
    > 如果您已[在開發環境中安裝 Storm](https://storm.apache.org/releases/current/Setting-up-development-environment.html)，您可以改用下列命令：
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    `--local` 参数在开发环境中以本地模式运行拓扑。 `-R /topology.yaml` 參數會使用 jar 檔案中的 `topology.yaml` 檔案資源來定義拓撲。

    它執行時，拓撲將顯示啟動資訊。 以下文字是輸出的範例：

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    記錄資訊的批次之間有 10 秒的延遲時間。

2. 基于项目创建新的拓扑 yaml。
 
    a. 输入以下命令打开 `topology.xml`：

    ```cmd
    notepad resources\topology.yaml
    ```

    b. 找到以下节，将 `10` 的值更改为 `5`。 此修改會將發出字數統計的批次之間的間隔從 10 秒變更為 5 秒。  

    ```yaml
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 5
      parallelism: 1  
    ```  

    c. 将文件另存为 `newtopology.yaml`。

3. 若要运行拓扑，请输入以下命令：

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    或者，如果您的開發環境上含有 Storm：

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

     此命令使用 `newtopology.yaml` 作为拓扑定义。 因為我們並未包含 `compile` 參數，Maven 會重複使用先前步驟中建置的專案版本。

    启动拓扑后，你将发现，发出批的间隔时间已更改，会反映 `newtopology.yaml` 中的值。 因此您可以看到，您可以透過 YAML 檔案變更組態，而不需要重新編譯拓撲。

如需Flux 架構的這些功能和其他功能的詳細資訊，請參閱 [Flux (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html)。

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) 是 Storm 提供的高階抽象概念。 它支援具狀態的處理。 Trident 的主要优点在于，它可以保证进入拓扑的每个消息只会处理一次。 若未使用 Trident，您的拓撲只能保證至少處理一次訊息。 還有其他差異，例如可供使用的內建元件，而不是建立 Bolt。 事實上，較不一般的元件 (例如篩選、投影和函數) 會取代 Bolt。

可以使用 Maven 專案來建立 Trident 應用程式。 您使用與本文稍早所呈現的相同基本步驟—只有程式碼不同。 Trident (目前) 也無法與 Flux 架構搭配使用。

有关 Trident 的详细信息，请参阅 [Trident API 概述](https://storm.apache.org/releases/current/Trident-API-Overview.html)。

## <a name="next-steps"></a>後續步驟

您已了解如何使用 Java 建立 Apache Storm 拓撲。 現在要了解如何：

* [部署和管理 HDInsight 上的 Apache Storm 拓撲](apache-storm-deploy-monitor-topology-linux.md)

* [使用 Visual Studio 開發 Apache Storm on HDInsight 的 C# 拓撲](apache-storm-develop-csharp-visual-studio-topology.md)

您可透過瀏覽 [HDInsight 上 Apache Storm 的範例拓撲](apache-storm-example-topology.md)找到更多範例 Apache Storm 拓撲。
