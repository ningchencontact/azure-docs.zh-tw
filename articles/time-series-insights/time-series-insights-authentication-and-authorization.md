---
title: 如何在 Azure 時間序列深入解析中以 API 驗證和授權 | Microsoft Docs
description: 本文說明如何為呼叫 Azure Time Series Insights API 的自訂應用程式設定驗證和授權。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 5fb2802bfe9cc0a4d3297e6fa749e5b94008c616
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472675"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API 的驗證和授權

本文說明如何設定在呼叫 Azure Time Series Insights API 之自訂應用程式中使用的驗證和授權。

> [!TIP]
> 了解[資料存取權授與](./time-series-insights-data-access.md)您 Azure Active Directory 中的時間序列深入解析環境。

## <a name="service-principal"></a>服務主體

這和下列各節說明如何設定應用程式代表應用程式存取 Time Series Insights API。 應用程式查詢或應用程式認證，而不是使用者認證的 Time Series Insights 環境中發佈參考資料。

## <a name="best-practices"></a>最佳做法

當您有必須存取 Time Series Insights 的應用程式：

1. 設定 Azure Active Directory 應用程式。
1. [將資料存取原則指派](./time-series-insights-data-access.md)Time Series Insights 環境中。

使用應用程式，而不是您的使用者認證適合自：

* 您可以將權限指派給不同於您自己的權限的應用程式身分識別。 一般而言，這些權限只會限制為 App 所需的權限。 例如，您可允許應用程式僅讀取特定 Time Series Insights 環境中的資料。
* 如果您的職責變更，您不需要變更應用程式的認證。
* 您可以在執行自動指令碼時，使用憑證或應用程式金鑰自動進行驗證。

下列各節示範如何透過 Azure 入口網站執行這些步驟。 本文著重在單一租用戶應用程式，應用程式的目的是只在一個組織中執行。 您通常會在您的組織中執行的特定業務應用程式使用單一租用戶應用程式。

## <a name="set-up-summary"></a>設定摘要

設定流程是由三個步驟所組成：

1. 在 Azure Active Directory 中建立應用程式。
1. 授權此應用程式存取 Time Series Insights 環境。
1. 使用應用程式識別碼和金鑰來取得權杖，以從`https://api.timeseries.azure.com/`。 此權杖可用來呼叫 Time Series Insights API。

## <a name="detailed-setup"></a>詳細的安裝程式

1. 在 Azure 入口網站中，選取 [Azure Active Directory] > [應用程式註冊] > [新應用程式註冊]。

   [![Azure Active Directory 中的新應用程式註冊](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. 指定應用程式的名稱，選取 [Web 應用程式 / API] 作為類型，選取任何有效的 URI 作為 [登入 URL]，然後按一下 [建立]。

   [![Azure Active Directory 中建立應用程式](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. 選取新建立的應用程式，並將其應用程式識別碼複製到您慣用的文字編輯器。

   [![複製應用程式識別碼](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. 選取 [金鑰]，輸入金鑰名稱，選取到期日，然後按一下 [儲存]。

   [![選取應用程式金鑰](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![輸入金鑰名稱和到期日，然後按一下 [儲存]](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. 將金鑰複製到您慣用的文字編輯器。

   [![複製應用程式金鑰](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. 針對 Time Series Insights 環境，選取 [資料存取原則]，然後按一下 [新增]。

   [![將新的資料存取原則新增至 Time Series Insights 環境](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. 在 [**選取使用者**] 對話方塊中，貼上的應用程式名稱 (從**步驟 2**) 或應用程式識別碼 (從**步驟 3**)。

   [![在 [選取使用者] 對話方塊中尋找應用程式](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 選取的角色 (**讀者**來查詢資料，**參與者**查詢資料和變更參考資料)，按一下  **確定**。

   [![在 [選取角色] 對話方塊中挑選讀者或參與者](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. 按一下 儲存原則**確定**。

1. 使用應用程式識別碼 (從**步驟 3**) 和應用程式金鑰 (從**步驟 5**) 來代表應用程式取得權杖。 當應用程式呼叫 Time Series Insights API 時，此權杖即可傳入 `Authorization` 標頭。

    如果您使用 C#，您可以使用下列程式碼來代表應用程式取得權杖。 如需完整範例，請參閱[使用 C# 查詢資料](time-series-insights-query-data-csharp.md)。

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

使用**應用程式識別碼**並**金鑰**向 Azure Time Series Insights 的應用程式中。

## <a name="next-steps"></a>後續步驟

- 如需呼叫 Time Series Insights API 的範例程式碼，請參閱[使用 C# 查詢資料](time-series-insights-query-data-csharp.md)。

- 如需 API 參考資訊，請參閱[查詢 API 參考](/rest/api/time-series-insights/ga-query-api)。

- 了解如何[建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。
