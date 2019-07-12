---
title: 註冊 Azure Functions 繫結延伸模組
description: 了解如何註冊您的環境為基礎的 Azure Functions 繫結延伸模組。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 5969c3e0d270b45347f8132b2d655ba2e56cb2c0
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625903"
---
# <a name="register-azure-functions-binding-extensions"></a>註冊 Azure Functions 繫結延伸模組

在 Azure Functions 第 2.x 中，[繫結](./functions-triggers-bindings.md)可用以從 functions 執行階段的個別封裝。 雖然.NET 函式會透過 NuGet 套件存取繫結，延伸模組套件組合會允許透過組態設定的所有繫結其他函式存取。

請考慮下列項目與繫結延伸模組：

- 繫結延伸模組未明確註冊函式中除非 1.x[建立C#使用 Visual Studio 的類別程式庫](#local-csharp)。

- HTTP 和計時器觸發程序會根據預設所支援，而且不需要延伸模組。

下表指出您何時及如何註冊繫結。

| 開發環境 |註冊<br/> 在 Functions 1.x 中  |註冊<br/> 在 Functions 2.x 中  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 入口網站|自動|自動|
|非.NET 語言或本機 Azure Core Tools 開發|自動|[使用 Azure Functions Core Tools 和延伸模組套件組合](#extension-bundles)|
|C#使用 Visual Studio 的類別庫|[使用 NuGet 工具](#vs)|[使用 NuGet 工具](#vs)|
|使用 Visual Studio Code 的 C# 類別庫|N/A|[使用 .NET Core CLI](#vs-code)|

## <a name="extension-bundles"></a>適用於本機開發的延伸模組套件組合

延伸模組套件組合是版本 2.x 執行階段，讓您新增相容的集合，繫結至您的函式應用程式專案的延伸模組的函式的本機開發技術。 當您部署至 Azure 時，這些延伸模組套件則包含部署套件中。 套件組合會透過在設定的 Microsoft 所發行的所有繫結*host.json*檔案。 組合中定義的延伸模組套件會彼此相容，這可協助您避免套件之間的衝突。 當開發在本機，請確定您使用最新版[Azure Functions Core Tools](functions-run-local.md#v2)。

使用適用於所有的本機開發使用 Azure Functions Core Tools] 或 [Visual Studio Code 的延伸模組套件組合。

如果您不使用延伸模組套件組合，您必須安裝.NET Core 2.x SDK 安裝的任何繫結延伸模組之前，先在本機電腦上。 套件組合會移除這項需求適用於本機開發。 

若要使用延伸模組套件組合，更新*host.json*檔案，以包含下列項目`extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

下列屬性可用於`extensionBundle`:

| 屬性 | 描述 |
| -------- | ----------- |
| **`id`** | Microsoft Azure 函式延伸模組套件組合命名空間。 |
| **`version`** | 若要安裝套件組合的版本。 Functions 執行階段一律會挑選版本範圍或間隔所定義的最高允許版本。 上述的版本值可讓所有的套件組合版本，從最高的 1.0.0，但不是包括 2.0.0。 如需詳細資訊，請參閱 <<c0> [ 間隔標記法來指定版本範圍](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards)。 |

以套件組合變更的套件組合版本遞增。 套件組合中的增加主要版本，通常會伴隨 Functions 執行階段的主要版本中的變更時，就會發生主要版本變更。  

在此列舉目前集合的預設套件組合安裝的延伸模組[extensions.json 檔案](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)。

<a name="local-csharp"></a>

## <a name="vs"></a> C\#使用 Visual Studio 類別庫

在  **Visual Studio**，您可以從 Package Manager Console 來安裝封裝[Install-package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package)命令，如下列範例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

參考文件中提供特定繫結所使用的封裝名稱，該繫結。 如需範例，請參閱[服務匯流排繫結參考文章的套件一節](functions-bindings-service-bus.md#packages---functions-1x)。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<TARGET_VERSION>`。 有效的版本會列在 [NuGet.org](https://nuget.org) 的個別套件頁面上。對應至 Functions 執行階段 1.x 或 2.x 的主要版本，會在繫結的參考文章中指定。

如果您使用`Install-Package`若要參考之繫結，您不需要使用[延伸模組套件組合](#extension-bundles)。 這種方法是專為 Visual Studio 中建置的類別程式庫。

## <a name="vs-code"></a> C#使用 Visual Studio Code 的類別庫

> [!NOTE]
> 我們建議您使用[延伸模組套件組合](#extension-bundles)能夠自動安裝相容的一份繫結延伸模組套件的函式。

在  **Visual Studio Code**，安裝套件C#類別庫專案，從命令提示字元使用[dotnet 新增套件](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package).NET Core CLI 命令。 下列範例會示範如何將繫結，新增：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI 只能用於 Azure Functions 2.x 開發。

取代`<BINDING_TYPE_NAME>`如您所需的繫結參考文章中所提供的套件的名稱。 您可以找到所需的繫結參考文章中[支援的繫結清單](./functions-triggers-bindings.md#supported-bindings)。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<TARGET_VERSION>`。 有效的版本會列在 [NuGet.org](https://nuget.org) 的個別套件頁面上。對應至 Functions 執行階段 1.x 或 2.x 的主要版本，會在繫結的參考文章中指定。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [Azure 函式觸發程序和繫結範例](./functions-bindings-example.md)
