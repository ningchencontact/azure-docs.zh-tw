---
title: 在 Azure 時間序列深入解析中使用 API 來進行驗證和授權 |Microsoft Docs
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
ms.openlocfilehash: bdf0fbfb2b40e932f9e3627c3bc0356eb0afb472
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677935"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API 的驗證和授權

本檔說明如何使用新的 Azure Active Directory 分頁, 在 Azure Active Directory 中註冊應用程式。 在 Azure Active Directory 中註冊的應用程式可讓使用者對與時間序列深入解析環境相關聯的 Azure 時間序列深入解析 API 進行驗證及授權使用。

## <a name="service-principal"></a>服務主體

下列各節說明如何設定應用程式, 以代表應用程式存取時間序列深入解析 API。 然後, 應用程式可以透過 Azure Active Directory 使用自己的應用程式認證, 在時間序列深入解析環境中查詢或發佈參考資料。

## <a name="summary-and-best-practices"></a>摘要和最佳作法

Azure Active Directory 應用程式註冊流程牽涉到三個主要步驟。

1. 在 Azure Active Directory 中[註冊應用程式](#azure-active-directory-app-registration)。
1. 授權應用程式擁有[時間序列深入解析環境的資料存取權](#granting-data-access)。
1. 使用**應用程式識別碼**和**用戶端密碼**, 從`https://api.timeseries.azure.com/`您的[用戶端應用程式](#client-app-initialization)取得權杖。 此權杖可用來呼叫 Time Series Insights API。

根據**步驟 3**, 將您的應用程式和使用者認證分開, 可讓您:

* 將許可權指派給與您自己的許可權不同的應用程式身分識別。 一般而言，這些權限只會限制為 App 所需的權限。 例如, 您可以允許應用程式只從特定的時間序列深入解析環境中讀取資料。
* 使用**用戶端密碼**或安全性憑證, 將應用程式的安全性與建立使用者的驗證認證隔離。 因此, 應用程式的認證不會相依于特定使用者的認證。 如果使用者的角色變更, 應用程式不一定需要新的認證或進一步的設定。 如果使用者變更其密碼, 對應用程式的所有存取都不需要新的認證或金鑰。
* 使用**用戶端密碼**或安全性憑證來執行自動腳本, 而不是特定使用者的認證 (需要有這些認證)。
* 使用安全性憑證而非密碼來保護 Azure 時間序列深入解析 API 的存取。

> [!IMPORTANT]
> 設定 Azure 時間序列深入解析安全性原則時, 請遵循**關注點分離的**原則 (針對上述案例說明)。

> [!NOTE]
> * 本文著重于單一租使用者應用程式, 其中應用程式只會在一個組織中執行。
> * 您通常會將單一租使用者應用程式用於組織內執行的企業營運系統應用程式。

## <a name="detailed-setup"></a>詳細設定

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory 應用程式註冊

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>授與資料存取權

1. 針對時間序列深入解析環境, 選取 [**資料存取原則**], 然後選取 [**新增**]。

   [![將新的資料存取原則新增至時間序列深入解析環境](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. 在 [**選取使用者**] 對話方塊中, 貼上 [Azure Active Directory 應用程式註冊] 區段中的 [**應用程式名稱**] 或 [**應用程式識別碼**]。

   [![在 [選取使用者] 對話方塊中尋找應用程式](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 選取角色。 選取 [**讀取器**] 來查詢資料或**參與者**, 以查詢資料並變更參考資料。 選取 [確定]。

   [![選取 [選取使用者角色] 對話方塊中的 [讀取器] 或 [參與者]](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. 選取 **[確定]** 以儲存原則。

   > [!TIP]
   > 瞭解如何將[資料存取權](./time-series-insights-data-access.md)授與 Azure Active Directory 中的時間序列深入解析環境。

### <a name="client-app-initialization"></a>用戶端應用程式初始化

1. 使用 [Azure Active Directory 應用程式註冊] 區段中的 [**應用程式識別碼**] 和 [**用戶端密碼**] (應用程式金鑰), 代表應用程式取得權杖。

    在C#中, 下列程式碼可以代表應用程式取得權杖。 如需完整範例，請參閱[使用 C# 查詢資料](time-series-insights-query-data-csharp.md)。

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

- 如需呼叫 GA 時間序列深入解析 API 的範例程式碼, 請參閱[使用C#查詢資料](./time-series-insights-query-data-csharp.md)。

- 如需預覽時間序列深入解析 API 程式碼範例, 請參閱[使用C#查詢預覽資料](./time-series-insights-update-query-data-csharp.md)。

- 如需 API 參考資訊，請參閱[查詢 API 參考](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)。

- 瞭解如何[建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。