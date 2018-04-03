---
title: 使用 OAuth 2.0 搭配 Azure Active Directory 與 API 管理來保護 API | Microsoft Docs
description: 了解如何使用 Azure Active Directory 與 API 管理保護 Web API 後端。
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: 3caa3d2b8640c83f1001aeac3b0a5e9ada143183
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-protect-an-api-using-oauth-20-with-azure-active-directory-and-api-management"></a>如何使用 OAuth 2.0 搭配 Azure Active Directory 與 API 管理來保護 API

本指南說明如何設定 API 管理 (APIM) 執行個體，以使用 OAuth 2.0 通訊協定與 Azure Active Directory (AAD) 來保護 API。 

## <a name="prerequisite"></a>必要條件
若要依照本文中的步驟進行，您必須有：
* APIM 執行個體
* 使用 APIM 執行個體發行的 API
* Azure AD 租用戶

## <a name="overview"></a>概觀

此指南將說明如何在 APIM 中使用 OAuth 2.0 保護 API。 在本文中，會使用 Azure AD 作為授權伺服器 (OAuth 伺服器)。 以下為步驟概述：

1. 在 Azure AD 中註冊應用程式 (後端應用程式) 來代表 API
2. 在 Azure AD 中註冊另一個應用程式 (用戶端應用程式) 來代表需要呼叫 API 的用戶端應用程式
3. 在 Azure AD 中授與權限，允許用戶端應用程式呼叫後端應用程式
4. 設定讓開發人員主控台使用 OAuth 2.0 使用者授權
5. 新增驗證 JWT 的原則，以驗證每個連入要求的 OAuth 權杖

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>在 Azure AD 中註冊應用程式來代表 API

若要使用 Azure AD 保護 API，第一個步驟是在 Azure AD 中註冊一個應用程式來代表 API。 

瀏覽至您的 Azure AD 租用戶，然後瀏覽至 [應用程式註冊]。

選取 [新增應用程式註冊]。 

提供應用程式的名稱。 此範例中使用 `backend-app`。  

選擇 [Web 應用程式/API] 作為 [應用程式類型]。 

在 [登入 URL] 中，您可以使用 `https://localhost` 作為預留位置。

按一下 [建立]。

應用程式建立後，記下**應用程式識別碼**供後續步驟使用。 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>在 Azure AD 中註冊另一個應用程式來代表用戶端應用程式

每個需要呼叫 API 的用戶端應用程式，也必須在 Azure AD 中註冊為應用程式。 在本指南中，我們會使用 APIM 開發人員入口網站中的「開發人員主控台」作為用戶端應用程式範例。 

我們必須在 Azure AD 中註冊另一個應用程式，來代表開發人員主控台。

再次按一下 [新增應用程式註冊]。 

提供應用程式的名稱，選擇 [Web 應用程式/API] 作為 [應用程式類型]。 此範例中使用 `client-app`。  

在 [登入 URL] 中，您可以使用 `https://localhost` 作為預留位置，或使用 APIM 執行個體的登入 URL。 此範例中使用 `https://contoso5.portal.azure-api.net/signin`。

按一下 [建立]。

應用程式建立後，記下**應用程式識別碼**供後續步驟使用。 

現在我們需要建立此應用程式的用戶端密碼，以供後續步驟使用。

再次按一下 [設定]，移至 [金鑰]。

在 [密碼] 下提供 [金鑰描述]，選擇金鑰的到期日，然後按一下 [儲存]。

記下金鑰的值。 

## <a name="grant-permissions-in-aad"></a>在 AAD 中授與權限

現在我們已經註冊兩個應用程式來分別代表 API (也就是後端應用程式) 和開發人員主控台 (也就是用戶端應用程式)，我們需要授與權限，允許用戶端應用程式呼叫後端應用程式。  

再次瀏覽至 [應用程式註冊]。 

按一下 `client-app` 並移至 [設定]。

按一下 [所需的權限]，然後按一下[新增]。

按一下 [選取 API]，搜尋 `backend-app`。

核取 [委派的權限] 下的 `Access backend-app`。 

按一下 [選取]，然後按一下 [完成]。 

> [!NOTE]
> 如果 [Windows Azure Active Directory] 並未列在 [其他應用程式的權限] 之下，請按一下 [新增] 從清單將其新增。
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>在開發人員主控台中啟用 OAuth 2.0 使用者授權

此時，我們已在 Azure AD 中建立我們的應用程式，並已授與適當的權限，讓用戶端應用程式可以呼叫後端應用程式。 

在本指南中，我們會使用「開發人員主控台」作為用戶端應用程式。 以下步驟說明如何在開發人員主控台中啟用 OAuth 2.0 使用者授權 

瀏覽至您的 APIM 執行個體。

按一下 [OAuth 2.0]，然後按一下 [新增]。

提供[顯示名稱] 和 [描述]。

[用戶端註冊頁面 URL] 請輸入預留位置值，例如 `http://localhost`。  [用戶端註冊頁面 URL] 指向使用者可用來建立和設定其對 OAuth 2.0 提供者之專屬帳戶的頁面，而提供者支援使用者帳戶管理。 在此範例中，使用者沒有建立和設定自己的帳戶，因此使用預留位置。

盒取 [授權碼] 作為 [授權授與類型]。

接著，指定 [授權端點 URL] 和 [權杖端點 URL]。

這些值可以從 Azure AD 租用戶中的 [端點] 頁面擷取。 若要存取端點，請再次瀏覽至 [應用程式註冊] 頁面，然後按一下 [端點]。

複製 [OAuth 2.0 授權端點] 並貼到 [授權端點 URL] 文字方塊中。

複製 [OAuth 2.0 權杖端點] 並貼到 [權杖端點 URL] 文字方塊中。

除了貼上權杖端點外，請新增另一個名為 **resource** 的主體參數，值則使用後端應用程式的**應用程式識別碼**。

接著，指定用戶端認證。 這些是用戶端應用程式的認證。

[用戶端識別碼] 請使用用戶端應用程式的**應用程式識別碼**。

[用戶端密碼] 請使用您稍早為用戶端應用程式建立的金鑰。 

緊接在用戶端密碼後面的是授權碼授與類型的 [redirect_url]。

記下此 URL。

按一下 [建立]。

瀏覽回到用戶端應用程式的 [設定] 頁面。

按一下 [回覆 URL]，並在第一行貼上 **redirect_url**。 在此範例中，我們將第一行中的 `https://localhost` 取代為此 URL。  

現在我們已經設定 OAuth 2.0 授權伺服器，開發人員主控台應該能夠從 Azure AD 取得存取權杖。 

下一個步驟是要讓我們的 API 能夠使用 OAuth 2.0 使用者授權，讓開發人員主控台知道它需要先代表使用者取得存取權杖，然後才能呼叫我們的 API。

瀏覽至您的 APIM 執行個體，移至 [API]。

按一下您要保護的 API。 在此範例中，我們會使用 `Echo API`。

移至 [設定] 。

在 [安全性] 下，選擇 [OAuth 2.0]，選取我們先前設定的 OAuth 2.0 伺服器。 

按一下 [ **儲存**]。

## <a name="successfully-call-the-api-from-the-developer-portal"></a>從開發人員入口網站成功呼叫 API

現在 `Echo API` 已啟用 OAuth 2.0 使用者授權，開發人員主控台會先代表使用者取得存取權杖，然後才呼叫 API。

在開發人員入口網站中，瀏覽至 `Echo API` 底下的任何作業，按一下 [試試看]，這會將我們帶到開發人員主控台。

請注意，在 [授權] 區段中有一個新項目對應到您剛才新增的授權伺服器。

從授權下拉式清單中選取 [授權碼]，系統會提示您登入 Azure AD 租用戶。 如果您已經使用帳戶登入，就不會提示您。

成功登入之後，就會在要求中新增 `Authorization` 標頭，以及來自 Azure AD 的存取權杖。 

權杖範例看起來像下面這樣，所採用的是 Base64 編碼。

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
```

按一下 [傳送]，您應該能夠成功呼叫 API。


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>設定 JWT 驗證原則來預先授權要求

此時，當使用者嘗試從開發人員主控台進行呼叫，系統會提示使用者登入，而開發人員主控台會代表使用者取得存取權杖。 一切如預期般運作。 不過，如果有人沒用權杖或用無效的權杖呼叫 API 會怎麼樣？ 例如，您可以嘗試刪除 `Authorization` 標頭，並發現您仍然可以呼叫 API。 這是因為 APIM 不會在此時驗證存取權杖。 它會將 `Auhtorization` 標頭傳遞至後端 API。

我們可以使用[驗證 JWT](api-management-access-restriction-policies.md#ValidateJWT) 原則，藉由驗證每個傳入要求的存取權杖來預先授權 APIM 中的要求。 如果要求沒有有效權杖，API 管理就會將其封鎖，而不會將其傳入後端。 我們可以將下列原則新增至 `Echo API`。 

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="next-steps"></a>後續步驟
* 查看更多有關 API 管理的 [視訊](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 。
* 如需其他保護後端服務的方式，請參閱[相互憑證驗證](api-management-howto-mutual-certificates.md)。

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
