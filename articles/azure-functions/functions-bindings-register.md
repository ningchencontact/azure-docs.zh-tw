---
title: 註冊 Azure Functions 繫結延伸模組
description: 了解如何註冊您的環境為基礎的 Azure Functions 繫結延伸模組。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 53eb5fc9389d913ecacec3729a06e47a1c2bf56b
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864556"
---
# <a name="register-azure-functions-binding-extensions"></a>註冊 Azure Functions 繫結延伸模組

在 Azure Functions 第 2.x 中，[繫結](./functions-triggers-bindings.md)可用以從 functions 執行階段的個別封裝。 雖然.NET 函式會透過 NuGet 套件存取繫結，延伸模組套件組合會允許透過組態設定的所有繫結其他函式存取。

請考慮下列項目與繫結延伸模組：

- 繫結延伸模組未明確註冊函式中除非 1.x[建立C#使用 Visual Studio 2019 的類別程式庫](#local-csharp)。

- HTTP 和計時器觸發程序會根據預設所支援，而且不需要延伸模組。

下表指出您何時及如何註冊繫結。

| 開發環境 |登錄<br/> 在 Functions 1.x 中  |登錄<br/> 在 Functions 2.x 中  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 入口網站|自動|自動|
|非.NET 語言或本機 Azure Core Tools 開發|自動|[使用 Azure Functions Core Tools 和延伸模組套件組合](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|C#使用 Visual Studio 2019 的類別程式庫|[使用 NuGet 工具](#c-class-library-with-visual-studio-2019)|[使用 NuGet 工具](#c-class-library-with-visual-studio-2019)|
|使用 Visual Studio Code 的 C# 類別庫|N/A|[使用 .NET Core CLI](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>使用 Azure Functions Core Tools 和延伸模組套件組合的本機開發

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2019"></a>C#使用 Visual Studio 2019 的類別庫

在  **Visual Studio 2019**，您可以從 Package Manager Console 來安裝封裝[Install-package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package)命令，如下列範例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

參考文件中提供特定繫結所使用的封裝名稱，該繫結。 如需範例，請參閱[服務匯流排繫結參考文章的套件一節](functions-bindings-service-bus.md#packages---functions-1x)。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<TARGET_VERSION>`。 有效的版本會列在 [NuGet.org](https://nuget.org) 的個別套件頁面上。對應至 Functions 執行階段 1.x 或 2.x 的主要版本，會在繫結的參考文章中指定。

## <a name="c-class-library-with-visual-studio-code"></a>包含 Visual Studio Code 的 C# 類別庫

在 **Visual Studio Code** 中，您可以在 .NET Core CLI 中使用 [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 命令，從命令提示字元安裝套件，如下列範例所示：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

.NET Core CLI 只能用於 Azure Functions 2.x 開發。

在指定繫結的參考文章中，會提供要用於該繫結的套件名稱。 如需範例，請參閱[服務匯流排繫結參考文章的套件一節](functions-bindings-service-bus.md#packages---functions-1x)。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<TARGET_VERSION>`。 有效的版本會列在 [NuGet.org](https://nuget.org) 的個別套件頁面上。對應至 Functions 執行階段 1.x 或 2.x 的主要版本，會在繫結的參考文章中指定。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [Azure 函式觸發程序和繫結範例](./functions-bindings-example.md)

