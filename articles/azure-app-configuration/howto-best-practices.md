---
title: Azure 應用程式組態的最佳做法 |Microsoft Docs
description: 了解如何善用 Azure 應用程式設定
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3d9a597e7ced631627a121f3f0757e472f9a4bae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393581"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 應用程式組態的最佳作法

這篇文章會討論常見的模式和最佳作法，當您使用 Azure 應用程式設定。

## <a name="key-groupings"></a>索引鍵的群組

應用程式組態會提供用來組織按鍵的兩個選項：

* 索引鍵的前置詞
* 標籤

您可以使用一個或兩個選項，將您的金鑰。

*索引鍵前置詞*開頭部分的索引鍵。 您可以以邏輯方式群組一組索引鍵，其名稱中使用相同的前置詞。 前置詞可以包含多個元件，例如連線依分隔符號、 `/`，類似於 URL 路徑，以形成的命名空間。 當您在一個應用程式組態存放區中儲存許多應用程式、 元件服務和環境的索引鍵，這種階層很有用。

要牢記在心的重點是索引鍵是您的應用程式程式碼參考來擷取對應的設定值。 不應該變更金鑰，或是您必須修改您的程式碼每次發生的。

*標籤*會根據索引鍵屬性。 它們用來建立機碼的變體。 比方說，您也可以將標籤指派給多個版本的金鑰。 版本可能是反覆項目、 環境或某些其他內容資訊。 您的應用程式可以要求完全不同組的索引鍵的值，指定另一個標籤。 如此一來，所有的索引鍵參考維持不變，您的程式碼中的。

## <a name="key-value-compositions"></a>索引鍵 / 值組合

應用程式組態會將與它一起儲存為獨立的實體的所有索引鍵。 應用程式設定不會嘗試推斷索引鍵之間的任何關聯性，或繼承其階層架構為基礎的索引鍵值。 您可以使用彙總多組金鑰，不過，搭配您的應用程式程式碼中堆疊的適當設定的標籤。

讓我們看看以下範例。 假設您有設定，名為**Asset1**，其值可能會有所不同根據開發環境。 您建立名為"Asset1 」 使用空白標籤和名為 「 開發 」 標籤。 您將放在第一個標籤中的預設值**Asset1**，並將特定值如 「 開發 」 放在後者中。

在您的程式碼中，您第一次擷取的索引鍵值，而不需要任何標籤，，然後您擷取同一組索引鍵值的第二次的 「 開發 」 標籤。 當您第一次擷取的值時，則會覆寫先前的索引鍵的值。 .NET Core 組態系統，可讓您 「 堆疊 」 多個組態彼此的資料集。 如果索引鍵存在於一個以上的集合，則會使用最後一集，其中包含它。 透過新式的程式設計架構，例如.NET Core，您可以此堆疊的功能免費如果您使用的原生組態提供者來存取應用程式設定。 下列程式碼片段會示範如何實作在.NET Core 應用程式堆疊：

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Use(KeyFilter.Any, LabelFilter.Null)
           .Use(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>應用程式設定啟動程序

若要存取的應用程式組態存放區，您可以使用其連接字串，在 Azure 入口網站中可用。 因為連接字串包含認證資訊，所以被視為機密資料。 這些密碼必須儲存在 Azure Key Vault，以及您的程式碼必須向 Key Vault 來擷取它們。

更好的選項是使用 Azure Active Directory 中的受管理的身分識別功能。 使用受管理的身分識別，您需要您的應用程式組態存放區只啟動程序的存取權的應用程式設定端點 URL。 您可以將 URL 內嵌在應用程式程式碼 (例如，在*appsettings.json*檔案)。 請參閱[與 Azure 整合管理身分識別](howto-integrate-azure-managed-service-identity.md)如需詳細資訊。

## <a name="app-or-function-access-to-app-configuration"></a>應用程式或函式存取應用程式設定

您可以提供存取應用程式設定 web 應用程式或函式使用下列方法之一：

* 透過 Azure 入口網站中，輸入您的應用程式組態存放區中的 App Service 應用程式設定的連接字串。
* 您的應用程式組態存放區的連接字串儲存在 Key Vault 並[參考從 App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)。
* 使用 Azure 受管理的身分識別來存取應用程式組態存放區。 如需詳細資訊，請參閱 <<c0> [ 與 Azure 整合管理身分識別](howto-integrate-azure-managed-service-identity.md)。
* 從應用程式設定的設定推送至 App Service 中。 應用程式組態會提供將資料傳送到 App Service 的直接匯出函式 （在 Azure 入口網站和 Azure CLI）。 使用此方法，您不需要完全變更應用程式程式碼。

## <a name="next-steps"></a>後續步驟

* [索引鍵和值](./concept-key-value.md)
