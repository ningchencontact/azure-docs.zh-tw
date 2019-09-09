---
title: 註冊 Azure Functions 系結延伸模組
description: 瞭解如何根據您的環境註冊 Azure Functions 系結延伸模組。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 93ced443a73d5499d8b305770c3c866c26d540f0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086471"
---
# <a name="register-azure-functions-binding-extensions"></a>註冊 Azure Functions 系結延伸模組

在 Azure Functions 2.x 版中，系結會當做函式運行[時間中的](./functions-triggers-bindings.md)個別套件來提供。 雖然 .NET 函式會透過 NuGet 封裝來存取系結，但擴充功能組合可讓其他函式透過設定設定來存取所有系結。

請考慮下列與系結延伸模組相關的專案：

- 系結延伸模組不會在函數1.x 中明確註冊，除非[使用 Visual Studio 建立C#類別庫](#local-csharp)。

- 預設支援 HTTP 和計時器觸發程式，而且不需要擴充功能。

下表指出註冊系結的時機和方式。

| 開發環境 |登錄<br/> 在 Functions 1.x 中  |登錄<br/> 在 Functions 2.x 中  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 入口網站|自動|自動|
|Non-.NET 語言或本機 Azure Core 工具開發|自動|[使用 Azure Functions Core Tools 和延伸模組配套](#extension-bundles)|
|C#使用 Visual Studio 的類別庫|[使用 NuGet 工具](#vs)|[使用 NuGet 工具](#vs)|
|使用 Visual Studio Code 的 C# 類別庫|N/A|[使用 .NET Core CLI](#vs-code)|

## <a name="extension-bundles"></a>本機開發的延伸模組配套

延伸模組配套是2.x 版執行時間的本機開發技術，可讓您將一組相容的函式系結延伸模組，新增至您的函式應用程式專案。 當您部署至 Azure 時，這些擴充套件會包含在部署套件中。 配套會讓 Microsoft 發佈的所有系結都可透過*主機 json*檔案中的設定來取得。 組合中定義的延伸模組套件彼此相容，這可協助您避免封裝之間的衝突。 在本機開發時，請確定您使用的是最新版本的[Azure Functions Core Tools](functions-run-local.md#v2)。

使用 Azure Functions Core Tools 或 Visual Studio Code，將延伸模組配套用於所有本機開發。

如果您未使用延伸模組配套，則必須先在本機電腦上安裝 .NET Core 2.x SDK，再安裝任何系結延伸模組。 配套會移除本機開發的這項需求。 

若要使用延伸模組配套，請更新*主機的 json*檔案，以包含下列`extensionBundle`專案：

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

下列屬性可用於`extensionBundle`：

| 屬性 | 描述 |
| -------- | ----------- |
| **`id`** | Microsoft Azure 函式擴充功能配套的命名空間。 |
| **`version`** | 要安裝的配套版本。 函數執行時間一律會挑選版本範圍或間隔所定義的最大允許版本。 上述版本值允許所有來自1.0.0 的套件組合版本，但不包括2.0.0。 如需詳細資訊，請參閱[指定版本範圍的間隔標記法](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards)。 |

配套版本隨著套件在配套中的增加而變更。 主要版本變更會在配套中的套件以主要版本遞增時進行，這通常會與函式執行時間的主要版本變更一致。  

預設配套所安裝的目前延伸模組集合會在此[副檔名 json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)檔案中列舉。

<a name="local-csharp"></a>

## <a name="vs"></a>具有\# Visual Studio 的 C 類別庫

在**Visual Studio**中，您可以使用 [[安裝套件](https://docs.microsoft.com/nuget/tools/ps-ref-install-package)] 命令從套件管理員主控台安裝套件，如下列範例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

用於指定系結的封裝名稱會在該系結的參考文章中提供。 如需範例，請參閱[服務匯流排繫結參考文章的套件一節](functions-bindings-service-bus.md#packages---functions-1x)。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<TARGET_VERSION>`。 有效的版本會列在 [NuGet.org](https://nuget.org) 的個別套件頁面上。對應至 Functions 執行階段 1.x 或 2.x 的主要版本，會在繫結的參考文章中指定。

如果您使用`Install-Package`來參考系結，則不需要使用[延伸](#extension-bundles)模組配套。 這個方法是 Visual Studio 內建的類別庫所特有。

## <a name="vs-code"></a>C#具有 Visual Studio Code 的類別庫

> [!NOTE]
> 我們建議使用[延伸](#extension-bundles)模組配套，讓函式自動安裝一組相容的系結延伸模組套件。

在**Visual Studio Code**中，使用 .NET Core CLI 中C#的[dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package)命令，從命令提示字元安裝類別庫專案的套件。 下列範例示範如何新增系結：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI 只能用於 Azure Functions 2.x 開發。

以`<BINDING_TYPE_NAME>`所需系結的參考文章中所提供的封裝名稱取代。 您可以在支援的系結[清單](./functions-triggers-bindings.md#supported-bindings)中找到所需的系結參考文章。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<TARGET_VERSION>`。 有效的版本會列在 [NuGet.org](https://nuget.org) 的個別套件頁面上。對應至 Functions 執行階段 1.x 或 2.x 的主要版本，會在繫結的參考文章中指定。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [Azure 函數觸發程式和系結範例](./functions-bindings-example.md)
