---
title: 設定 ASP.NET Core 應用程式-Azure App Service |Microsoft Docs
description: 了解如何設定 Azure App Service 中運作的 ASP.NET Core 應用程式
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: f2781e3cc2433f73ba7ff33e5c452e29de746adf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956197"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>設定 Linux 的 Azure App Service 的 ASP.NET Core 應用程式

ASP.NET Core 應用程式必須部署為編譯的二進位檔。 Visual Studio 發佈工具建置解決方案，並接著將部署的已編譯的二進位檔直接，而 App Service 部署引擎會先部署程式碼存放庫，，然後編譯二進位檔。

本指南提供重要概念和 ASP.NET core 的指示在 App Service 中使用內建的 Linux 容器的開發人員。 如果您從未使用過 Azure App Service，請遵循[ASP.NET Core 快速入門](quickstart-dotnetcore.md)並[ASP.NET Core 與 SQL Database 教學課程](tutorial-dotnetcore-sqldb-app.md)第一次。

## <a name="show-net-core-version"></a>顯示.NET Core 版本

若要顯示目前的.NET Core 版本，請執行下列命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要顯示所有支援的.NET Core 版本，請執行下列命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>設定.NET Core 版本

執行下列命令[Cloud Shell](https://shell.azure.com)將.NET Core 版本為 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然後，您可以使用標準 ASP.NET 模式：

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

如果您使用相同的名稱，在 App Service 中，並在設定的應用程式設定*Web.config*，App Service 的值會優先於 Web.config 值。 Web.config 值可讓您偵錯在本機的應用程式，但 App Service 值可讓您執行使用生產環境設定的產品中的應用程式。 連接字串的運作方式相同。 如此一來，您可以讓您的程式碼存放庫之外的您的應用程式祕密，並存取適當的值，而不需要變更您的程式碼。

## <a name="get-detailed-exceptions-page"></a>取得詳細的例外狀況頁面

當您 ASP.NET 應用程式在 Visual Studio 偵錯工具中產生例外狀況時，瀏覽器顯示詳細的例外狀況 頁面中，但是在 App Service 中該頁面會取代泛型**HTTP 500**錯誤或**錯誤時發生處理您的要求。** 訊息。 若要在 App Service 中顯示詳細的例外狀況] 頁面，加入`ASPNETCORE_ENVIRONMENT`應用程式設定設為 [您的應用程式中執行下列命令<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>偵測 HTTPS 工作階段

在 App Service 中，[SSL 終止](https://wikipedia.org/wiki/TLS_termination_proxy)會在網路負載平衡器上發生，因此所有的 HTTPS 要求都會以未加密 HTTP 要求的形式進入您的應用程式。 如果您的應用程式邏輯必須知道如果使用者要求是否會加密，設定轉送標頭中介軟體*Startup.cs*:

- 設定與中的介軟體[ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions)轉送給`X-Forwarded-For`並`X-Forwarded-Proto`中的標頭`Startup.ConfigureServices`。
- 因此中, 介軟體可以信任 App Service 的負載平衡器，則您可以加入已知的網路，私人 IP 位址範圍。
- 叫用[UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders)方法中的`Startup.Configure`再呼叫其他中介軟體。

將所有的三個項目放在一起，您的程式碼看起來如下列範例所示：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

如需詳細資訊，請參閱 <<c0> [ 設定 ASP.NET Core 以處理 proxy 伺服器和負載平衡器](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)。

## <a name="deploy-multi-project-solutions"></a>部署多專案的方案

當您將 ASP.NET 存放庫部署到具有部署引擎 *.csproj*檔案的根目錄中，引擎會將專案部署。 當您部署使用 ASP.NET 存放庫 *.sln*檔案的根目錄中，引擎會挑選第一個網站或 Web 應用程式專案，它會尋找與 App Service 應用程式。 可以，引擎不挑選您想要的專案。

若要部署多專案方案，您可以指定專案以在 App Service 中使用兩個不同的方式：

### <a name="using-deployment-file"></a>使用.deployment 檔案

新增 *.deployment*檔案至存放庫根目錄，並新增下列程式碼：

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>使用應用程式設定

在  <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>，執行下列 CLI 命令，將應用程式設定新增至您的 App Service 應用程式。 取代 *\<應用程式名稱 >* ， *\<資源群組名稱 >* ，以及 *\<專案名稱 >* 以適當的值.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在瀏覽器中開啟 SSH 工作階段

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：ASP.NET Core 應用程式搭配 SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](app-service-linux-faq.md)