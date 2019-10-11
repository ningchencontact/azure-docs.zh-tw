---
title: 設定 Azure Active Directory 驗證 - Azure App Service
description: 瞭解如何為您的 App Service 應用程式設定 Azure Active Directory 驗證。
author: cephalin
services: app-service
documentationcenter: ''
manager: gwallace
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service
ms.workload: web,mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: cephalin
ms.custom: fasttrack-edit
ms.openlocfilehash: ac73b549546c353dce4c40005b7742577e03d26c
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176988"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>設定 App Service 應用程式以使用 Azure AD 登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文說明如何將 Azure App Service 設定為使用 Azure Active Directory （Azure AD）做為驗證提供者。

> [!NOTE]
> 目前只有 Azure AD v1.0 才支援 Azure App Service 和 Azure Functions。 [Microsoft 身分識別平臺](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-overview)v2.0 不支援這些版本，其中包含 microsoft 驗證程式庫（MSAL）。

在設定您的應用程式和驗證時，請遵循下列最佳作法：

- 為每個 App Service 應用程式提供自己的許可權和同意。
- 使用自己的註冊來設定每個 App Service 應用程式。
- 在不同的部署位置使用個別的應用程式註冊，以避免在環境之間共用許可權。 測試新的程式碼時，這種作法有助於防止問題影響生產應用程式。

## <a name="express"> </a>使用快速設定來設定

1. 在[Azure 入口網站]中，移至您的 App Service 應用程式。
1. 在左窗格中選取 [**設定**] [ > ] [**驗證/授權**]，並確定**App Service 驗證**已**開啟**。
1. 選取 [Azure Active Directory]，然後選取 [管理模式] 下方的 [快速]。
1. 選取 [確定] 以在 Azure Active Directory 中註冊 App Service 應用程式。 隨即會建立新的應用程式註冊。

   如果您想要改為選擇現有的應用程式註冊：

   1. 選擇 [**選取現有的應用程式**]，然後在您的租使用者內搜尋先前建立之應用程式註冊的名稱。
   1. 選取應用程式註冊，然後選取 **[確定]** 。
   1. 然後在 [Azure Active Directory 設定] 頁面上選取 **[確定]** 。

   根據預設，App Service 會提供驗證，但不會限制對您網站內容和 Api 的已授權存取。 您必須在應用程式程式碼中授權使用者。
1. 選擇性若要將應用程式存取限制為僅由 Azure Active Directory 驗證的使用者，請將 [**要求未經驗證時所採取的動作**] 設定為 [**使用 Azure Active Directory 登入**]。 當您設定此功能時，您的應用程式會要求所有要求都必須經過驗證。 它也會將所有未驗證的重新導向至 Azure Active Directory 進行驗證。

    > [!CAUTION]
    > 以這種方式限制存取適用于對您應用程式的所有呼叫，這對於具有公開可用首頁的應用程式（如許多單頁應用程式），可能不是理想的做法。 對於這類應用程式，可能會慣用 [**允許匿名要求（無動作）** ]，而應用程式會以手動方式啟動登入本身。 如需詳細資訊，請參閱[驗證流程](overview-authentication-authorization.md#authentication-flow)。
1. 選取 [儲存]。

## <a name="advanced"> </a>使用進階設定來設定

如果您想要使用不同于您用來登入 Azure 的 Azure AD 租使用者，您可以手動設定應用程式設定。 若要完成此自訂設定，您必須：

1. 在 Azure AD 中建立註冊。
1. 提供 App Service 的一些註冊詳細資料。

### <a name="register"></a>在 Azure AD 中，為您的 App Service 應用程式建立應用程式註冊

當您設定 App Service 應用程式時，您將需要下列資訊：

- 用戶端識別碼
- 租用戶識別碼
- 用戶端密碼（選擇性）
- 應用程式識別碼 URI

執行下列步驟：

1. 登入[Azure 入口網站]並移至您的 App Service 應用程式。 請記下您應用程式的**URL**。 您將使用它來設定您的 Azure Active Directory 應用程式註冊。
1. 選取 [Azure Active Directory] > [應用程式註冊] > [新註冊]。
1. 在 [**註冊應用程式**] 頁面中，輸入應用程式註冊的**名稱**。
1. 在 [重新**導向 URI**] 中，選取 [ **Web** ]，然後輸入 APP SERVICE 應用程式的 URL，並將路徑附加 `/.auth/login/aad/callback`。 例如，`https://contoso.azurewebsites.net/.auth/login/aad/callback`。 
1. 選取 [建立]。
1. 建立應用程式註冊之後，請複製**應用程式（用戶端）識別碼**和**目錄（租使用者）識別碼**，以供稍後查看。
1. 選取 [**商標**]。 在 [**首頁 url**] 中，輸入 App Service 應用程式的 URL，然後選取 [**儲存**]。
1. 選取 [**公開 API** > **組**]。 貼上 App Service 應用程式的 URL，然後選取 [**儲存**]。

   > [!NOTE]
   > 此值是應用**程式註冊的應用程式識別碼 URI** 。 如果您的 web 應用程式需要存取雲端中的 API，當您設定雲端 App Service 資源時，您需要 web 應用**程式的應用程式識別碼 URI** 。 例如，如果您想要讓雲端服務明確授與 web 應用程式的存取權，您可以使用此範例。

1. 選取 [新增範圍]。
   1. 在 [**範圍名稱**] 中，輸入*user_impersonation*。
   1. 在文字方塊中，輸入您希望使用者在同意頁面上看到的同意範圍名稱和描述。 例如，輸入*存取我的應用程式*。 
   1. 選取 [**新增領域**]。
1. 選擇性若要建立用戶端密碼，請選取 **憑證 & 秘密** > **新的用戶端密碼** @no__t **-3**新增。 複製頁面中顯示的 [用戶端密碼] 值。 它將不會再次顯示。
1. 選擇性若要新增多個**回復 url**，請選取 [**驗證**]。

### <a name="secrets"> </a>將 Azure Active Directory 資訊新增至 App Service 應用程式

1. 在[Azure 入口網站]中，移至您的 App Service 應用程式。 
1. 在左窗格中選取 [**設定] > [驗證/授權**]，並確定**App Service 驗證**已**開啟**。
1. 選擇性根據預設，App Service authentication 會允許未經驗證的存取您的應用程式。 若要強制執行使用者驗證，請將 [**要求未經驗證時所採取的動作**] 設定為 [**使用 Azure Active Directory 登入**]。
1. 在 [驗證提供者] 底下，選取 [ **Azure Active Directory**]。
1. 在 [**管理模式]** 中，選取 [ **Advanced** ]，並根據下表設定 App Service 驗證：

    |欄位|描述|
    |-|-|
    |用戶端識別碼| 使用應用**程式註冊的應用程式（用戶端）識別碼**。 |
    |簽發者識別碼| 使用 `https://login.microsoftonline.com/<tenant-id>`，並以應用程式註冊的**目錄（租使用者）識別碼**取代 *\<tenant-id >* 。 |
    |用戶端密碼（選擇性）| 使用您在應用程式註冊中產生的用戶端密碼。|
    |允許的權杖物件| 如果這是雲端或伺服器應用程式，而且您想要允許來自 web 應用程式的驗證權杖，請在這裡新增 web 應用程式的**應用程式識別碼 URI** 。 |

    > [!NOTE]
    > 設定的**用戶端識別碼** *一律*會隱含地視為允許的物件，不論您如何設定允許的**權杖使用者**。
1. 選取 [確定]，然後選取 [儲存]。

您現在已經準備好在 App Service 應用程式中使用 Azure Active Directory 進行驗證。

## <a name="configure-a-native-client-application"></a>設定原生用戶端應用程式

您可以註冊原生用戶端，以允許使用用戶端程式庫（例如**Active Directory 驗證程式庫**）進行驗證。

1. 在 [Azure 入口網站]中，選取 [ **Active Directory** > ]**應用程式註冊** >  個 [**新增註冊**]。
1. 在 [**註冊應用程式**] 頁面中，輸入應用程式註冊的**名稱**。
1. 在 [重新**導向 URI**] 中，選取 [**公用用戶端（行動 & 桌面）** ]，然後輸入您 APP SERVICE 應用程式的 URL，並將路徑附加 `/.auth/login/aad/callback`。 例如，`https://contoso.azurewebsites.net/.auth/login/aad/callback`。
1. 選取 [建立]。

    > [!NOTE]
    > 若為 Windows 應用程式，請改用[封裝 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)作為 URI。
1. 建立應用程式註冊之後，複製 [**應用程式（用戶端）識別碼**] 的值。
1. 選取**API 許可權** > **新增許可權** > **我的 api**。
1. 選取您稍早為 App Service 應用程式建立的應用程式註冊。 如果您沒有看到應用程式註冊，請確定您已在[App Service 應用程式的 Azure AD 中的 [建立應用程式註冊](#register)] 中新增**user_impersonation**範圍。
1. 選取 [ **user_impersonation**]，然後選取 [**新增許可權**]。

您現在已設定了可以存取您 App Service 應用程式的原生用戶端應用程式。

## <a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure 入口網站]: https://portal.azure.com/
[alternative method]:#advanced
