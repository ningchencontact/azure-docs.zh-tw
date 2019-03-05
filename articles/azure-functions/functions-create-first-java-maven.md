---
title: 使用 Java 和 Maven 在 Azure 中建立第一個函式 | Microsoft Docs
description: 使用 Java 和 Maven 建立簡單的 HTTP 觸發函式並發佈到 Azure。
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, 函式, 事件處理, 計算, 無伺服器架構
ms.service: azure-functions
ms.devlang: java
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: a72d6b180db35f3e0f0e0527e8ae0f544a585b25
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822956"
---
# <a name="create-your-first-function-with-java-and-maven"></a>使用 Java 和 Maven 建立您的第一個函式

本文會引導您使用 Maven 命令列工具來建置 Java 函式並發佈至 Azure Functions。 當您完成時，您的函式程式碼會在 Azure 中的[取用方案](functions-scale.md#consumption-plan)上執行，並且可使用 HTTP 要求來觸發。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

若要使用 Java 開發函式，您必須安裝下列項目：

- [Java Developer Kit](https://www.azul.com/downloads/zulu/)第 8 版。
- [Apache Maven](https://maven.apache.org) 3.0 版或更高版本。
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Azure Functions Core Tools](functions-run-local.md#v2) (需要 **.NET Core 2.x SDK**)

> [!IMPORTANT]
> JAVA_HOME 環境變數必須設定為 JDK 的安裝位置，才能完成本快速入門。

## <a name="generate-a-new-functions-project"></a>產生新的 Functions 專案

在空的資料夾中，執行下列命令以從 [Maven 原型](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) \(英文\) 產生 Functions 專案。

### <a name="linuxmacos"></a>Linux/MacOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="windows-cmd"></a>Windows (CMD)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

Maven 會要求您提供完成產生專案所需的值。 如需 _groupId_、_artifactId_ 和 _version_ 值，請參閱 [Maven 命名慣例](https://maven.apache.org/guides/mini/guide-naming-conventions.html) \(英文\) 參考。 _appName_ 值必須在整個 Azure 中是唯一的，Maven 才能根據先前輸入的 _artifactId_ 作為預設值產生應用程式名稱。 _packageName_ 值決定產生的函式程式碼的 Java 封裝。

以下的 `com.fabrikam.functions` 和 `fabrikam-functions` 識別碼作為範例使用，讓本快速入門中的步驟更容易閱讀。 建議您在此步驟中提供自己的值給 Maven。

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

Maven 會以 _artifactId_ 名稱在新資料夾中建立專案檔案，在此例中為 `fabrikam-functions`。 在本專案中所產生且準備要執行的程式碼是一個簡單的 [HTTP 觸發](/azure/azure-functions/functions-bindings-http-webhook)函式，能回應要求的本文：

```java
public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage<String> hello(
            @HttpTrigger(name = "req", methods = {"get", "post"}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponse(400, "Please pass a name on the query string or in the request body");
        } else {
            return request.createResponse(200, "Hello, " + name);
        }
    }
}

```

## <a name="run-the-function-locally"></a>在本機執行函式

將目錄變更為新建立的專案資料夾，並且使用 Maven 建置和執行函式：

```
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> 如果您遇到下列例外狀況：使用 Java 9 時的 `javax.xml.bind.JAXBException`，請參閱 [GitHub](https://github.com/jOOQ/jOOQ/issues/6477) 上的因應措施。

當函式在您的系統本機執行並準備好回應 HTTP 要求時，您會看到以下輸出：

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

在新的終端機視窗中使用 curl 從命令列觸發函式：

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

在終端機中使用 `Ctrl-C` 可停止函式程式碼。

## <a name="deploy-the-function-to-azure"></a>將函式部署到 Azure

部署到 Azure Functions 的程序從 Azure CLI 使用帳戶認證。 繼續執行前，請[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

```azurecli
az login
```

使用 `azure-functions:deploy` Maven 目標將您的程式碼部署到新的函式應用程式。

```
mvn azure-functions:deploy
```

部署完成時，您會看到可用來存取 Azure 函式應用程式的 URL：

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

使用 `cURL` 測試在 Azure 上執行的函式應用程式。 您必須變更下面範例中的 URL，以符合上一個步驟中自有函式應用程式的已部署 URL。

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>進行變更並重新部署

在產生的專案中編輯 `src/main.../Function.java` 來源檔案，以改變您的 Function 應用程式所傳回的文字。 將這一行：

```java
return request.createResponse(200, "Hello, " + name);
```

變更為下列內容：

```java
return request.createResponse(200, "Hi, " + name);
```

儲存變更，然後一如往常從終端機執行 `azure-functions:deploy` 來重新部署。 函數應用程式將會更新，而此要求：

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

將有經更新的輸出：

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>後續步驟

您已經建立一個包含簡單 HTTP 觸發程序的 Java 函式應用程式，並部署到 Azure Functions。

- 檢閱 [Java 函式開發人員指南](functions-reference-java.md)以了解開發 Java 函式的詳細資訊。
- 使用 `azure-functions:add`Maven 目標，將具有不同觸發程序的其他函式新增至您的專案。
- 使用 [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions) (英文)、[IntelliJ](functions-create-maven-intellij.md) 和 [Eclipse](functions-create-maven-eclipse.md)，在本機撰寫函式並進行偵錯。 
- 使用 Visual Studio Code 對部署在 Azure 中的函式進行偵錯。 請參閱 Visual Studio Code [無伺服器 Java 應用程式](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) (英文) 文件中的指示。
