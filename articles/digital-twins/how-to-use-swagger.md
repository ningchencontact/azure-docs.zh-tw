---
title: 了解如何使用 Azure Digital Twins Swagger | Microsoft Docs
description: 如何使用 Azure Digital Twins Swagger
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 3bc365c204ab75a2f136c3e26c4b598b25f66114
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093484"
---
# <a name="how-to-use-azure-digital-twins-swagger"></a>如何使用 Azure Digital Twins Swagger

每個已佈建的 Azure Digital Twins 執行個體都包含本身自動產生的 Swagger 參考文件。

[Swagger](https://swagger.io/) (或[OpenAPI](https://www.openapis.org/)) 會將複雜 API 資訊結合至可互動且與語言無關的參考資源。 具體來說，若要對 API 執行作業，Swagger 會針對要使用的 JSON 承載、HTTP 方法和特定端點提供重要參考資料。

## <a name="swagger-summary"></a>Swagger 摘要

Swagger 會提供可互動的 API 摘要，包括：

* API 和物件模型資訊。
* 指定必要要求承載、標頭、參數、內容路徑和 HTTP 方法的 REST API 端點。
* API 功能測試。
* 用於驗證和確認 HTTP 回應的範例回應資訊。
* 錯誤碼資訊。

因此，Swagger 是很便利的工具，可協助您開發和測試對管理 API 發出的呼叫。

> [!TIP]
> 如需參考，Swagger 搶先預覽中有 API 功能集的示範。
> 這些內容位在 [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger)。

您可以在以下位置存取您自己產生的管理 API Swagger 文件：

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| 自訂屬性名稱 | 取代為 |
| --- | --- |
| *yourInstanceName* | Azure Digital Twins 執行個體的名稱 |
| *yourLocation* | 裝載您執行個體的伺服器區域 |

## <a name="reference-material"></a>參考資料

自動產生的參考資料會說明重要概念和物件模型。

透過簡單摘要來說明 API：

![Swagger 最上層][1]

也會列出核心 API 物件模型：

![Swagger 模型][2]

您可以按一下每個列出的物件模型，以取得更詳細的主要屬性摘要：

![Swagger 模型][3]

使用產生的 Swagger 物件模型來查看所有可用的 Azure Digital Twins [物件和 API](./concepts-objectmodel-spatialgraph.md) 相當方便。 對開發人員而言，這是他們在 Azure Digital Twins 上建置解決方案時會使用的絕佳資源。

## <a name="endpoint-summary"></a>端點摘要

Swagger 也提供 API 架構中所有端點的整體概觀。

每個列出的端點也會包含必要的要求資訊，例如：

* 必要參數。
* 必要參數資料類型。
* 要存取資源的 HTTP 方法。

![Swagger 端點][4]

您可以按一下每個資源，以查看更詳細的概觀。

## <a name="using-swagger-to-test-endpoints"></a>使用 Swagger 來測試端點

Swagger 所提供的強大功能之一是能夠**試用**，或直接透過文件 UI 測試 API 端點。

點進特定端點後，您會看到 [試用] 按鈕：

![Swagger 試用][5]

展開該區段後，隨即顯示每個必要和選擇性參數的輸入欄位。 輸入適當的值，然後按一下 [執行]：

![Swagger 已試用][6]

執行測試之後，您可以驗證回應資料。

## <a name="swagger-response-data"></a>Swagger 回應資料

每個列出的端點也會包含回應主體資料，可用來驗證您的開發和測試。 這些範例包含成功 HTTP 要求所需的狀態碼和 JSON。

![Swagger 回應][7]

範例也會包含錯誤碼，可用來協助偵錯或改善失敗的測試。

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 授權

若要針對 OAuth 2.0 所保護的 API 資源以互動方式來測試要求，請參閱[官方文件](https://swagger.io/docs/specification/authentication/oauth2/)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Digital Twins 物件模型和空間智慧圖表，請參閱[這篇文章](./concepts-objectmodel-spatialgraph.md)。

若要了解如何使用管理 API 進行驗證，請閱讀[使用 API 進行驗證](./security-authenticating-apis.md)。

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
