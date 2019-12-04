---
title: Azure Functions SignalR Service 繫結
description: 了解如何搭配使用 SignalR Service 繫結與 Azure Functions。
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: a1de59ebb5ef0d7f5522a388aa9a2f5818495a9f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786328"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>適用於 Azure Functions 的 SignalR Service 繫結

本文說明如何藉由在 Azure Functions 中使用 SignalR Service 繫結，來驗證及傳送即時訊息給連線至 [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) 的用戶端。 Azure Functions 支援 SignalR Service 的輸入和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>套件 - Functions 2.x

[Microsoft.signalrservice](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) Nuget 套件（版本 1. *）中提供了 SignalR Service 系結。 套件的原始程式碼位於 [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub 存放庫中。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

如需如何設定及使用 SignalR Service 和 Azure Functions 的詳細資訊，請參閱[Azure SignalR Service 的 Azure Functions 開發和](../azure-signalr/signalr-concept-serverless-development-config.md)設定。

### <a name="annotations-library-java-only"></a>注釋程式庫（僅限 JAVA）

若要使用 JAVA 函式中的 SignalR Service 批註，您必須將相依性新增至您的 pom 的*SignalR*成品（1.0 版或更高版本）。

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="input"></a>輸入

在用戶端可以連線到 Azure SignalR Service 之前，它必須擷取服務端點 URL 和有效的存取權杖。 SignalRConnectionInfo 輸入繫結會產生 SignalR Service 端點 URL 和有效的存取權杖，可用來連線到服務。 因為權杖是限時的，且可用來驗證連線的特定使用者，所以您不應該快取權杖或者在用戶端之間共用權杖。 使用此繫結的 HTTP 觸發程序可以供用戶端用來擷取連線資訊。

如需如何使用此系結來建立可供 SignalR 用戶端 SDK 使用的「negotiate」函式的詳細資訊，請參閱 SignalR Service 概念檔中的[Azure Functions 開發和](../azure-signalr/signalr-concept-serverless-development-config.md)設定一文。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

下列範例顯示函式*json*檔案中的 SignalR 連線資訊輸入系結，以及使用該系結來傳回連接資訊的[ C#腳本](functions-reference-csharp.md)函式。

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

C#腳本如下：

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

下列範例示範 function.json 檔案中的 SignalR 連線資訊輸入繫結，以及使用繫結來傳回連線資訊的 [JavaScript 函式](functions-reference-node.md)。

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

# <a name="pythontabpython"></a>[Python](#tab/python)

下列範例顯示在函式*json*檔案中的 SignalR 連線資訊輸入系結，以及使用該系結來傳回連接資訊的[Python](functions-reference-python.md)函式。

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

以下是 Python 程式碼：

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

下列範例示範的[JAVA](functions-reference-java.md)函式會使用輸入系結取得 SignalR 連接資訊，並透過 HTTP 傳回它。

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

---

### <a name="authenticated-tokens"></a>已驗證權杖

如果函式是由已驗證的用戶端觸發，您可以將使用者識別碼宣告新增至產生的權杖。 您可以使用[App Service authentication](../app-service/overview-authentication-authorization.md)，輕鬆地將驗證新增至函數應用程式。

App Service 驗證會設定名為 `x-ms-client-principal-id` 和 `x-ms-client-principal-name` 的 HTTP 標頭，這些標頭分別包含已驗證使用者的用戶端主體識別碼和名稱。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

您可以使用[繫結運算式](./functions-bindings-expressions-patterns.md)：`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}`，將繫結的 `UserId` 屬性設為任一標頭的值。

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

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

您可以使用[繫結運算式](./functions-bindings-expressions-patterns.md)：`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}`，將繫結的 `userId` 屬性設為任一標頭的值。

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

C#腳本如下：

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

您可以使用[繫結運算式](./functions-bindings-expressions-patterns.md)：`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}`，將繫結的 `userId` 屬性設為任一標頭的值。

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

# <a name="pythontabpython"></a>[Python](#tab/python)

您可以使用[繫結運算式](./functions-bindings-expressions-patterns.md)：`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}`，將繫結的 `userId` 屬性設為任一標頭的值。

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

以下是 Python 程式碼：

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

您可以使用[繫結運算式](./functions-bindings-expressions-patterns.md)：`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}`，將繫結的 `userId` 屬性設為任一標頭的值。

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

---

## <a name="output"></a>輸出

若使用 SignalR 輸出繫結，即可使用 Azure SignalR Service 來傳送一或多則訊息。 您可以將訊息廣播到所有已連線的用戶端，或者您可以只將訊息廣播到已對指定使用者驗證的已連線用戶端。

您也可以使用它來管理使用者所屬的群組。

### <a name="broadcast-to-all-clients"></a>廣播到所有用戶端

下列範例顯示的函式會使用輸出系結，將訊息傳送至所有已連線的用戶端。 *目標*是要在每個用戶端上叫用之方法的名稱。 *引數*是零個或多個要傳遞至用戶端方法之物件的陣列。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

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

C#腳本如下：

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

以下是 Python 程式碼：

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

### <a name="send-to-a-user"></a>傳送給使用者

您只能將訊息傳送至已通過使用者驗證的連線，方法是在 SignalR 訊息中設定*使用者識別碼*。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

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

C#腳本如下：

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

以下是 Python 程式碼：

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this user ID
        'userId': 'userId1',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

### <a name="send-to-a-group"></a>傳送至群組

您可以藉由設定 SignalR 訊息中的*組名*，僅將訊息傳送至已新增至群組的連接。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

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

C#腳本如下：

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

以下是 *function.json* 檔案中的繫結資料：

function.json 範例：

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

以下是 Python 程式碼：

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this group
        'groupName': 'myGroup',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

### <a name="group-management"></a>群組管理

SignalR Service 可讓使用者新增至群組。 然後可以將訊息傳送給群組。 您可以使用 `SignalR` 輸出系結來管理使用者的群組成員資格。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

#### <a name="add-user-to-a-group"></a>將使用者新增至群組

下列範例會將使用者新增至群組。

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>從群組中移除使用者

下列範例會從群組中移除使用者。

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> 為了讓 `ClaimsPrincipal` 正確地系結，您必須已在 Azure Functions 中設定驗證設定。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

#### <a name="add-user-to-a-group"></a>將使用者新增至群組

下列範例會將使用者新增至群組。

範例*函數. json*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*執行. .csx*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>從群組中移除使用者

下列範例會從群組中移除使用者。

範例*函數. json*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*執行. .csx*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> 為了讓 `ClaimsPrincipal` 正確地系結，您必須已在 Azure Functions 中設定驗證設定。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

#### <a name="add-user-to-a-group"></a>將使用者新增至群組

下列範例會將使用者新增至群組。

範例*函數. json*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
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

#### <a name="remove-user-from-a-group"></a>從群組中移除使用者

下列範例會從群組中移除使用者。

範例*函數. json*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
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

# <a name="pythontabpython"></a>[Python](#tab/python)

#### <a name="add-user-to-a-group"></a>將使用者新增至群組

下列範例會將使用者新增至群組。

範例*函數. json*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init. py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'add'
    }))
```

#### <a name="remove-user-from-a-group"></a>從群組中移除使用者

下列範例會從群組中移除使用者。

範例*函數. json*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init. py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'remove'
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

#### <a name="add-user-to-a-group"></a>將使用者新增至群組

下列範例會將使用者新增至群組。

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

#### <a name="remove-user-from-a-group"></a>從群組中移除使用者

下列範例會從群組中移除使用者。

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

---

## <a name="configuration"></a>組態

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `SignalRConnectionInfo` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type**|| 必須設為 `signalRConnectionInfo`。|
|**direction**|| 必須設為 `in`。|
|**name**|| 函式程式碼中用於連線資訊物件的變數名稱。 |
|**hubName**|**HubName**| 此值必須設為 SignalR 中樞 (針對該中樞產生連線資訊) 的名稱。|
|**userId**|UserId| 選擇性：要在存取金鑰權杖中設定的使用者識別碼宣告值。 |
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
