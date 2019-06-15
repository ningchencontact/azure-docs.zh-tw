---
title: 開發和設定 Azure Functions SignalR 服務應用程式
description: 有關如何開發和設定使用 Azure Functions 和 Azure SignalR 服務的無伺服器即時應用程式的詳細資料
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809022"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions 開發和使用 Azure SignalR 服務的組態

Azure Functions 應用程式可以利用[Azure SignalR 服務繫結](../azure-functions/functions-bindings-signalr-service.md)新增即時功能。 用戶端應用程式會使用數種語言中可用的用戶端 Sdk 連接到 Azure SignalR 服務並接收即時訊息。

本文說明開發和設定 Azure 函式應用程式與 SignalR 服務整合的概念。

## <a name="signalr-service-configuration"></a>SignalR 服務組態

Azure SignalR 服務可以設定在不同的模式。 Azure Functions 搭配使用時，服務必須設定於*無伺服器*模式。

在 Azure 入口網站中，找出*設定*SignalR 服務資源頁面。 設定*服務模式*要*無伺服器*。

![SignalR Service 模式](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions 開發

使用 Azure Functions 和 Azure SignalR 服務通常所建置的無伺服器即時應用程式需要兩個 Azure 函式：

* 「 交涉 」 以取得有效的 SignalR 服務的用戶端呼叫的函式的存取權杖，而服務端點 URL
* 一或多個傳送訊息] 或 [管理群組成員資格的函式

### <a name="negotiate-function"></a>交涉函式

用戶端應用程式需要有效的存取權杖來連線到 Azure SignalR 服務。 存取權杖可以是匿名或已驗證為指定的使用者識別碼。 無伺服器的 SignalR 服務應用程式必須將 HTTP 端點名為"negotiate"來取得權杖和其他的連接資訊，例如 SignalR 服務端點 URL。

使用 HTTP 觸發 Azure 函式和*SignalRConnectionInfo*輸入繫結來產生連接資訊物件。 此函式必須以 HTTP 路由`/negotiate`。

如需有關如何建立交涉函式的詳細資訊，請參閱 < [ *SignalRConnectionInfo*輸入繫結參考](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding)。

若要深入了解如何建立已驗證的 token，請參閱[使用 App Service 驗證](#using-app-service-authentication)。

### <a name="sending-messages-and-managing-group-membership"></a>將訊息傳送及管理群組成員資格

使用*SignalR*輸出繫結傳送訊息給用戶端連線到 Azure SignalR 服務。 您可以將訊息傳播至所有用戶端，或您可以將它們傳送至用戶端會驗證特定使用者識別碼，或已新增至特定群組的子集。

使用者可以加入一個或多個群組。 您也可以使用*SignalR*輸出繫結來新增或移除群組之間的使用者。

如需詳細資訊，請參閱 < [ *SignalR*輸出繫結參考](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding)。

### <a name="signalr-hubs"></a>SignalR 中樞

SignalR 具有 「 中樞 」 的概念。 從 Azure Functions 傳送訊息，以及每個用戶端連線的範圍限定在特定的中樞。 您可以使用中樞做為您的連線和訊息分成邏輯的命名空間的方式。

## <a name="client-development"></a>用戶端開發

SignalR 用戶端應用程式可以利用 SignalR 用戶端 SDK 中有幾種語言的其中一個以便輕鬆地連接到並接收來自 Azure SignalR 服務訊息。

### <a name="configuring-a-client-connection"></a>設定用戶端連線

若要連接到 SignalR 服務，用戶端必須完成下列步驟所組成的連線成功交涉：

1. 提出的要求*交涉*上面討論以取得有效的連接資訊的 HTTP 端點
1. 使用連線到 SignalR 服務的服務端點 URL 和存取權杖將會取自*交涉*端點

SignalR 用戶端 Sdk 已經包含執行交涉交握所需的邏輯。 Negotiate 端點的 URL，傳遞減`negotiate`區段中的，sdk `HubConnectionBuilder`。 在 JavaScript 中，以下是範例：

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

依照慣例，SDK 會自動附加`/negotiate`至 URL 並使用它來開始的交涉。

> [!NOTE]
> 如果您在瀏覽器中使用 JavaScript/TypeScript SDK，您需要[啟用跨原始資源共用 (CORS)](#enabling-cors)函式應用程式上。

如需有關如何使用 SignalR 用戶端 SDK 的詳細資訊，請參閱您的語言的文件：

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>從用戶端傳送訊息至服務

雖然 SignalR SDK 可讓用戶端應用程式叫用中的 SignalR 中樞的後端邏輯，這項功能尚無法使用 Azure Functions 使用 SignalR 服務時。 使用 HTTP 要求來叫用 Azure 函式。

## <a name="azure-functions-configuration"></a>Azure Functions 組態

整合 Azure SignalR 服務的 azure 函式應用程式可以部署任何一般的 Azure 函數應用程式，例如使用技術等[持續部署](../azure-functions/functions-continuous-deployment.md)， [zip 部署](../azure-functions/deployment-zip-push.md)，並[從封裝執行](../azure-functions/run-functions-from-deployment-package.md)。

不過，有幾個使用 SignalR 服務的繫結的應用程式的特殊考量。 如果用戶端會在瀏覽器中執行，則必須啟用 CORS。 如果應用程式需要驗證，您可以整合 negotiate 端點與 App Service 驗證。

### <a name="enabling-cors"></a>啟用 CORS

JavaScript/TypeScript 用戶端會將 HTTP 要求對交涉函式，來起始連線交涉。 當用戶端應用程式裝載於 Azure 函數應用程式不同的網域時，必須在函式應用程式上啟用跨原始資源共用 (CORS)，或瀏覽器將會封鎖要求。

#### <a name="localhost"></a>localhost

當您的本機電腦上執行函式應用程式，您可以加入`Host`一節*local.settings.json*來啟用 CORS。 在 `Host`區段中，新增兩個屬性：

* `CORS` -輸入是原始用戶端應用程式的基底 URL
* `CORSCredentials` -將它設定為`true`允許"withCredentials 」 要求

範例：

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="azure"></a>Azure

若要啟用 CORS 的 Azure 函式應用程式上，移至下的 [CORS 組態] 畫面*平台功能*函式應用程式在 Azure 入口網站中的索引標籤。

SignalR 用戶端呼叫交涉函式時，必須啟用 CORS 的存取控制-允許-認證使用。 選取核取方塊來啟用它。

在 *允許的來源*區段中，新增具有您的 web 應用程式的來源基底 URL 的項目。

![設定 CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>使用 App Service 驗證

Azure Functions 有內建的驗證，支援受歡迎的提供者，例如 Facebook、 Twitter、 Microsoft 帳戶、 Google 和 Azure Active Directory。 這項功能可以與整合*SignalRConnectionInfo*繫結至建立連線，以 Azure SignalR 服務已通過驗證的使用者識別碼。 您的應用程式可以使用傳送訊息*SignalR*輸出繫結的目標的使用者識別碼。

在 Azure 入口網站中，在您的函式應用程式*平台功能*索引標籤上，開啟*驗證/授權*設定 視窗。 請依照下列的文件[App Service 驗證](../app-service/overview-authentication-authorization.md)設定使用您選擇的身分識別提供者的驗證。

一旦設定之後，會包含已驗證的 HTTP 要求`x-ms-client-principal-name`和`x-ms-client-principal-id`分別包含已驗證的識別的使用者名稱和使用者識別碼的標頭。

您可以使用這些標頭中的您*SignalRConnectionInfo*繫結組態，以建立已驗證的連線。 以下是範例C#交涉使用函式`x-ms-client-principal-id`標頭。

```csharp
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

您可以再將訊息傳送給該使用者藉由設定`UserId`SignalR 訊息的屬性。

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

如需其他語言的資訊，請參閱[Azure SignalR 服務繫結](../azure-functions/functions-bindings-signalr-service.md)for Azure Functions 的參考。

## <a name="next-steps"></a>後續步驟

在本文中，您學到如何開發和設定使用 Azure Functions 的無伺服器 SignalR 服務應用程式。 嘗試建立應用程式使用其中一個快速入門或教學課程上自行[SignalR 服務 [概觀] 頁面](index.yml)。