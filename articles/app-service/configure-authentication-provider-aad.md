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
ms.openlocfilehash: 8de464a00867dd397f28de1dc35cf264244f6905
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743264"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>設定 App Service 應用程式使用 Azure Active Directory 登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

> [!NOTE]
> 目前，Azure App Service 和 Azure Functions 不支援 AAD V2 （包括 MSAL）。
>

本文說明如何將 Azure App Service 設定為使用 Azure Active Directory 做為驗證提供者。

建議您使用自己的註冊來設定每個 App Service 應用程式，因此它有自己的許可權和同意。 此外，請考慮針對個別的部署位置使用個別的應用程式註冊。 這可避免在環境之間共用許可權，因此，您所測試的新程式碼中的問題不會影響生產。

## <a name="express"> </a>使用快速設定來設定

1. 在 [Azure 入口網站]中，瀏覽至您的 App Service 應用程式。 在左側瀏覽區域中，選取 [驗證/授權]。
2. 如果 [驗證/授權] 並未啟用，請選取 [開啟]。
3. 選取 [Azure Active Directory]，然後選取 [管理模式] 下方的 [快速]。
4. 選取 [確定] 以在 Azure Active Directory 中註冊 App Service 應用程式。 這樣會建立新的應用程式註冊。 如果您想改為選擇現有的應用程式註冊，請按一下 [選取現有的 App]，然後在您的租用戶內搜尋先前建立之應用程式註冊的名稱。 按一下應用程式註冊以選取它，然後按一下 [確定]。 然後在 Azure Active Directory 設定頁面上按一下 [確定]。
App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。 您必須在應用程式程式碼中授權使用者。
5. 選擇性若要將應用程式的存取限制為只有 Azure Active Directory 驗證的使用者，請將 [**要求未經驗證時所採取的動作**] 設定為 [**使用 Azure Active Directory 登入**]。 這會要求所有要求都需經過驗證，且所有未經驗證的要求都會重新導向至 Azure Active Directory 以進行驗證。

    > [!NOTE]
    > 以這種方式限制存取適用于應用程式的所有呼叫，這對於想要公開使用首頁的應用程式（如許多單頁應用程式），可能不是理想的做法。 對於這類應用程式，可能會慣用 [**允許匿名要求（無動作）** ]，而應用程式會以手動方式啟動登入本身，如[這裡](overview-authentication-authorization.md#authentication-flow)所述。
6. 按一下 [儲存]。

## <a name="advanced"> </a>使用進階設定來設定

如果您想要使用的 Azure Active Directory 租使用者不同于您用來登入 Azure 的租使用者，您也可以手動提供設定。 若要完成組態，您必須先在 Azure Active Directory 中建立註冊，接著必須提供一些註冊詳細資料給 App Service。

### <a name="register"></a>在 Azure AD 中，為您的 App Service 應用程式建立應用程式註冊

手動建立應用程式註冊時，請注意您稍後在設定 App Service 應用程式時將需要的三項資訊：用戶端識別碼、租使用者識別碼，以及選擇性的用戶端密碼和應用程式識別碼 URI。

1. 在  [Azure 入口網站]中，流覽至您的 App Service 應用程式，並記下您應用程式的**URL**。 您將使用它來設定您的 Azure Active Directory 應用程式註冊。
1. 在[Azure 入口網站]中，從左側功能表中選取 [ **Active Directory**  > ]**應用程式註冊** > [**新增註冊**]。 
1. 在 [**註冊應用程式**] 頁面中，輸入應用程式註冊的**名稱**。
1. 在 [重新**導向 URI**] 中，選取 [ **Web** ]，然後輸入 APP SERVICE 應用程式`/.auth/login/aad/callback`的 URL 並附加路徑。 例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 然後選取 [建立]。
1. 一旦建立應用程式註冊，請複製**應用程式（用戶端）識別碼**和**目錄（租使用者）識別碼**，以供稍後查看。
1. 選取 [**商標**]。 在 [**首頁 url**] 中，輸入 App Service 應用程式的 URL，然後選取 [**儲存**]。
1. 選取 [**公開 API**  > **集合**]。 貼上 App Service 應用程式的 URL，然後選取 [**儲存**]。

    > [!NOTE]
    > 此值是應用**程式註冊的應用程式識別碼 URI** 。 例如，如果您想要讓前端 web 應用程式存取後端 API，而您希望後端明確授與前端的存取權，當您設定 [App Service 應用程式資源] 時，您需要*前端*的 [**應用程式識別碼 URI** ]> 後端。
1. 選取 [新增範圍]。 在 [**範圍名稱**] 中，輸入*user_impersonation*。 在文字方塊中，輸入您想要讓使用者在同意頁面上看到的同意範圍名稱和描述，例如 [*存取我的應用程式*]。 完成後，按一下 [**新增領域**]。
1. 選擇性若要建立用戶端密碼，請選取 **憑證 & 秘密** > **新的用戶端密碼** > **新增**。 複製頁面中顯示的 [用戶端密碼] 值。 一旦您離開之後，就不會再顯示。
1. 選擇性若要新增多個**回復 url**，請選取功能表中的 [**驗證**]。

### <a name="secrets"> </a>將 Azure Active Directory 資訊新增至 App Service 應用程式

1. 在 [Azure 入口網站]中，瀏覽至您的 App Service 應用程式。 從左側功能表中，選取 [**驗證/授權**]。 如果 [驗證/授權] 功能未啟用，請選取 [**開啟**]。 
1. 選擇性根據預設，App Service authentication 會允許未經驗證的存取您的應用程式。 若要強制執行使用者驗證，請將 [**要求未經驗證時所採取的動作**] 設定為 [**使用 Azure Active Directory 登入**]。
1. 在 [驗證提供者] 底下，選取 [ **Azure Active Directory**]。
1. 在 [**管理模式]** 中，選取 [ **Advanced** ]，並根據下表設定 App Service 驗證：

    |欄位|描述|
    |-|-|
    |用戶端識別碼| 使用應用**程式註冊的應用程式（用戶端）識別碼**。 |
    |簽發者識別碼| 使用`https://login.microsoftonline.com/<tenant-id>`，並將 [  *\<租使用者識別碼] >* 取代為應用程式註冊的**目錄（租使用者）識別碼**。 |
    |用戶端密碼（選擇性）| 使用您在應用程式註冊中產生的用戶端密碼。|
    |允許的權杖對象| 如果這是*後端*應用程式，而且您想要允許來自前端應用程式的驗證權杖，請在這裡新增*前端*的**應用程式識別碼 URI** 。 |

    > [!NOTE]
    > 設定的**用戶端識別碼** *一律*會隱含地視為允許的物件，不論您如何設定允許的**權杖使用者**。
1. 選取 **[確定]** ，然後選取 [**儲存**]。

現在，您已可在 App Service 應用程式中使用 Azure Active Directory 進行驗證。

## <a name="configure-a-native-client-application"></a>設定原生用戶端應用程式
如果您想要使用用戶端程式庫（例如**Active Directory 驗證程式庫**）來執行登入，您可以註冊原生用戶端。

1. 在[Azure 入口網站]中，從左側功能表中選取 [ **Active Directory**  > ]**應用程式註冊** > [**新增註冊**]。 
1. 在 [**註冊應用程式**] 頁面中，輸入應用程式註冊的**名稱**。
1. 在 [重新**導向 URI**] 中，選取 [**公用用戶端（行動 & 桌面）** ]，然後輸入 APP SERVICE 應用`/.auth/login/aad/callback`程式的 URL 並附加路徑。 例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 然後選取 [建立]。

    > [!NOTE]
    > 若為 Windows 應用程式，請改用[封裝 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)作為 URI。
1. 一旦建立應用程式註冊，請複製 [**應用程式（用戶端）識別碼**] 的值。
1. 從左側功能表中，選取 [ **API 許可權** > ] [**新增許可權** > ] [**我的 api**]。
1. 選取您稍早為 App Service 應用程式建立的應用程式註冊。 如果您沒有看到應用程式註冊，請確認您已在[App Service 應用程式的 Azure AD 中建立應用程式註冊](#register)中新增了**user_impersonation**範圍。
1. 選取 [ **user_impersonation** ]，然後按一下 [**新增許可權**]。

您現在已設定了可以存取您 App Service 應用程式的原生用戶端應用程式。

## <a name="related-content"> </a>相關內容

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
