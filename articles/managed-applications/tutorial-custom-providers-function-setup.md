---
title: 設定 Azure 自訂提供者的 Azure Functions
description: 本教學課程將介紹如何建立 Azure 函式，並將其設定為使用 Azure 自訂提供者
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799117"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>設定 Azure 自訂提供者的 Azure Functions

自訂提供者可讓您在 Azure 上自訂工作流程。 自訂提供者是 Azure 和 `endpoint` 之間的合約。 本教學課程會詳細解說要讓 Azure 函式作為自訂提供者 `endpoint` 所需進行的設定程序。

本教學課程分成下列步驟：

- 建立 Azure 函式
- 安裝 Azure 資料表繫結
- 更新 RESTful HTTP 方法
- 新增 Azure Resource Manager NuGet 套件

本教學課程會以下列教學課程為基礎來建置：

- [透過 Azure 入口網站建立您的第一個 Azure 函式](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>建立 Azure 函式

> [!NOTE]
> 在本教學課程中，我們會使用 Azure 函式建立簡單的服務端點，但自訂提供者可以使用任何可公開存取的 `endpoint`。 Azure Logic Apps、Azure API 管理和 Azure Web Apps 是一些不錯的替代項目。

若要開始本教學課程，請遵循[在 Azure 入口網站中建立您的第一個 Azure 函式](../azure-functions/functions-create-first-azure-function.md)教學課程。 本教學課程會建立可在 Azure 入口網站中修改的 .NET Core Webhook 函式。

## <a name="install-azure-table-bindings"></a>安裝 Azure 資料表繫結

本節會詳細解說用於安裝 Azure 資料表儲存體繫結的快速步驟。

1. 瀏覽至 HttpTrigger 的 `Integrate` 索引標籤。
2. 按一下 `+ New Input`。
3. 選取 `Azure Table Storage`。
4. 安裝 `Microsoft.Azure.WebJobs.Extensions.Storage` (如果尚未安裝)。
5. 將 `Table parameter name` 更新為 "tableStorage"，並將 `Table name` 更新為 "myCustomResources"。
6. 儲存更新後的輸入參數。

![自訂提供者概觀](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>更新 RESTful HTTP 方法

本節會詳細解說用於設定 Azure 函式以納入自訂提供者 RESTful 要求方法的快速步驟。

1. 瀏覽至 HttpTrigger 的 `Integrate` 索引標籤。
2. 將 `Selected HTTP methods` 更新為：GET、POST、DELETE 和 PUT。

![自訂提供者概觀](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>修改 csproj

> [!NOTE]
> 如果目錄中遺漏 csproj，您可以手動新增，或者，當函式上安裝了 `Microsoft.Azure.WebJobs.Extensions.Storage` 擴充功能後也會出現。

接下來，我們會更新 csproj 檔案來納入實用的 NuGet 程式庫，以讓您更輕鬆地剖析從自訂提供者傳入的要求。 請遵循[從入口網站新增擴充功能](../azure-functions/install-update-binding-extensions-manual.md)中的步驟，並更新 csproj 以納入下列套件參考：

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

csproj 檔案範例：

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>後續步驟

在本文中，我們已將 Azure 函式設定為 Azure 自訂提供者 `endpoint`。 請移至下一篇文章，以了解如何撰寫 RESTful 自訂提供者 `endpoint`。

- [教學課程：撰寫 RESTful 自訂提供者端點](./tutorial-custom-providers-function-authoring.md)
