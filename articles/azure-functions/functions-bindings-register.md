---
title: 注册 Azure Functions 绑定扩展
description: 了解如何根据你的环境注册 Azure Functions 绑定扩展。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740107"
---
# <a name="register-azure-functions-binding-extensions"></a>注册 Azure Functions 绑定扩展

Azure Functions 支持 HTTP 和现成的计时器。 若要使用其他服务，你需要安装或*注册*[绑定](./functions-triggers-bindings.md)扩展。 绑定扩展是通过 Azure Core Tools 或 NuGet 程序包提供的。 

下表指明了何时以及如何注册绑定。

| 開發環境 |註冊<br/> 在 Functions 1.x 中  |註冊<br/> 在 Functions 2.x 中  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 入口網站|自動|[自動 (含提示)](#azure-portal-development)|
|非 .NET 语言或本地 Azure Core Tools 开发|自動|[使用核心工具 CLI 命令](#local-development-azure-functions-core-tools)|
|使用 Visual Studio 2017 的 C# 類別庫|[使用 NuGet 工具](#c-class-library-with-visual-studio-2017)|[使用 NuGet 工具](#c-class-library-with-visual-studio-2017)|
|使用 Visual Studio Code 的 C# 類別庫|N/A|[使用 .NET Core CLI](#c-class-library-with-visual-studio-code)|

下列繫結類型是例外，不需要明確註冊，因為這些類型會在所有版本和環境中自動註冊：HTTP 與計時器。

> [!IMPORTANT]
> 本文的其余内容仅适用于 Functions 2.x。 在 Functions 1.x 中，除了[使用 Visual Studio 2017 创建 C# 类库](#local-csharp)时，绑定扩展不是显式注册的。

## <a name="azure-portal-development"></a>Azure 入口網站開發

當您建立函式或新增繫結時，系統會在觸發程序或繫結的擴充功能需要註冊時提示您。 請按一下 [安裝] 來註冊擴充功能，以回應提示。 安裝在取用方案上可能需要多達 10 分鐘。

針對指定的函式應用程式，您只須安裝每個延伸模組一次。 針對已支援但在入口網站中未提供的繫結，或是若要更新已安裝的延伸模組，您也可以[從入口網站手動安裝或更新 Azure Functions 繫結延伸模組](install-update-binding-extensions-manual.md)。  

## <a name="local-development-azure-functions-core-tools"></a>本機開發 Azure Functions Core Tools

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>包含 Visual Studio 2017 的 C# 類別庫

在 **Visual Studio 2017** 中，您可以使用 [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 命令，從「套件管理員主控台」中安裝套件，如下列範例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

在指定繫結的參考文章中，會提供要用於該繫結的套件名稱。 如需範例，請參閱[服務匯流排繫結參考文章的套件一節](functions-bindings-service-bus.md#packages---functions-1x)。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<target_version>`。 有效的版本會列在 [NuGet.org](https://nuget.org) 的個別套件頁面上。對應至 Functions 執行階段 1.x 或 2.x 的主要版本，會在繫結的參考文章中指定。

## <a name="c-class-library-with-visual-studio-code"></a>包含 Visual Studio Code 的 C# 類別庫

在 **Visual Studio Code** 中，您可以在 .NET Core CLI 中使用 [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 命令，從命令提示字元安裝套件，如下列範例所示：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

.NET Core CLI 只能用於 Azure Functions 2.x 開發。

在指定繫結的參考文章中，會提供要用於該繫結的套件名稱。 如需範例，請參閱[服務匯流排繫結參考文章的套件一節](functions-bindings-service-bus.md#packages---functions-1x)。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<target_version>`。 有效的版本會列在 [NuGet.org](https://nuget.org) 的個別套件頁面上。對應至 Functions 執行階段 1.x 或 2.x 的主要版本，會在繫結的參考文章中指定。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [Azure Functions 触发器和绑定示例](./functions-bindings-example.md)

