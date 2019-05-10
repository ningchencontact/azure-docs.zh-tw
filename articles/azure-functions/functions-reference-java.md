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
ms.openlocfilehash: ce7eb546c342ffd20557a95d5293d83b39ec3afb
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507185"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 開發人員指南

Azure Functions 运行时支持 [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/)。

本指南包含有关使用 Java 编写 Azure Functions 的复杂性的信息。

Java 函数是一个 `public` 方法，使用注释 `@FunctionName` 修饰。 此方法定义 java 函数的条目，必须在给定包中独一无二。 

本文假設您已經讀過 [Azure Functions 開發人員參考](functions-reference.md)。 此外，应该完成有关如何使用 [Visual Studio Code](functions-create-first-function-vs-code.md) 或[使用 maven](functions-create-first-java-maven.md) 创建第一个函数的 Functions 快速入门。

## <a name="programming-model"></a>程式設計模型 

[觸發程序和繫結](functions-triggers-bindings.md)的概念是 Azure Functions 的基礎。 觸發程序會開始執行您的程式碼。 繫結可讓您將資料傳至函式以及從函式傳回資料，而不需要撰寫自訂的資料存取程式碼。

## <a name="folder-structure"></a>資料夾結構

以下是 Azure 函式 Java 專案的資料夾結構：

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

其中有一個可用來設定函數應用程式的共用 [host.json](functions-host-json.md) 檔案。 每個函式都有自己的程式碼檔案 (.java) 和繫結設定檔 (function.json)。

您可以在專案中放入多個函式。 請勿將函式放入個別的 jar。 目標目錄中的 FunctionApp 就是會部署至 Azure 中函數應用程式的項目。

## <a name="triggers-and-annotations"></a>觸發程序和註解

 Azure 函式可透過觸發程序 (例如，HTTP 要求、計時器或資料的更新) 來叫用。 函式必須處理該觸發程序和任何其他輸入，以產生一或多個輸出。

請使用 [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 套件中所包含的 Java 註釋，以將輸入和輸出繫結至方法。 如需詳細資訊，請參閱 < [Java 參考文件](/java/api/com.microsoft.azure.functions.annotation)。

> [!IMPORTANT] 
> 您必須在 [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) 中設定 Azure 儲存體帳戶，以在本機執行 Azure 儲存體 Blob、佇列或資料表觸發程序。

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

以下是 [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin) 所產生的對應 `function.json`：

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

請從 [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) 下載並使用 [Azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDK，以在本機開發 Java 函數應用程式。 當您將函數應用程式部署至雲端時，Azure Functions 使用 Azul Java 8 JDK 執行階段。

針對 JDK 和函數應用程式的問題，[Azure 支援](https://azure.microsoft.com/support/)可提供[完整的支援方案](https://azure.microsoft.com/support/plans/)。

## <a name="customize-jvm"></a>自訂 JVM

Functions 可讓您自訂 Java 虛擬機器 (JVM) 用來執行您的 Java 函式。 [下列 JVM 選項](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)預設會使用：

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

您可以提供額外的引數中的應用程式設定具名`JAVA_OPTS`。 您可以將應用程式設定加入您的函式應用程式部署至 Azure，在下列其中一種：

### <a name="azure-portal"></a>Azure 入口網站

在 [ [Azure 入口網站](https://portal.azure.com)，使用[應用程式設定] 索引標籤](functions-how-to-use-azure-function-app-settings.md#settings)加入`JAVA_OPTS`設定。

### <a name="azure-cli"></a>Azure CLI

[Az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings)命令可以用來設定`JAVA_OPTS`，如下列範例所示：

    ```azurecli-interactive
    az functionapp config appsettings set --name <APP_NAME> \
    --resource-group <RESOURCE_GROUP> \
    --settings "JAVA_OPTS=-Djava.awt.headless=true"
    ```
這個範例會啟用遠端控制模式。 取代`<APP_NAME>`函式應用程式的名稱和`<RESOURCE_GROUP> `與資源群組。

> [!WARNING]  
> 在中執行時[耗用量計劃](functions-scale.md#consumption-plan)，您必須新增`WEBSITE_USE_PLACEHOLDER`的值設定`0`。  
此設定會增加 Java 函式的冷啟動時間。

## <a name="third-party-libraries"></a>第三方程式庫 

Azure Functions 支援使用第三方程式庫。 根據預設，專案的 `pom.xml` 檔案中所指定的相依性全都會在 [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) 目標期間自動配套。 對於在 `pom.xml` 檔案中未指定為相依性的程式庫，請將其放入函式根目錄的 `lib` 目錄中。 放在 `lib` 目錄中的相依性會在執行階段新增至系統類別載入器。

`com.microsoft.azure.functions:azure-functions-java-library` 相依性預設會在 Classpath 提供，因此無須包含在 `lib` 目錄中。 此外，[azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) 會將[這裡](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies)列出的相依性新增至類別路徑。

## <a name="data-type-support"></a>資料類型支援

您可以使用 Plain Old Java Object (POJO)，`azure-functions-java-library` 或基本 dataTypes 中定義的類型 (例如字串、整數) 來繫結至輸入/輸出繫結。

### <a name="plain-old-java-objects-pojos"></a>純舊 Java 物件 (POJO)

為了將輸入資料轉換成 POJO，[azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) 會使用 [gson](https://github.com/google/gson) 程式庫。 作為函式輸入的 POJO 類型應為 `public`。

### <a name="binary-data"></a>二進位資料

藉由將 function.json 中的 `dataType` 欄位設定為 `binary`，將二進位輸入或輸出繫結至 `byte[]`：

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

如果預期為 Null 值，請使用 `Optional<T>`

## <a name="bindings"></a>繫結

輸入和輸出繫結提供從您的程式碼內連線到資料的宣告式方法。 一個函數可以有多個輸入和輸出繫結。

### <a name="example-input-binding"></a>範例輸入繫結

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

此函式透過 HTTP 要求來叫用。 
- HTTP 要求承載會傳入作為 引數 `inputReq` 的 `String`
- 系統會從 Azure 資料表儲存體中擷取一個項目，並傳入作為引數 `inputData` 的 `TestInputData`。

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

每當設定的事件中樞內有新資料時，就會觸發此函式。 由於 `cardinality` 設為 `MANY`，因此函式會從事件中樞接收訊息批次。 來自事件中樞的 EventData 會轉換成 `TestEventData` 供函式執行。

### <a name="example-output-binding"></a>範例輸出繫結

您可以使用 `$return` 將「輸出繫結」繫結至傳回值 

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

此函式 HttpRequest 上叫用，並寫入 Azure 佇列中的多個值。

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage 和 HttpResponseMessage

 定義於 `azure-functions-java-library` 中的 HttpRequestMessage 和 HttpResponseMessage 類型，是要與 HttpTrigger 函式搭配使用的協助程式類型

| 特殊類型      |       目標        | 一般使用方式                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 觸發程序     | 取得方法、標頭或查詢 |
| `HttpResponseMessage` | HTTP 輸出繫結 | 傳回 200 以外的狀態   |

## <a name="metadata"></a>中繼資料

有幾個觸發程序會將[觸發程序中繼資料](/azure/azure-functions/functions-triggers-bindings)與輸入資料一起傳送。 您可以使用註釋 `@BindingName` 繫結至觸發程序中繼資料


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
在上述範例中，`queryValue` 繫結至 Http 要求 URL `http://{example.host}/api/metadata?name=test` 中的查詢字串參數 `name`。 以下是另一個從佇列觸發程序中繼資料繫結至 `Id` 的範例

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
> 註釋中提供的名稱必須符合中繼資料屬性

## <a name="execution-context"></a>執行內容

定義於 `azure-functions-java-library` 中的 `ExecutionContext` 包含用來與函式執行階段進行通訊的協助程式方法。

### <a name="logger"></a>記錄器

使用 `ExecutionContext` 中定義的 `getLogger` 從函式程式碼寫入記錄。

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

請設定您的函式應用程式以使用 Azure CLI 寫入應用程式記錄：

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

若要使用 Azure CLI 串流 Function 應用程式的記錄輸出，請開啟新的命令列提示字元、Bash 或終端機工作階段，然後輸入下列命令：

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[az webapp log tail](/cli/azure/webapp/log) 命令有選項可使用 `--provider` 選項篩選輸出。 

若要使用 Azure CLI 將記錄檔做為單一 ZIP 檔案下載，請開啟新的命令列提示字元、Bash 或終端機工作階段，然後輸入下列命令：

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

您必須已啟用登入 Azure 入口網站或 Azure CLI，執行此命令之前的檔案系統。

## <a name="environment-variables"></a>環境變數

在 Functions 中，[應用程式設定](https://docs.microsoft.com/azure/azure-functions/functions-app-settings) (例如服務連接字串) 在執行期間會公開為環境變數。 您可以使用 `System.getenv("AzureWebJobsStorage")` 來存取這些設定

範例：

新增具有名稱 testAppSetting 和值 testAppSettingValue 的 [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings)

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>後續步驟

如需 Azure Function Java 開發的詳細資訊，請參閱以下資源：

* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 開發人員參考](functions-reference.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
* 使用 [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)[IntelliJ](functions-create-maven-intellij.md) 和 [Eclipse](functions-create-maven-eclipse.md) 進行本機開發與偵錯。 
* [使用 Visual Studio Code 針對 Java Azure Functions 進行遠端偵錯](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Azure Functions 適用的 Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) (英文) - 透過 `azure-functions:add` 目標建立串流函式，並針對 [ZIP 檔案開發](deployment-zip-push.md)準備暫存目錄。
