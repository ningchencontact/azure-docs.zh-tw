---
title: 如何在 Azure Dev Spaces 中使用自訂 NuGet 摘要
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: 在 Azure Dev Spaces 中使用自訂 NuGet 摘要以存取及使用 NuGet 套件。
keywords: Docker、Kubernetes、Azure、AKS、Azure Container Service、容器
manager: gwallace
ms.openlocfilehash: ee14d999872f6e739321c144831d60a4ae6f9388
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279957"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>在 Azure Dev Spaces 中使用自訂 NuGet 摘要

NuGet 摘要會提供在專案中包含套件來源的便利方式。 Azure Dev Spaces 需要存取此摘要，才能正確地將相依性安裝在 Docker 容器中。

## <a name="set-up-a-nuget-feed"></a>設定 NuGet 摘要

在 [`PackageReference`] 節點下的 `*.csproj` 檔案中，新增相依性的[套件參考](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files)。 例如︰

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

在專案資料夾中建立[nuget.exe](https://docs.microsoft.com/nuget/reference/nuget-config-file)檔案，並設定 nuget 摘要的 `packageSources` 和 `packageSourceCredentials` 區段。 `packageSources` 區段包含您的摘要 url，必須可從您的 AKS 叢集存取。 `packageSourceCredentials` 是用來存取摘要的認證。 例如︰

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

更新您的 Dockerfile，將 `NuGet.Config` 檔案複製到映射。 例如︰

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> 在 Windows 上，`NuGet.Config`、`Nuget.Config`和 `nuget.config` 全部都可做為有效的檔案名。 在 Linux 上，只有 `NuGet.Config` 是此檔案的有效檔案名。 因為 Azure Dev Spaces 使用 Docker 和 Linux，所以這個檔案必須命名為 `NuGet.Config`。 您可以手動或藉由執行 `dotnet restore --configfile nuget.config`來修復命名。


如果您使用 Git，則不應該在版本控制中擁有 NuGet 摘要的認證。 將 `NuGet.Config` 新增至專案的 `.gitignore`，讓 `NuGet.Config` 檔案不會加入至版本控制。 Azure Dev Spaces 在容器映射建立過程中需要這個檔案，但根據預設，它會遵循在同步處理期間 `.gitignore` 和 `.dockerignore` 中定義的規則。 若要變更預設值，並允許 Azure Dev Spaces 同步處理 `NuGet.Config` 檔案，請更新 `azds.yaml` 檔案：

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

如果您不是使用 Git，可以略過此步驟。

下次當您在 Visual Studio Code 或 Visual Studio 中執行 `azds up` 或點擊 `F5` 時，Azure Dev Spaces 會同步處理 `NuGet.Config` 檔案，使用它來安裝套件相依性。

## <a name="next-steps"></a>後續步驟

深入瞭解[NuGet 及其運作方式](https://docs.microsoft.com/nuget/what-is-nuget)。