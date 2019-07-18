---
title: 如何針對 Azure SignalR 服務調整多個執行個體
description: 在許多調整的情況下，客戶通常必須佈建多個執行個體，並將它們放在一起，以建立大規模的部署設定。 例如，分區化會需要多個執行個體支援。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809166"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>如何調整 SignalR 服務多個執行個體？
最新的 SignalR 服務 SDK 支援 SignalR 服務執行個體的多個端點。 您可以調整的並行連線，使用這項功能，或將它用於跨區域訊息。

## <a name="for-aspnet-core"></a>For ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何從設定中新增多個端點？

具有索引鍵的組態`Azure:SignalR:ConnectionString`或`Azure:SignalR:ConnectionString:`SignalR 服務的連接字串。

如果索引鍵的開頭`Azure:SignalR:ConnectionString:`，它應該採用格式`Azure:SignalR:ConnectionString:{Name}:{EndpointType}`，其中`Name`並`EndpointType`是屬性`ServiceEndpoint`物件，並可從程式碼存取。

您可以新增多個執行個體連接字串使用下列`dotnet`命令：

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何從程式碼中新增多個端點？

A`ServicEndpoint`類別引進來描述 Azure SignalR 服務端點的屬性。
使用透過 Azure SignalR 服務 SDK 時，您可以設定多個執行個體端點：
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>如何自訂端點路由器？

根據預設，SDK 會使用[DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs)挑選端點。

#### <a name="default-behavior"></a>預設行為 
1. 用戶端要求路由

    當用戶端`/negotiate`與應用程式伺服器。 根據預設，SDK**隨機選取**一個端點，從可用的服務端點的集合。

2. 伺服器訊息路由

    當 *將訊息傳送至特定的 **連接*** 和目標連線會路由傳送至目前的伺服器，訊息會直接移至該連線的端點。 否則，訊息會傳播到每個 Azure SignalR 端點。

#### <a name="customize-routing-algorithm"></a>自訂路由的演算法
當您有特殊的知識來識別訊息應該移至哪一個端點時，您可以建立您自己的路由器。

做為範例底下定義自訂的路由器時的群組`east-`一律移至名為的端點`east`:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

另一個範例所示，會覆寫預設值 negotiate 行為、 選取的端點取決於應用程式伺服器所在的位置。

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

別忘了註冊至 DI 容器使用路由器：

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>For ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何從設定中新增多個端點？

具有索引鍵的組態`Azure:SignalR:ConnectionString`或`Azure:SignalR:ConnectionString:`SignalR 服務的連接字串。

如果索引鍵的開頭`Azure:SignalR:ConnectionString:`，它應該採用格式`Azure:SignalR:ConnectionString:{Name}:{EndpointType}`，其中`Name`並`EndpointType`是屬性`ServiceEndpoint`物件，並可從程式碼存取。

您可以新增多個執行個體的連接字串來`web.config`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何從程式碼中新增多個端點？

A`ServicEndpoint`類別引進來描述 Azure SignalR 服務端點的屬性。
使用透過 Azure SignalR 服務 SDK 時，您可以設定多個執行個體端點：

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>如何自訂路由器？

ASP.NET SignalR 及 ASP.NET Core SignalR 唯一的差別是的 http 內容類型`GetNegotiateEndpoint`。 為 ASP.NET SignalR，它是[IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19)型別。

以下是自訂為 ASP.NET SignalR 交涉範例：

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

別忘了註冊至 DI 容器使用路由器：

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>在跨區域案例中的組態

`ServiceEndpoint`物件具有`EndpointType`屬性值`primary`或`secondary`。

`primary` 端點慣用的端點，以接收用戶端流量，而且被視為具有更可靠的網路連線;`secondary`端點都會被視為具有較不可靠的網路連線和僅適用於採用伺服器到用戶端流量，比方說，廣播訊息，而不接受用戶端到伺服器的流量使用。

在跨區域的情況下，網路可能是不穩定。 一個應用程式伺服器位於*美國東部*，SignalR 服務的端點位於相同*美國東部*區域可以設定為`primary`且在其他區域的端點標示為`secondary`。 在此組態中，可以在其他區域的服務端點**接收**訊息從此*美國東部*應用程式伺服器上，但會沒有**跨區域**用戶端路由傳送至此應用程式伺服器。 下圖顯示架構：

![跨異地基礎結構](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

當用戶端嘗試`/negotiate`與應用程式伺服器，使用預設的 SDK 路由器**隨機選取**集中可用的一個端點`primary`端點。 當端點可供使用，然後 SDK**會隨機選擇**從所有可用`secondary`端點。 端點會標示為**可用**伺服器與服務端點之間的連線時運作。

在跨區域案例中，當用戶端嘗試`/negotiate`與應用程式伺服器中裝載*美國東部*，依預設它一律會傳回`primary`端點位於相同的區域。 當所有*美國東部*端點無法使用，用戶端重新導向至其他區域中的端點。 容錯移轉的下一節會說明詳細的案例。

![一般交涉](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>容錯移轉

當所有`primary`端點會無法使用，用戶端`/negotiate`挑選可用`secondary`端點。 此容錯移轉機制需要每個端點應該做為`primary`到至少一個應用程式伺服器的端點。

![容錯移轉](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>後續步驟

在本指南中，您會了解如何調整大小、 分區化和跨區域案例的相同應用程式中設定多個執行個體。

多個端點的支援也可用在高可用性和災害復原案例。

> [!div class="nextstepaction"]
> [安裝程式的災害復原和高可用性的 SignalR 服務](./signalr-concept-disaster-recovery.md)
