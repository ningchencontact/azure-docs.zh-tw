---
title: 驗證和授權使用 Azure Time Series Insights 中的 API |Microsoft Docs
description: 本文說明如何為呼叫 Azure Time Series Insights API 的自訂應用程式設定驗證和授權。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/29/2019
ms.custom: seodec18
ms.openlocfilehash: 899bcffaf3a54bd541d488f99c35ec6721d751ca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543925"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API 的驗證和授權

本文件說明如何使用新的 Azure Active Directory 刀鋒視窗的 Azure Active Directory 中註冊應用程式。 在 Azure Active Directory 中註冊的應用程式啟用使用者驗證和授權使用 Time Series Insights 環境相關聯 Azure Time Series Insight API。

## <a name="service-principal"></a>服務主體

下列各節說明如何設定應用程式代表應用程式存取 Time Series Insights API。 應用程式可能會接著查詢，或在 Time Series Insights 環境中使用它自己的應用程式認證，透過 Azure Active Directory 中發佈參考資料。

## <a name="summary-and-best-practices"></a>摘要及最佳做法

Azure Active Directory 應用程式註冊流程牽涉到三個主要步驟。

1. [註冊應用程式](#azure-active-directory-app-registration)Azure Active Directory 中。
1. 授權應用程式能夠[Time Series Insights 環境的資料存取](#granting-data-access)。
1. 使用**應用程式識別碼**並**用戶端祕密**來取得權杖，從`https://api.timeseries.azure.com/`中您[用戶端應用程式](#client-app-initialization)。 此權杖可用來呼叫 Time Series Insights API。

每個**步驟 3**，用來分隔您的應用程式和您的使用者認證可讓您：

* 不同於您自己的權限的應用程式身分識別指派權限。 一般而言，這些權限只會限制為 App 所需的權限。 例如，您可以允許應用程式僅讀取特定 Time Series Insights 環境的資料。
* 藉由隔離應用程式的安全性，從 建立使用者的驗證認證**用戶端祕密**或安全性憑證。 如此一來，應用程式的認證不相依於特定使用者的認證。 如果使用者的角色變更時，應用程式不一定需要新的認證或進一步的組態。 如果使用者變更其密碼時，所有存取應用程式不需要新的認證或金鑰。
* 使用自動安裝指令碼執行**用戶端祕密**或安全性憑證，而不是特定使用者的認證 （需要它們會出現）。
* 使用安全性憑證，而不是密碼來保護您的 Azure Time Series Insights API 的存取。

> [!IMPORTANT]
> 遵循的原則**關注點分離**（說明上述在此案例） 時設定您的 Azure Time Series Insights 安全性原則。

> [!NOTE]
> * 本文著重在單一租用戶應用程式，應用程式的目的是只在一個組織中執行。
> * 您通常會在您的組織中執行的特定業務應用程式使用單一租用戶應用程式。

## <a name="detailed-setup"></a>詳細的安裝程式

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory 應用程式註冊

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>資料存取權授與

1. 針對時間序列深入解析環境中，選取**資料存取原則**，然後選取**新增**。

   [![將新的資料存取原則新增至 Time Series Insights 環境](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. 在**選取使用者**對話方塊方塊中，貼上其中一個**應用程式名稱**或**應用程式識別碼**從 Azure Active Directory 應用程式註冊一節。

   [![在 [選取使用者] 對話方塊中尋找應用程式](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 選取的角色。 選取 **讀者**來查詢資料或**參與者**查詢資料，並變更參考資料。 選取 [確定]  。

   [![在 [選取使用者角色] 對話方塊中挑選讀者或參與者](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. 儲存選取的原則**確定**。

   > [!TIP]
   > 了解[資料存取權授與](./time-series-insights-data-access.md)您 Azure Active Directory 中的時間序列深入解析環境。

### <a name="client-app-initialization"></a>用戶端應用程式初始化

1. 使用**應用程式識別碼**並**用戶端祕密**（應用程式金鑰） 從 Azure Active Directory 應用程式註冊一節，以代表應用程式取得權杖。

    在C#，下列程式碼可以取得代表應用程式的權杖。 如需完整範例，請參閱[使用 C# 查詢資料](time-series-insights-query-data-csharp.md)。

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

1. 當應用程式呼叫 Time Series Insights API 時，此權杖即可傳入 `Authorization` 標頭。

## <a name="next-steps"></a>後續步驟

- 呼叫 GA Time Series Insights API 的範例程式碼，請參閱[查詢的資料使用C# ](./time-series-insights-query-data-csharp.md)。

- 如需預覽 Time Series Insights API 的程式碼範例，請參閱[使用 查詢預覽的資料C# ](./time-series-insights-update-query-data-csharp.md)。

- 如需 API 參考資訊，請參閱[查詢 API 參考](/rest/api/time-series-insights/ga-query-api)。

- 了解如何[建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。