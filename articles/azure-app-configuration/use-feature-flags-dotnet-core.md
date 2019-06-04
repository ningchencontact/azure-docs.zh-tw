---
title: 如何在 .NET Core 應用程式中使用功能旗標的教學課程 | Microsoft Docs
description: 在本教學課程中，您將了解如何在 .NET Core 應用程式中實作功能旗標
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: b0e48a0db63eded9e9c4921d33b03af39656ce0d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299270"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>教學課程：在 .NET Core 應用程式中使用功能旗標

.NET Core 功能管理程式庫可讓您在 .NET 或 ASP.NET Core 應用程式中以慣用方式實作功能旗標。 其可讓您以更為宣告的方式在程式碼中新增功能旗標，因此您不必以手動方式為功能旗標撰寫所有 `if` 陳述式。 其會在幕後管理功能旗標的生命週期 (例如，重新整理及快取旗標狀態、保證在要求呼叫期間旗標的狀態不會變化)。 此外，ASP.NET Core 程式庫還提供現成可用的整合，包括 MVC 控制器動作、檢視、路由和中介軟體。

[將功能旗標新增至 ASP.NET Core 應用程式](./quickstart-feature-flag-aspnet-core.md)快速入門會示範數種方法，讓您了解如何在 ASP.NET Core 應用程式中新增功能旗標。 本教學課程會更為詳細地說明這些方法。 如需完整的參考，請參閱 [ASP.NET Core 功能管理文件](https://go.microsoft.com/fwlink/?linkid=2091410)。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 在應用程式的重要部分新增功能旗標來控制功能的可用性。
> * 在使用應用程式組態來管理功能旗標時與其進行整合。

## <a name="setup"></a>設定

.NET Core 功能管理員 `IFeatureManager` 會從架構的原生組態系統取得功能旗標。 因此，您可以使用 .NET Core 所支援的組態來源 (包括本機 appsettings.json  檔或環境變數) 來定義應用程式的功能旗標。 功能管理員會仰賴 .NET Core 相依性插入。 您可以使用標準慣例來註冊功能管理服務。

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

根據預設，功能管理員會從 .NET Core 組態資料的 "FeatureManagement" 區段擷取功能旗標。 下列範例會發出指示，讓其改為讀取稱為 "MyFeatureFlags" 的不同區段。

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

如果您在功能旗標中使用篩選條件，則需要納入其他程式庫並予以註冊。 下列範例示範如何使用稱為 **PercentageFilter"** 的內建功能篩選條件。

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

若要有效運作，請在應用程式之外保留功能旗標並分開管理。 這樣做可讓您隨時修改旗標狀態，並讓那些變更在應用程式中立即生效。 應用程式組態可讓您透過專用的入口網站 UI 在集中的位置組織和控制所有功能旗標，並透過其 .NET Core 用戶端程式庫直接將旗標傳遞至應用程式。 若要將 ASP.NET Core 應用程式連線至應用程式組態，最簡單的方式是透過組態提供者 `Microsoft.Extensions.Configuration.AzureAppConfiguration`。 您可以藉由將下列內容新增至 Program.cs  檔案，以在程式碼中使用此 NuGet 套件：

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
           .ConfigureAppConfiguration((hostingContext, config) => {
               var settings = config.Build();
               config.AddAzureAppConfiguration(options => {
                   options.Connect(settings["ConnectionStrings:AppConfig"])
                          .UseFeatureFlags();
                });
           })
           .UseStartup<Startup>();
```

功能旗標值應該會隨著時間而變更。 根據預設，功能管理員會每隔 30 秒重新整理功能旗標值一次。 您可以在上面的 `options.UseFeatureFlags()` 呼叫中使用不同的輪詢間隔。

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>功能旗標宣告

每個功能旗標都有兩個部分：名稱以及一份清單，此清單內含可用來評估功能狀態是否為「開啟」  (也就是當其值為 `True` 時) 的一或多個篩選條件。 篩選條件會定義應該開啟功能的使用案例。 如果功能旗標有多個篩選條件，則會依序周遊篩選條件清單，直到其中一個篩選條件確定應啟用功能。 此時，便會將功能旗標視為「開啟」  ，並跳過剩餘的篩選條件結果。 如果沒有篩選條件指出應啟用功能，則功能旗標是「關閉」  。

功能管理員支援以 appsettings.json  作為功能旗標的組態來源。 下列範例示範如何在 json 檔案中設定功能旗標。

```JSON
"FeatureManagement": {
    "FeatureX": true, // Feature flag set to on
    "FeatureY": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

依照慣例，我們會將此 json 文件的 `FeatureManagement` 區段用於功能旗標設定。 上述範例顯示了三個功能旗標及其在 EnabledFor  屬性中所定義的篩選條件：

* **FeatureA** 是「開啟」  。
* **FeatureB** 是「關閉」  。
* **FeatureC** 會指定名為 Percentage  且具有 Parameters  屬性的篩選條件。 Percentage  是可設定的篩選條件範例，而且其指定 **FeatureC** 旗標在機率為 50% 時會「開啟」  。

## <a name="referencing"></a>參考

雖非必要，但請將功能旗標定義為 `enum` 變數，以利在程式碼中加以參考。

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-check"></a>功能旗標檢查

功能管理的基本模式是先檢查功能旗標是否設定為「開啟」  ，如果是，則執行以括號括住的動作。

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>相依性插入

在 ASP.NET Core MVC 中，功能管理員 `IFeatureManager` 可透過相依性插入來加以存取。

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-action"></a>控制器動作

在 MVC 控制器中，`Feature` 屬性可用來控制是要啟用整個控制器類別還是特定動作。 下列 `HomeController` 控制器需要在 FeatureA  「開啟」  時才能執行其所包含的動作。

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

上面出現的下列 `Index` 動作需要在 FeatureA  「開啟」  時才能執行。

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

當 MVC 控制器或動作因為控制功能旗標「關閉」  而遭到封鎖，系統便會呼叫已註冊的 `IDisabledFeatureHandler`。 預設的 `IDisabledFeatureHandler` 便會對用戶端傳回沒有回應主體的 404 狀態碼。

## <a name="view"></a>檢視

在 MVC 檢視中，`<feature>` 標記可用來根據功能旗標是否已啟用來呈現內容。

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filter"></a>MVC 篩選條件

您可以將 MVC 篩選條件設定為根據功能旗標的狀態來加以啟動。 下列程式碼會新增名為 `SomeMvcFilter` 的 MVC 篩選條件。 只有在 FeatureA  已啟用時才會在 MVC 管線內觸發此篩選條件。

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="route"></a>路由

路由可以根據功能旗標來動態公開。 下列程式碼會新增路由，其只有在 FeatureA  已啟用時才會將 `Beta` 設定為預設控制器。

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>中介軟體

功能旗標可用來根據條件來新增應用程式分支和中介軟體。 下列程式碼只有在 FeatureA  已啟用時才會在要求管線中插入中介軟體元件。

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

這會建置更泛型的功能以根據功能旗標來為整個應用程式建立分支。

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何藉由利用 `Microsoft.FeatureManagement` 程式庫，在 ASP.NET Core 應用程式中實作功能旗標。 如需 ASP.NET Core 和應用程式組態中的功能管理支援詳細資訊，請參閱下列資源。

* [ASP.NET Core 功能旗標程式碼範例](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Microsoft.FeatureManagement 文件](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [編輯功能旗標](./manage-feature-flags.md)
