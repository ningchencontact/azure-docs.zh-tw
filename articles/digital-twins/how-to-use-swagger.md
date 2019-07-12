---
title: 了解如何使用 Azure Digital Twins 參考 Swagger | Microsoft Docs
description: 了解如何使用 Azure Digital Twins Swagger 參考文件。
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/29/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 9d4fd798309398dff38081cc66b4071c5dd1f5bf
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670777"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Azure Digital Twins Swagger 參考文件

每個已佈建的 Azure Digital Twins 執行個體都包含本身自動產生的 Swagger 參考文件。

[Swagger](https://swagger.io/) \(英文\) (或 [OpenAPI](https://www.openapis.org/) \(英文\)) 會將複雜的 API 資訊結合至可互動且與語言無關的參考資源。 若要對 API 執行作業，Swagger 會針對要使用的 JSON 承載、HTTP 方法和特定端點提供重要的參考資料。

## <a name="swagger-summary"></a>Swagger 摘要

Swagger 會提供可互動的 API 摘要，包括：

* API 和物件模型資訊。
* REST API 端點，會指定必要的要求承載、標頭、參數、內容路徑和 HTTP 方法。
* API 功能測試。
* 用於驗證和確認 HTTP 回應的範例回應資訊。
* 錯誤碼資訊。

Swagger 是一個很便利的工具，可協助您開發和測試對 Azure Digital Twins 管理 API 所發出的呼叫。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>參考資料

自動產生的 Swagger 參考資料提供重要概念的快速概觀、可用的管理 API 端點，以及每個物件模型的說明，以協助執行開發和測試工作。

透過簡單摘要來說明 API。

[![Swagger 頂端](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

此外，也會列出管理 API 物件模型。

[![Swagger 模型](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

您可以選取每個列出的物件模型，以取得更詳細的主要屬性摘要。

[![Swagger 模型](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

使用產生的 Swagger 物件模型來查看所有可用的 Azure Digital Twins [物件和 API](./concepts-objectmodel-spatialgraph.md) 相當方便。 開發人員可在於 Azure Digital Twins 上建置解決方案時使用此資源。

## <a name="endpoint-summary"></a>端點摘要

Swagger 也提供管理 API 中所有端點的整體概觀。

每個列出的端點也會包含必要的要求資訊，例如：

* 必要參數。
* 必要參數資料類型。
* 要存取資源的 HTTP 方法。

[![Swagger 端點](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

若要查看更詳細的概觀，請選取每個資源。

## <a name="use-swagger-to-test-endpoints"></a>使用 Swagger 來測試端點

Swagger 所提供的強大功能之一是直接透過文件 UI 來測試 API 端點。

在您選取特定端點之後，就會看到**歡迎試用**。

[![Swagger try](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

展開該區段後，即會顯示每個必要和選擇性參數的輸入欄位。 輸入正確的值，然後選取 [執行]  。

[![嘗試的 swagger](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

當您執行測試之後，就可以驗證回應資料。

## <a name="swagger-response-data"></a>Swagger 回應資料

每個列出的端點也會包含回應主體資料，可用來驗證您的開發和測試。 這些範例包含您想要看到適用於成功 HTTP 要求的狀態碼和 JSON。

[![Swagger 的回應](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

範例也會包含錯誤碼，可用來協助偵錯或改善失敗的測試。

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 授權

> [!NOTE]
> * 建立 Azure 的數位對應項資源的使用者主體會有空間系統管理員角色指派，並將能夠建立其他使用者的其他角色指派。 這類使用者以及其角色可被授權可呼叫 Api。

1. 請依照下列中的步驟[本快速入門](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)或[註冊 Azure 的數位對應項應用程式與 Azure Active Directory 的舊版](./how-to-use-legacy-aad.md)來建立及設定 Azure AD 應用程式。 或者，您可以重複使用現有的應用程式註冊。

1. 應用程式註冊中加入下列回覆 url:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | 名稱  | 更換為 | 範例 |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | 您在入口網站中找到的管理 REST API 文件 URL  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. 複製您的 Azure AD 應用程式識別碼。

之後完成的 Azure Active Directory 註冊：

1. 選取 **授權**swagger 頁面上的按鈕。

    [![選取 Swagger 授權按鈕](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. 貼上到的應用程式識別碼**client_id**欄位。

    [![Swagger client_id 欄位](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. 您接著將導向至下列的強制回應成功。

    [![Swagger 重新導向的強制回應](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

若要深入了解如何以互動方式測試由 OAuth 2.0 所保護的要求，請參閱[官方文件](https://swagger.io/docs/specification/authentication/oauth2/)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure Digital Twins 物件模型和空間智慧圖表，請參閱[了解 Azure Digital Twins 物件模型](./concepts-objectmodel-spatialgraph.md)。

- 若要了解如何使用管理 API 進行驗證，請參閱[使用 API 進行驗證](./security-authenticating-apis.md)。