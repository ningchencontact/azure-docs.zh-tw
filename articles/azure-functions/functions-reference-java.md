---
title: 適用於 Azure Functions 的 Java 開發人員參考 | Microsoft Docs
description: 了解如何使用 Java 開發函式。
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構, Java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: acd873cd19cafb785f968fd3d8671640bcfafed8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163711"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 開發人員指南

Azure Functions 執行階段支援[Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/)。 本指南包含撰寫 Java 的 Azure Functions 的錯綜複雜的相關資訊。

Java 函式是`public`方法，以註釋裝飾`@FunctionName`。 這個方法定義是 Java 函式中，項目，而且必須是唯一在特定的封裝。 

本文假設您已經讀過 [Azure Functions 開發人員參考](functions-reference.md)。 您應該也會完成藉由建立您的第一個函式，Functions 快速入門[Visual Studio Code](functions-create-first-function-vs-code.md)或是[Maven](functions-create-first-java-maven.md)。

## <a name="programming-model"></a>程式設計模型 

[觸發程序和繫結](functions-triggers-bindings.md)的概念是 Azure Functions 的基礎。 觸發程序會開始執行您的程式碼。 繫結可讓您將資料傳至函式以及從函式傳回資料，而不需要撰寫自訂的資料存取程式碼。

## <a name="folder-structure"></a>資料夾結構

以下是 Azure Functions 的 Java 專案的資料夾結構：

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

您可以使用共用[host.json](functions-host-json.md)檔案來設定函數應用程式。 每個函式都有自己的程式碼檔案 (.java) 和繫結設定檔 (function.json)。

您可以在專案中放入多個函式。 請勿將函式放入個別的 jar。 `FunctionApp`目標中目錄是您在 Azure 中的函式應用程式以取得部署的內容。

## <a name="triggers-and-annotations"></a>觸發程序和註解

 函式會叫用觸發程序，例如 HTTP 要求、 計時器或資料的更新。 您的函式必須處理該觸發程序，以及任何其他輸入，產生一或多個輸出。

請使用 [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 套件中所包含的 Java 註釋，以將輸入和輸出繫結至方法。 如需詳細資訊，請參閱 < [Java 參考文件](/java/api/com.microsoft.azure.functions.annotation)。

> [!IMPORTANT] 
> 您必須設定的 Azure 儲存體帳戶中您[local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file)在本機執行 Azure Blob 儲存體、 Azure 佇列儲存體或 Azure 資料表儲存體觸發程序。

範例：

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

以下是產生的對應`function.json`所[azure 函式-maven 外掛程式](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>JDK 執行階段可用性和支援 

對於本機的 Java 函式應用程式開發，下載並使用[適用於 Azure 的 Azul Zulu 企業](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf)從 Java 8 Jdk [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/)。 當您將函數應用程式部署至雲端時，Azure Functions 使用 Azul Java 8 JDK 執行階段。

[Azure 支援](https://azure.microsoft.com/support/)問題的 Jdk 和函式應用程式是適用於[限定的支援計劃](https://azure.microsoft.com/support/plans/)。

## <a name="customize-jvm"></a>自訂 JVM

Functions 可讓您自訂 Java 虛擬機器 (JVM) 用來執行您的 Java 函式。 [下列 JVM 選項](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)預設會使用：

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

您可以提供額外的引數中的應用程式設定具名`JAVA_OPTS`。 您可以將應用程式設定加入您在 Azure 入口網站或 Azure CLI 部署至 Azure 的函式應用程式。

### <a name="azure-portal"></a>Azure 入口網站

在 [ [Azure 入口網站](https://portal.azure.com)，使用[應用程式設定] 索引標籤](functions-how-to-use-azure-function-app-settings.md#settings)加入`JAVA_OPTS`設定。

### <a name="azure-cli"></a>Azure CLI

您可以使用[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings)命令來設定`JAVA_OPTS`，如下列範例所示：

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
這個範例會啟用遠端控制模式。 取代`<APP_NAME>`函式應用程式的名稱和`<RESOURCE_GROUP>`與資源群組。

> [!WARNING]  
> 在[耗用量計劃](functions-scale.md#consumption-plan)，您必須新增`WEBSITE_USE_PLACEHOLDER`的值設定`0`。  
此設定會增加 Java 函式的冷啟動時間。

## <a name="third-party-libraries"></a>第三方程式庫 

Azure Functions 支援使用第三方程式庫。 根據預設，所有相依性專案中的指定`pom.xml`檔案會自動組合期間[ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage)目標。 對於在 `pom.xml` 檔案中未指定為相依性的程式庫，請將其放入函式根目錄的 `lib` 目錄中。 相依性放在`lib`目錄會加入至系統類別載入器在執行階段。

`com.microsoft.azure.functions:azure-functions-java-library`依預設，在 classpath 提供相依性，並不需要包含在`lib`目錄。 此外， [azure 函式-java 工作者](https://github.com/Azure/azure-functions-java-worker)加入列出的相依性[這裡](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies)至 classpath。

## <a name="data-type-support"></a>資料類型支援

您可以使用純舊 Java 物件 (Pojo)、 型別定義中`azure-functions-java-library`，或基本資料類型，例如字串和整數繫結，來輸入或輸出繫結。

### <a name="pojos"></a>Pojo

將輸入的資料轉換成 POJO， [azure 函式-java 工作者](https://github.com/Azure/azure-functions-java-worker)會使用[gson](https://github.com/google/gson)程式庫。 作為函式輸入的 POJO 類型應為 `public`。

### <a name="binary-data"></a>二進位資料

二進位輸入或輸出繫結`byte[]`，藉由設定`dataType`欄位來將 function.json 中`binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

如果您預期的 null 值，使用`Optional<T>`。

## <a name="bindings"></a>繫結

輸入和輸出繫結提供從您的程式碼內連線到資料的宣告式方法。 一個函數可以有多個輸入和輸出繫結。

### <a name="input-binding-example"></a>輸入繫結範例

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

您叫用此函式，透過 HTTP 要求。 
- 被當做 HTTP 要求承載`String`引數`inputReq`。
- 一個項目會從資料表儲存體，而且會當做`TestInputData`的引數`inputData`。

若要接收的輸入批次，您可以繫結至`String[]`， `POJO[]`， `List<String>`，或`List<POJO>`。

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

每當設定的事件中樞內有新資料時，就會觸發此函式。 因為`cardinality`設為`MANY`，函式會從事件中樞接收一批訊息。 `EventData` 從事件中樞取得轉換成`TestEventData`函式執行。

### <a name="output-binding-example"></a>輸出繫結範例

您可以使用繫結的輸出繫結至傳回值`$return`。 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

如果有多個輸出繫結，請只對其中一個使用傳回值。

若要傳送多個輸出值，請使用 `azure-functions-java-library` 套件中定義的 `OutputBinding<T>`。 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

您叫用此函式上 HttpRequest。 它會將多個值寫入至佇列儲存體中。

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage 和 HttpResponseMessage

 這些定義在`azure-functions-java-library`。 它們都是使用 HttpTrigger 函式的協助程式類型。

| 特製化的型別      |       目標        | 典型的使用方式                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 觸發程序     | 取得方法、 標頭或查詢 |
| `HttpResponseMessage` | HTTP 輸出繫結 | 會傳回 200 以外的狀態   |

## <a name="metadata"></a>中繼資料

有幾個觸發程序會將[觸發程序中繼資料](/azure/azure-functions/functions-triggers-bindings)與輸入資料一起傳送。 您可以使用註解`@BindingName`繫結至觸發程序中繼資料。


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
在上述範例中，`queryValue`繫結至查詢字串參數`name`在 http 要求 URL 中， `http://{example.host}/api/metadata?name=test`。 以下是另一個範例，示範如何將繫結至`Id`從佇列觸發程序中繼資料。

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> 附註中提供的名稱必須符合中繼資料屬性。

## <a name="execution-context"></a>執行內容

`ExecutionContext`定義於`azure-functions-java-library`，包含與 functions 執行階段進行通訊的 helper 方法。

### <a name="logger"></a>記錄器

使用`getLogger`，其定義於`ExecutionContext`，以從函式程式碼寫入記錄檔。

範例：

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>檢視記錄和追蹤

您可以使用 Azure CLI 來串流 Java stdout 和 stderr 記錄，以及其他應用程式記錄。 

以下是如何設定您的函式應用程式，以使用 Azure CLI 來撰寫應用程式記錄：

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

串流記錄輸出函式應用程式使用 Azure CLI，請開啟新的命令提示字元、 Bash 或終端機工作階段，並輸入下列命令：

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[Az webapp log tail](/cli/azure/webapp/log)命令有選項可讓使用篩選輸出`--provider`選項。 

若要下載為同一個 ZIP 檔案的記錄檔，使用 Azure CLI，開啟新的命令提示字元、 Bash 或終端機工作階段，並輸入下列命令：

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

您必須已啟用登入 Azure 入口網站或 Azure CLI，執行此命令之前的檔案系統。

## <a name="environment-variables"></a>環境變數

在 Functions 中，[應用程式設定](https://docs.microsoft.com/azure/azure-functions/functions-app-settings) (例如服務連接字串) 在執行期間會公開為環境變數。 您可以使用，來存取這些設定`System.getenv("AzureWebJobsStorage")`。

比方說，您可以新增[AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings)，以名稱`testAppSetting`的值`testAppSettingValue`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>後續步驟

如需有關 Azure Functions Java 開發的詳細資訊，請參閱下列資源：

* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 開發人員參考](functions-reference.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
* 本機開發和使用的偵錯[Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)， [IntelliJ](functions-create-maven-intellij.md)，和[Eclipse](functions-create-maven-eclipse.md)
* [使用 Visual Studio Code 針對 Java Azure Functions 進行遠端偵錯](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [適用於 Azure Functions 的 maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* 簡化透過函式建立程序`azure-functions:add`目標，並準備的暫存目錄[ZIP 檔案部署](deployment-zip-push.md)。
