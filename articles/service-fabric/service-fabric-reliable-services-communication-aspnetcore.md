---
title: 使用 ASP.NET Core 的服務通訊 |Microsoft Docs
description: 了解如何在無狀態和具狀態的 Reliable Services 中使用 ASP.NET Core。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 638c06e1854504dcb7ff34b1d9df56694556c421
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939785"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>Azure Service Fabric Reliable Services 中的 ASP.NET Core

ASP.NET Core 是一種開放原始碼和跨平台架構。 此架構設計用來建置雲端式、 網際網路連線應用程式，例如 web apps、 IoT 應用程式，以及行動後端。

這篇文章是使用裝載在 Service Fabric Reliable Services 中的 ASP.NET Core 服務的深度指南**Microsoft.ServiceFabric.AspNetCore。** 一組 NuGet 套件。

如需 Service Fabric 中的 ASP.NET Core 的簡介教學課程和如何設定開發環境的指示，請參閱[教學課程：建立及部署含有 ASP.NET Core Web API 前端服務和具狀態後端服務的應用程式](service-fabric-tutorial-create-dotnet-app.md)。

這篇文章的其餘部分假設您已熟悉 ASP.NET Core。 如果沒有，請先閱讀[ASP.NET Core 基本概念](https://docs.microsoft.com/aspnet/core/fundamentals/index)。

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric 環境中的 ASP.NET Core

ASP.NET Core 和 Service Fabric 應用程式可以在.NET Core 或完整的.NET Framework 上執行。 您可以使用 ASP.NET Core Service Fabric 中的兩種方式：
 - **裝載作為客體可執行檔**。 如此一來，主要是在 Service Fabric 上執行現有的 ASP.NET Core 應用程式，不必變更程式碼。
 - **在 reliable service 內部執行**。 如此一來允許與 Service Fabric 執行階段更緊密整合，並允許具狀態 ASP.NET Core 服務。

這篇文章的其餘部分將說明如何使用 ASP.NET Core 可靠服務，透過 ASP.NET Core 整合元件隨附於 Service Fabric SDK 內。

## <a name="service-fabric-service-hosting"></a>Service Fabric 服務裝載

在 Service Fabric 中一或多個執行個體及/或您的服務複本執行*服務主機處理序*： 執行您的服務程式碼的可執行檔。 您身為服務作者，擁有服務主機處理序，和 Service Fabric 會啟動並監視您。

傳統 ASP.NET (直到 MVC 5) 已透過 System.Web.dll 與 IIS 緊密結合。 ASP.NET Core 能區別網頁伺服器與 web 應用程式。 這種區隔可讓具有可攜性不同的網頁伺服器之間的 web 應用程式。 它也可讓 web 伺服器不必*自我裝載*。 這表示您可以在自己的程序，而不是專用的 web 伺服器軟體，例如 IIS 所擁有的處理序中啟動 web 伺服器。

若要結合的 Service Fabric 服務和 ASP.NET 中，為來賓可執行檔，或者在可靠的服務中，您必須是能夠啟動您的服務主機處理序內的 ASP.NET 程式碼。 ASP.NET Core 自我裝載可讓您執行這項操作。

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Reliable service 中裝載 ASP.NET Core
一般而言，自我裝載的 ASP.NET Core 應用程式會在應用程式的進入點建立 WebHost，例如 `Program.cs` 方法中的 `static void Main()`。 在此情況下，WebHost 的生命週期會繫結程序生命週期。

![在處理序中裝載 ASP.NET Core][0]

但是應用程式進入點不是在 reliable service 中建立 WebHost 的正確位置。 這是因為應用程式進入點只會用來向 Service Fabric 執行階段的服務類型，這樣才可以建立該服務類型的執行個體。 WebHost 應該建立在 reliable service 本身中。 在服務主機處理序中，服務執行個體及/或複本可以通過多個生命週期。 

Reliable Service 執行個體是由您衍生自 `StatelessService` 或 `StatefulService` 的服務類別代表。 服務的通訊堆疊包含在您服務類別中的 `ICommunicationListener` 實作。 `Microsoft.ServiceFabric.AspNetCore.*` NuGet 封裝包含的實作`ICommunicationListener`啟動和管理 Kestrel 或 HTTP.sys 的 ASP.NET Core WebHost reliable service 中。

![Reliable service 中裝載 ASP.NET Core 的圖表][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
`ICommunicationListener` Kestrel 和 HTTP.sys 中的實作`Microsoft.ServiceFabric.AspNetCore.*`NuGet 套件有類似的使用模式。 但執行的是針對每個 web 伺服器的動作稍有不同。 

這兩種通訊接聽程式都會提供使用下列引數的建構函式︰
 - **`ServiceContext serviceContext`**：這是`ServiceContext`物件，其中包含執行中服務的相關資訊。
 - **`string endpointName`**：這是名稱`Endpoint`在 ServiceManifest.xml 中的組態。 它主要是由兩個通訊接聽程式的不同位置。 HTTP.sys*需要*`Endpoint`組態，而 Kestrel 不會。
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**：這是 lambda 可實作您，在您建立並傳回`IWebHost`。 它可讓您設定`IWebHost`您通常會在 ASP.NET Core 應用程式的方式。 Lambda 會為您提供所產生的 URL，在您使用的 Service Fabric 整合選項而定，`Endpoint`您提供的組態。 然後，您可以修改，或使用該 URL 來啟動 web 伺服器。

## <a name="service-fabric-integration-middleware"></a>Service Fabric 整合中介軟體
`Microsoft.ServiceFabric.AspNetCore` NuGet 套件會包含`UseServiceFabricIntegration`擴充方法`IWebHostBuilder`新增 Service Fabric – 感知的中介軟體。 此中介軟體會設定 Kestrel 或 HTTP.sys`ICommunicationListener`向 Service Fabric 命名服務註冊唯一服務 URL。 然後，它會驗證用戶端要求，以確保用戶端連線至正確的服務。 

這是必要步驟以避免用戶端不小心連線到錯誤的服務。 這是因為在共用主機環境，例如 Service Fabric，多個 web 應用程式可以在相同的實體或虛擬機器上執行，但不使用唯一的主機名稱。 此案例會在下一節中詳細說明。

### <a name="a-case-of-mistaken-identity"></a>誤用身分識別的案例
不論通訊協定，服務複本會接聽唯一 IP:port 組合。 一旦服務複本開始 ip: port 端點上接聽，它會報告該端點位址至 Service Fabric 命名服務。 用戶端或其他服務可以探索它。 如果服務使用動態指派的應用程式連接埠，服務複本可能會碰巧使用相同的 ip: port 端點的另一個先前是在相同的實體上的服務或虛擬機器。 這可能會造成用戶端不小心連線至錯誤的服務。 如果以下一連串事件，就會發生，可能會造成這種情況下：

 1. 服務 A 透過 HTTP 接聽 10.0.0.1:30000。 
 2. 用戶端解析服務 A 並取得位址 10.0.0.1: 30000。
 3. 服務 A 移到另一個節點。
 4. 服務 B 置於 10.0.0.1 且碰巧使用相同的連接埠 30000。
 5. 用戶端使用快取的位址 10.0.0.1:30000 嘗試連線到服務 A。
 6. 用戶端現在已成功連接到服務 B，且不瞭解其已連線到錯誤的服務。

這會不定時造成 難以診斷的 Bug。

### <a name="using-unique-service-urls"></a>使用唯一的服務 URL
若要避免這些錯誤，服務可以將端點張貼至命名服務，使用唯一的識別項，並在用戶端要求期間驗證該唯一識別碼。 這在非惡意租用戶受信任環境中的服務之間為合作式動作。 它並不提供惡意租用戶環境中的安全服務驗證。

在受信任環境中，新增的中介軟體`UseServiceFabricIntegration`方法會自動將唯一的識別碼附加至張貼至命名服務的位址。 它會驗證每個要求該識別項。 如果識別項不符合中, 介軟體會立即傳回 HTTP 410 不存在的回應。

使用動態指派連接埠的服務應該要使用此中介軟體。

使用固定的唯一連接埠的服務在合作的環境中沒有這個問題。 固定的唯一連接埠通常是用於對外開放的服務，需要用戶端應用程式連線的知名通訊埠。 例如，大部分的網際網路對向 web 應用程式會使用 web 瀏覽器連接的連接埠 80 或 443。 在此情況下，不應該啟用唯一識別碼。

下圖顯示已啟用中介軟體的要求流程︰

![Service Fabric ASP.NET Core 整合][2]

Kestrel 和 HTTP.sys`ICommunicationListener`實作以完全相同的方式使用此機制。 雖然 HTTP.sys 可以內部區分要求使用的基礎，根據唯一的 URL 路徑**HTTP.sys**連接埠共用功能，該功能*不*使用 HTTP.sys `ICommunicationListener`實作。 這是因為它會導致先前所述的案例中的 HTTP 503 和 HTTP 404 錯誤狀態碼。 又很難判斷錯誤的意圖的用戶端的 HTTP 503 和 HTTP 404 通常用來表示其他錯誤。 

因此，Kestrel 和 HTTP.sys`ICommunicationListener`所提供的中介軟體上標準化的實作`UseServiceFabricIntegration`擴充方法。 因此，用戶端只需在 HTTP 410 回應上執行的服務端點重新解析動作。

## <a name="httpsys-in-reliable-services"></a>Reliable Services 中的 HTTP.sys
您也可以匯入在 Reliable Services 中使用 HTTP.sys **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet 套件。 此套件包含`HttpSysCommunicationListener`，實作`ICommunicationListener`。 `HttpSysCommunicationListener` 可讓您利用 HTTP.sys 做為 web 伺服器來建立在 reliable service 內部 ASP.NET Core WebHost。

HTTP.sys 根據[Windows HTTP 伺服器 API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx)。 此 API 會使用**HTTP.sys**核心驅動程式來處理 HTTP 要求，並將它們路由至執行 web 應用程式的程序。 這可讓相同的實體或虛擬機器來裝載 web 應用程式相同的連接埠，明確識別的唯一 URL 路徑或主機名稱上的多個處理序。 這些功能對於在 Service Fabric 中於相同叢集裝載多個網站很有用。

>[!NOTE]
>HTTP.sys 實作只適用於 Windows 平台。

下圖說明如何使用 HTTP.sys **HTTP.sys**在 Windows 上的連接埠共用的核心驅動程式：

![HTTP.sys diagram][3]

### <a name="httpsys-in-a-stateless-service"></a>無狀態服務中的 HTTP.sys
若要在無狀態服務中使用 `HttpSys`，請覆寫 `CreateServiceInstanceListeners` 方法並傳回 `HttpSysCommunicationListener` 執行個體︰

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys 在具狀態服務

`HttpSysCommunicationListener` 目前不設計用於具狀態服務，由於與基礎的複雜性**HTTP.sys**連接埠共用功能。 如需詳細資訊，請參閱下列章節 HTTP.sys 使用動態連接埠配置。 對於具狀態服務，Kestrel 是建議的 web 伺服器。

### <a name="endpoint-configuration"></a>端點組態

`Endpoint`不需要對使用 Windows HTTP Server API，包括 HTTP.sys 網頁伺服器的組態。 使用 Windows HTTP Server API 的 web 伺服器必須先保留其 URL 與 HTTP.sys (這通常以完成[netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx)工具)。 

這個動作需要提高您的服務不提供預設的權限。 "Http"或"https"選項`Protocol`屬性`Endpoint`在 ServiceManifest.xml 中的組態會特別用來指示 Service Fabric 執行階段，以代表您向 HTTP.sys 的 URL。 其做法是使用[*強式萬用字元*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL 前置詞。

例如，若要保留`http://+:80`服務，請在 ServiceManifest.xml 中使用下列設定：

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

端點名稱必須傳遞給 `HttpSysCommunicationListener` 建構函式︰

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>HTTP.sys 使用靜態連接埠
若要利用 HTTP.sys 使用靜態連接埠，提供的連接埠號碼`Endpoint`組態：

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>HTTP.sys 使用動態連接埠
若要利用 HTTP.sys 使用動態指派連接埠，請省略`Port`屬性中的`Endpoint`組態：

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

所配置的動態連接埠`Endpoint`組態會提供一個連接埠*每個主控件程序*。 目前的 Service Fabric 主控模型可讓多個服務執行個體及/或複本裝載於相同的程序。 這表示每個會共用相同的連接埠時透過配置`Endpoint`組態。 多個**HTTP.sys**執行個體可以共用連接埠使用基礎**HTTP.sys**連接埠共用功能。 但它不受支援`HttpSysCommunicationListener`由於其帶來的複雜性用戶端要求。 動態連接埠使用量，Kestrel 是建議的 web 伺服器。

## <a name="kestrel-in-reliable-services"></a>Reliable Services 中的 Kestrel
您也可以匯入在 Reliable Services 中使用 Kestrel **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet 套件。 此套件包含`KestrelCommunicationListener`，實作`ICommunicationListener`。 `KestrelCommunicationListener` 可讓您藉由使用 Kestrel 作為 web 伺服器建立在 reliable service 內部 ASP.NET Core WebHost。

Kestrel 根據 libuv 是 ASP.NET Core 的跨平台 web 伺服器，為跨平台非同步 I/O 程式庫。 不同於 HTTP.sys，Kestrel 不會使用集中式的端點管理員。 也不同於 HTTP.sys，Kestrel 不支援多個處理序之間的連接埠共用。 Kestrel 的每個執行個體必須使用唯一的連接埠。

![Kestrel 圖表][4]

### <a name="kestrel-in-a-stateless-service"></a>無狀態服務中的 Kestrel
若要在無狀態服務中使用 `Kestrel`，請覆寫 `CreateServiceInstanceListeners` 方法並傳回 `KestrelCommunicationListener` 執行個體︰

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>具狀態服務中的 Kestrel
若要在具狀態服務中使用 `Kestrel`，請覆寫 `CreateServiceReplicaListeners` 方法並傳回 `KestrelCommunicationListener` 執行個體︰

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

在此範例中，`IReliableStateManager` 的單一執行個體會提供給 WebHost 相依性插入容器。 這不是絕對必要，但它可讓您使用`IReliableStateManager`和可靠的集合，在 MVC 控制器動作方法。

`Endpoint` 組態名稱*不*提供給具狀態服務中的 `KestrelCommunicationListener`。 我們會在下列各節中詳細說明這個部分。

### <a name="configure-kestrel-to-use-https"></a>將 Kestrel 設定為使用 HTTPS
當您的服務中啟用與 Kestrel HTTPS，您必須設定接聽的幾個選項。 更新`ServiceInstanceListener`若要使用*EndpointHttps*端點與接聽特定通訊埠 （例如連接埠 443）。 當設定為使用 Kestrel web 伺服器的 web 主機，您必須設定 Kestrel 來接聽的所有網路介面上的 IPv6 位址： 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

如需教學課程的完整範例，請參閱[設定為使用 HTTPS 的 Kestrel](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)。


### <a name="endpoint-configuration"></a>端點組態
`Endpoint`搭配使用 Kestrel 不需要設定。 

Kestrel 是簡單的獨立 web 伺服器。 不同於 HTTP.sys （或 HttpListener），它不需要`Endpoint`在 ServiceManifest.xml 中的設定因為它不需要註冊 URL，再開始。 

#### <a name="use-kestrel-with-a-static-port"></a>搭配使用 Kestrel 與靜態連接埠
您可以設定中的靜態連接埠`Endpoint`的 ServiceManifest.xml 與 Kestrel 搭配使用的組態。 雖然這不是絕對必要，它會提供兩個潛在的優點：
 - 如果連接埠不在應用程式連接埠範圍內，由 Service Fabric 會將它開啟透過 OS 防火牆。
 - 透過 `KestrelCommunicationListener` 提供給您的 URL 會使用此連接埠。

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

如果設定了 `Endpoint`，其名稱必須傳遞至 `KestrelCommunicationListener` 建構函式︰ 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

如果未使用 ServiceManifest.xml`Endpoint`組態中，省略名稱中的`KestrelCommunicationListener`建構函式。 在此情況下，它會使用動態連接埠。 請參閱下一節，如需詳細資訊。

#### <a name="use-kestrel-with-a-dynamic-port"></a>搭配使用 Kestrel 與動態連接埠
Kestrel 無法使用自動連接埠指派從`Endpoint`在 ServiceManifest.xml 中的組態。 這是因為自動連接埠指派`Endpoint`設定會指派每個唯一的連接埠*主機處理序*，且單一主機處理序可以包含多個 Kestrel 執行個體。 這並不使用 Kestrel，因為它不支援連接埠共用。 因此，每個 Kestrel 執行個體必須開啟唯一連接埠。

若要使用動態通訊埠指派與 Kestrel，省略`Endpoint`在 ServiceManifest.xml 中的組態，請完全不會傳遞至的端點名稱和`KestrelCommunicationListener`建構函式，如下所示：

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

在此組態中，`KestrelCommunicationListener` 會自動從應用程式連接埠範圍選取未使用的連接埠。

## <a name="service-fabric-configuration-provider"></a>Service Fabric 組態提供者
ASP.NET Core 中的應用程式設定為基礎的組態提供者所建立的索引鍵 / 值組。 讀取[ASP.NET Core 中的組態](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/)若要了解更多在一般 ASP.NET Core 組態的支援。

本章節描述如何的 Service Fabric 組態提供者整合，使用 ASP.NET Core 組態匯入`Microsoft.ServiceFabric.AspNetCore.Configuration`NuGet 套件。

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration 啟動延伸模組
匯入後`Microsoft.ServiceFabric.AspNetCore.Configuration`NuGet 套件，您需要以 ASP.NET Core 組態 API 註冊 Service Fabric 組態來源。 您可以檢查，藉以**AddServiceFabricConfiguration**中的延伸模組`Microsoft.ServiceFabric.AspNetCore.Configuration`命名空間對`IConfigurationBuilder`。

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

現在的 ASP.NET Core 服務可以存取 Service Fabric 的組態設定，就像任何其他應用程式設定。 比方說，您可以使用 「 選項 」 模式來載入強型別物件的設定。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>預設索引鍵的對應
根據預設，Service Fabric 組態提供者會包含封裝名稱、 區段名稱和屬性名稱。 在一起，這些會將 ASP.NET Core 組態機碼中，表單，如下所示：
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

例如，如果您有組態套件，名為`MyConfigPackage`使用下列內容，然後設定值可在 ASP.NET Core`IConfiguration`透過*MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric 組態選項
Service Fabric 組態提供者也支援`ServiceFabricConfigurationOptions`變更索引鍵對應的預設行為。

#### <a name="encrypted-settings"></a>加密的設定
Service Fabric 組態提供者一樣，Service Fabric 支援加密的設定。 解密的加密的設定至 ASP.NET Core`IConfiguration`預設。 加密的值儲存於該處改。 但是如果您想要將儲存在 ASP.NET Core IConfiguration 值解密，您可以設定*DecryptValue*旗標設為 false，在`AddServiceFabricConfiguration`延伸模組，如下所示：

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>多個組態封裝
Service Fabric 支援多個組態封裝。 根據預設，封裝名稱包含在組態機碼。 但是您可以設定`IncludePackageName`旗標設為 false，如下所示：
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>自訂的索引鍵對應 」、 「 值擷取和 「 資料母體擴展
Service Fabric 組態提供者也支援自訂的索引鍵對應更進階的案例`ExtractKeyFunc`和自訂-擷取的值與`ExtractValueFunc`。 您甚至可以變更利用填入 ASP.NET Core 組態從 Service Fabric 組態資料的整個程序`ConfigAction`。

下列範例說明如何使用`ConfigAction`來自訂資料母體擴展：
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>組態更新
Service Fabric 組態提供者也支援組態更新。 您可以使用 ASP.NET Core`IOptionsMonitor`收到變更通知，然後使用`IOptionsSnapshot`重新載入組態資料。 如需詳細資訊，請參閱 < [ASP.NET Core 選項](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

根據預設，會支援這些選項。 沒有進一步撰寫程式碼才能讓組態更新。

## <a name="scenarios-and-configurations"></a>案例和組態
本節提供 web 伺服器及連接埠組態，Service Fabric 整合選項，建議您針對下列案例進行疑難排解的其他設定的組合：
 - 外部公開 ASP.NET Core 無狀態服務
 - 內部公開 ASP.NET Core 無狀態服務
 - 內部公開 ASP.NET Core 具狀態服務

**外部公開服務**是公開端點通常是透過負載平衡器叢集外部呼叫。

**僅供內部使用**服務是其中一個端點只能從叢集內呼叫。

> [!NOTE]
> 具狀態服務端點通常不應該公開到網際網路。 位於察覺不到 Service Fabric 服務解析度，例如 Azure Load Balancer，負載平衡器後方的叢集將無法公開具狀態服務。 這是因為負載平衡器將無法找出並將流量路由傳送至適當的具狀態服務複本。 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>外部公開 ASP.NET Core 無狀態服務
Kestrel 是建議的 web 伺服器前端公開於外部、 網際網路 HTTP 端點的服務。 在 Windows 中，HTTP.sys 可以提供連接埠共用功能，可讓您裝載在同一組節點使用相同的連接埠上的多個 web 服務。 在此案例中，web 服務的差別在於主機名稱或路徑，而無需依賴前端 proxy 或閘道來提供 HTTP 路由。
 
當公開至網際網路時，無狀態服務應該使用可透過負載平衡器的已知且穩定端點。 您會將此 URL 提供給您的應用程式的使用者。 建議使用下列設定：

|  |  | **注意事項** |
| --- | --- | --- |
| Web 伺服器 | Kestrel | Kestrel 是慣用的 web 伺服器，因為它支援的 Windows 和 Linux。 |
| 連接埠組態 | 靜態 | 已知的靜態連接埠應在 ServiceManifest.xml 的 `Endpoints` 組態中設定，例如 HTTP 為 80 或 443 為 HTTPS。 |
| ServiceFabricIntegrationOptions | None | 使用`ServiceFabricIntegrationOptions.None`選項設定 Service Fabric 整合中介軟體時，讓服務不會嘗試驗證傳入要求的唯一識別碼。 您的應用程式的外部使用者不會知道中介軟體會使用的唯一識別資訊。 |
| 執行個體計數 | -1 | 在典型的使用情況下，執行個體計數設定應該設定為 *-1*。 這麼做是為了讓執行個體可接收流量的負載平衡器的所有節點上。 |

如果多個外部公開的服務共用相同的節點集，您可以使用 HTTP.sys 使用唯一但穩定的 URL 路徑。 您可以修改設定 IWebHost 時提供的 URL 來完成。 請注意，這僅適用於 HTTP.sys。

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>僅供內部使用的無狀態 ASP.NET Core 服務
只會從叢集內呼叫的無狀態服務應該使用唯一的 URL 並動態指派連接埠，以確保多個服務之間的合作。 建議使用下列設定：

|  |  | **注意事項** |
| --- | --- | --- |
| Web 伺服器 | Kestrel | 雖然您可以針對內部的無狀態服務使用 HTTP.sys，Kestrel 就會是最佳的伺服器，以允許多個服務執行個體共用主機。  |
| 連接埠組態 | 動態指派 | 多個具狀態服務複本可能共用主機處理序或主機作業系統，因此需要唯一的連接埠。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 使用動態連接埠指派，此設定可防止稍早所述的誤用識別問題。 |
| InstanceCount | 任意 | 執行個體計數設定可以設定為操作本服務所需的任何值。 |

### <a name="internal-only-stateful-aspnet-core-service"></a>僅供內部使用的具狀態 ASP.NET Core 服務
只會從叢集內呼叫的具狀態服務應該使用動態指派連接埠，以確保多個服務之間的合作。 建議使用下列設定：

|  |  | **注意事項** |
| --- | --- | --- |
| Web 伺服器 | Kestrel | `HttpSysCommunicationListener`不設計由複本共用主機處理序的具狀態服務使用。 |
| 連接埠組態 | 動態指派 | 多個具狀態服務複本可能共用主機處理序或主機作業系統，因此需要唯一的連接埠。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 通过动态端口分配，此设置可以防止前面所述的错误标识问题。 |

## <a name="next-steps"></a>後續步驟
[使用 Visual Studio 偵錯 Service Fabric 應用程式](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
