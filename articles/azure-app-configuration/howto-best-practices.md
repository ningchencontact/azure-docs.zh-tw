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
ms.openlocfilehash: d1275a48de5cad9321186ba20860d853b8ce55ad
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413610"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 應用程式組態的最佳作法

使用 Azure 應用程式組態時，這篇文章會討論常見的模式和作法。

## <a name="key-groupings"></a>索引鍵的群組

應用程式設定提供用來組織按鍵的兩個選項： 索引鍵的首碼或標籤。 您可以使用一個或兩個。

索引鍵的前置詞為開頭部分的索引鍵。 您可以以邏輯方式群組一組索引鍵，其名稱中使用相同的前置詞。 前置詞可以包含多個元件連接在一起以分隔符號例如`/`，類似於 URL 路徑，以形成的命名空間。 當您將許多應用程式、 元件服務和環境的金鑰儲存在一個應用程式組態存放區，這種階層很有用。 要牢記在心的重點是索引鍵是您的應用程式程式碼參考來擷取對應的設定值。 索引鍵應該不會變更，否則您就必須修改程式碼每次發生。

標籤是根據索引鍵的屬性。 它們用來建立機碼的變體。 比方說，您也可以將標籤指派給多個版本的金鑰。 版本可以是反覆項目、 環境或其他內容的資訊。 您的應用程式可以要求完全不同組的索引鍵-值只是藉由指定另一個標籤。 索引鍵的所有參考都能都保持不變。

## <a name="key-value-compositions"></a>索引鍵 / 值組合

應用程式組態會將與它一起儲存為獨立的實體的所有索引鍵。 它不會嘗試推斷索引鍵之間的任何關聯性，或繼承其階層架構為基礎的索引鍵值。 您可以彙總多個索引鍵集，不過，使用標籤搭配您的應用程式程式碼中堆疊的適當設定。

讓我們看看以下範例。 設定了**Asset1**其值可能不同的 「 開發 」 環境。 您可以建立名為"Asset1"空白標籤和稱為 「 開發 」 標籤的索引鍵。 將預設值**Asset1**前者和中的任何特定值如 「 開發 」 後者。 在您的程式碼中，您可以先擷取索引鍵值，而不需要任何標籤，然後這些覆寫任何先前的值相同的索引鍵的 「 開發 」 標籤。 如果您使用新式的程式設計架構，例如.NET Core 時，您可以取得此堆疊的功能免費如果您使用的原生組態提供者來存取應用程式設定。 下列程式碼片段會示範如何實作堆疊中的.NET Core 應用程式。

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

若要存取的應用程式組態存放區，您可以使用其連接字串，在 Azure 入口網站中可用。 包含認證資訊的連接字串，並會被視為機密資料。 他們必須儲存在金鑰保存庫。 更好的選項是使用 Azure 受管理的身分識別。 使用此方法，您只需要應用程式設定來啟動您的組態存放區存取的端點 URL。 您可以將 URL 內嵌在應用程式程式碼 (例如，在*appsettings.json*檔案)。 請參閱[與 Azure 整合管理身分識別](howto-integrate-azure-managed-service-identity.md)如需詳細資訊。

## <a name="web-app-or-function-access-to-app-configuration"></a>Web 應用程式或函式存取應用程式設定

您可以輸入到您的應用程式組態存放區的連接字串至 App Service 應用程式設定，透過 Azure 入口網站。 您可以也將其儲存在 Key Vault 並[參考從 App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)。 您也可以使用 Azure 受控身分識別來存取組態存放區。 請參閱[與 Azure 整合管理身分識別](howto-integrate-azure-managed-service-identity.md)如需詳細資訊。

或者，可以將組態從應用程式組態中，推送至 App Service 中。 應用程式組態會提供將資料傳送到 App Service 的直接匯出函式 （在 Azure 入口網站和 CLI）。 使用此方法，您不需要完全變更應用程式程式碼。

## <a name="next-steps"></a>後續步驟

* [索引鍵和值](./concept-key-value.md)
