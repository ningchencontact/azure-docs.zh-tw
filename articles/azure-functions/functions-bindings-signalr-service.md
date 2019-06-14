---
title: Azure Functions SignalR Service 繫結
description: 了解如何搭配使用 SignalR Service 繫結與 Azure Functions。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
editor: ''
tags: ''
keywords: azure functions, 函數, 事件處理, 動態運算, 無伺服器架構
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: f0d4a607676285ed4f0f91d8ce8c83ddf1313b89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64511148"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>適用於 Azure Functions 的 SignalR Service 繫結

本文說明如何藉由在 Azure Functions 中使用 SignalR Service 繫結，來驗證及傳送即時訊息給連線至 [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) 的用戶端。 Azure Functions 支援 SignalR Service 的輸入和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>套件 - Functions 2.x

提供 SignalR 服務繫結[Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) 1.* 版的 NuGet 套件。 套件的原始程式碼位於 [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub 存放庫中。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java 註釋

若要使用 Java 函式中的 SignalR 服務註解，您需要新增相依性*azure-函式-java-程式庫-signalr*到您的 pom.xml 的成品 （版本 1.0 或更新版本）。

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> 若要在 Java 中使用 SignalR Service 繫結，請確定您使用的是 2.4.419 版或更新版本的 Azure Functions Core Tools (主機版本 2.0.12332)。

## <a name="using-signalr-service-with-azure-functions"></a>使用 Azure Functions 中的 SignalR 服務

如需如何設定，並同時使用 SignalR 服務和 Azure Functions 的詳細資訊，請參閱[Azure Functions 開發和使用 Azure SignalR 服務的組態](../azure-signalr/signalr-concept-serverless-development-config.md)。

## <a name="signalr-connection-info-input-binding"></a>SignalR 連線資訊輸入繫結

在用戶端可以連線到 Azure SignalR Service 之前，它必須擷取服務端點 URL 和有效的存取權杖。 SignalRConnectionInfo  輸入繫結會產生 SignalR Service 端點 URL 和有效的存取權杖，可用來連線到服務。 因為權杖是限時的，且可用來驗證連線的特定使用者，所以您不應該快取權杖或者在用戶端之間共用權杖。 使用此繫結的 HTTP 觸發程序可以供用戶端用來擷取連線資訊。

請參閱特定語言的範例：

* [2.x C#](#2x-c-input-examples)
* [2.x JavaScript](#2x-javascript-input-examples)
* [2.x Java](#2x-java-input-examples)

如需有關如何使用此繫結來建立可由 SignalR 用戶端 SDK 的 「 交涉 」 函式的詳細資訊，請參閱 < [Azure Functions 開發及設定文件](../azure-signalr/signalr-concept-serverless-development-config.md)SignalR 服務概念文件。

### <a name="2x-c-input-examples"></a>2.xC#輸入範例

下列範例示範 [C# 函式](functions-dotnet-class-library.md)，該函式會使用輸入繫結來取得 SignalR 連線資訊，並且透過 HTTP 傳回。

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>已驗證權杖

如果函式是由已驗證的用戶端觸發，您可以將使用者識別碼宣告新增至產生的權杖。 您可以輕鬆加入函式應用程式使用的驗證[App Service 驗證](../app-service/overview-authentication-authorization.md)。

App Service 驗證會設定名為 `x-ms-client-principal-id` 和 `x-ms-client-principal-name` 的 HTTP 標頭，這些標頭分別包含已驗證使用者的用戶端主體識別碼和名稱。 您可以使用[繫結運算式](./functions-bindings-expressions-patterns.md)：`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}`，將繫結的 `UserId` 屬性設為任一標頭的值。 

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-examples"></a>2.x JavaScript 輸入的範例

下列範例示範 function.json  檔案中的 SignalR 連線資訊輸入繫結，以及使用繫結來傳回連線資訊的 [JavaScript 函式](functions-reference-node.md)。

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>已驗證權杖

如果函式是由已驗證的用戶端觸發，您可以將使用者識別碼宣告新增至產生的權杖。 您可以輕鬆加入函式應用程式使用的驗證[App Service 驗證](../app-service/overview-authentication-authorization.md)。

App Service 驗證會設定名為 `x-ms-client-principal-id` 和 `x-ms-client-principal-name` 的 HTTP 標頭，這些標頭分別包含已驗證使用者的用戶端主體識別碼和名稱。 您可以使用[繫結運算式](./functions-bindings-expressions-patterns.md)：`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}`，將繫結的 `userId` 屬性設為任一標頭的值。 

function.json 範例：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>2.x Java 輸入的範例

下列範例所示[Java 函式](functions-reference-java.md)，取得 SignalR 使用的輸入繫結的連接資訊並將它傳回透過 HTTP。

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>已驗證權杖

如果函式是由已驗證的用戶端觸發，您可以將使用者識別碼宣告新增至產生的權杖。 您可以輕鬆加入函式應用程式使用的驗證[App Service 驗證](../app-service/overview-authentication-authorization.md)。

App Service 驗證會設定名為 `x-ms-client-principal-id` 和 `x-ms-client-principal-name` 的 HTTP 標頭，這些標頭分別包含已驗證使用者的用戶端主體識別碼和名稱。 您可以使用[繫結運算式](./functions-bindings-expressions-patterns.md)：`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}`，將繫結的 `UserId` 屬性設為任一標頭的值。

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

## <a name="signalr-output-binding"></a>SignalR 輸出繫結

若使用 SignalR  輸出繫結，即可使用 Azure SignalR Service 來傳送一或多則訊息。 您可以將訊息廣播到所有已連線的用戶端，或者您可以只將訊息廣播到已對指定使用者驗證的已連線用戶端。

您也可以使用它來管理使用者所屬的群組。

請參閱特定語言的範例：

* [2.x C#](#2x-c-send-message-output-examples)
* [2.x JavaScript](#2x-javascript-send-message-output-examples)
* [2.x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2.xC#傳送訊息的輸出範例

#### <a name="broadcast-to-all-clients"></a>廣播到所有用戶端

下列範例示範 [C# 函式](functions-dotnet-class-library.md)，該函式會使用連結至所有已連線用戶端的輸出繫結，來傳送訊息。 `Target` 是要在每個用戶端上叫用的方法名稱。 `Arguments` 屬性是要傳遞至用戶端方法的零或多個物件陣列。

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>傳送給使用者

您可以藉由設定 SignalR 訊息的 `UserId` 屬性，僅將訊息傳送給已對使用者驗證的連線。

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

#### <a name="send-to-a-group"></a>傳送給群組

您可以將訊息僅傳送給已藉由設定新增至群組的連線`GroupName`SignalR 訊息的屬性。

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

### <a name="2x-c-group-management-output-examples"></a>2.xC#群組管理輸出範例

SignalR 服務可讓使用者新增至群組。 然後，訊息可以傳送給群組。 您可以使用`SignalRGroupAction`類別搭配`SignalR`輸出繫結來管理使用者的群組成員資格。

#### <a name="add-user-to-a-group"></a>將使用者新增至群組

下列範例會將使用者加入至群組。

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>從群組移除使用者

下列範例會從群組移除使用者。

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

### <a name="2x-javascript-send-message-output-examples"></a>2.x JavaScript 傳送訊息輸出範例

#### <a name="broadcast-to-all-clients"></a>廣播到所有用戶端

下列範例示範 function.json  檔案中的 SignalR 輸出繫結，以及透過 Azure SignalR Service 使用繫結來傳送訊息的 [JavaScript 函式](functions-reference-node.md)。 將輸出繫結設為一或多則 SignalR 訊息的陣列。 SignalR 訊息是由 `target` 屬性 (會指定要在每個用戶端上叫用的方法名稱) 和 `arguments` 屬性 (要傳遞至用戶端方法作為引數的物件陣列) 組成。

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>傳送給使用者

您可以藉由設定 SignalR 訊息的 `userId` 屬性，僅將訊息傳送給已對使用者驗證的連線。

function.json  維持不變。 以下是 JavaScript 程式碼：

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-group"></a>傳送給群組

您可以將訊息僅傳送給已藉由設定新增至群組的連線`groupName`SignalR 訊息的屬性。

function.json  維持不變。 以下是 JavaScript 程式碼：

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

### <a name="2x-javascript-group-management-output-examples"></a>2.x JavaScript 群組管理輸出範例

SignalR 服務可讓使用者新增至群組。 然後，訊息可以傳送給群組。 您可以使用`SignalR`輸出繫結來管理使用者的群組成員資格。

#### <a name="add-user-to-a-group"></a>將使用者新增至群組

下列範例會將使用者加入至群組。

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>從群組移除使用者

下列範例會從群組移除使用者。

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2.x Java 傳送訊息輸出範例

#### <a name="broadcast-to-all-clients"></a>廣播到所有用戶端

下列範例所示[Java 函式](functions-reference-java.md)，傳送訊息，使用輸出繫結至所有已連線的用戶端。 `target` 是要在每個用戶端上叫用的方法名稱。 `arguments` 屬性是要傳遞至用戶端方法的零或多個物件陣列。

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-user"></a>傳送給使用者

您可以藉由設定 SignalR 訊息的 `userId` 屬性，僅將訊息傳送給已對使用者驗證的連線。

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-group"></a>傳送給群組

您可以將訊息僅傳送給已藉由設定新增至群組的連線`groupName`SignalR 訊息的屬性。

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

### <a name="2x-java-group-management-output-examples"></a>2.x Java 群組管理輸出範例

SignalR 服務可讓使用者新增至群組。 然後，訊息可以傳送給群組。 您可以使用`SignalRGroupAction`類別搭配`SignalROutput`輸出繫結來管理使用者的群組成員資格。

#### <a name="add-user-to-a-group"></a>將使用者新增至群組

下列範例會將使用者加入至群組。

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>從群組移除使用者

下列範例會從群組移除使用者。

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

## <a name="configuration"></a>組態

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `SignalRConnectionInfo` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type**|| 必須設為 `signalRConnectionInfo`。|
|**direction**|| 必須設為 `in`。|
|**name**|| 函式程式碼中用於連線資訊物件的變數名稱。 |
|**hubName**|**HubName**| 此值必須設為 SignalR 中樞 (針對該中樞產生連線資訊) 的名稱。|
|**userId**|UserId | 選用：要在存取金鑰權杖中設定的使用者識別碼宣告值。 |
|**connectionStringSetting**|**ConnectionStringSetting**| 包含 SignalR Service 連接字串 (預設值為 "AzureSignalRConnectionString") 的應用程式設定名稱 |

### <a name="signalr"></a>SignalR

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `SignalR` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type**|| 必須設為 `signalR`。|
|**direction**|| 必須設為 `out`。|
|**name**|| 函式程式碼中用於連線資訊物件的變數名稱。 |
|**hubName**|**HubName**| 此值必須設為 SignalR 中樞 (針對該中樞產生連線資訊) 的名稱。|
|**connectionStringSetting**|**ConnectionStringSetting**| 包含 SignalR Service 連接字串 (預設值為 "AzureSignalRConnectionString") 的應用程式設定名稱 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [使用 Azure SignalR Service 來開發與設定 Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md)
