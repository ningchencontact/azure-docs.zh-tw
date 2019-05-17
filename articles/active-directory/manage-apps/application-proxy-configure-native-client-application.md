---
title: 發佈原生用戶端應用程式 - Azure AD | Microsoft Docs
description: 涵蓋如何讓原生用戶端應用程式與 Azure AD 應用程式 Proxy 連接器通訊，為內部部署的應用程式提供安全的遠端存取。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb36d6a03da07681db468184a489a79f7f0deab7
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825495"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>如何啟用與 proxy 應用程式互動的原生用戶端應用程式

您可以使用 Azure Active Directory (Azure AD) 應用程式 Proxy 來發佈 web 應用程式，但它也可用來發佈原生用戶端應用程式已與 Azure AD Authentication Library (ADAL)。 與 web 應用程式的原生用戶端應用程式不同，因為其安裝在裝置上，而透過瀏覽器存取的 web 應用程式。

若要支援原生用戶端應用程式，應用程式 Proxy 會接受傳送的標頭中的 Azure AD 發出的權杖。 應用程式 Proxy 服務會為使用者驗證。 此解決方案不使用應用程式權杖進行驗證。

![使用者、Azure Active Directory 和已發佈應用程式之間的關係](./media/application-proxy-configure-native-client-application/richclientflow.png)

若要發佈原生應用程式，使用 Azure AD 驗證程式庫，它會負責驗證，並支援許多用戶端環境。 應用程式 Proxy 融入 [原生應用程式到 Web API 案例](../develop/native-app.md)。

本文引導您完成使用應用程式 Proxy 和 Azure AD 驗證程式庫發佈原生應用程式的四個步驟。

## <a name="step-1-publish-your-proxy-application"></a>步驟 1：發佈您的 proxy 應用程式

如同任何其他應用程式一般，發佈您的 Proxy 應用程式，並指派使用者以存取您的應用程式。 如需詳細資訊，請參閱[使用應用程式 Proxy 發佈應用程式](application-proxy-add-on-premises-application.md)。

## <a name="step-2-register-your-native-application"></a>步驟 2：註冊原生應用程式

您現在需要在 Azure AD 中註冊您的應用程式時，也將，如下所示：

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)。 **儀表板**for **Azure Active Directory 系統管理中心**隨即出現。
2. 在提要欄位中，選取**Azure Active Directory**。 **Azure Active Directory**概觀頁面隨即出現。
3. 在 Azure AD 的概觀資訊看板中，選取**應用程式註冊**。 所有應用程式註冊清單隨即出現。
4. 選取 [新增註冊]。 **註冊應用程式**頁面隨即出現。

   ![建立新的應用程式註冊](./media/application-proxy-configure-native-client-application/create.png)
5. 在 **名稱**標題之下，指定您的應用程式的使用者端的顯示名稱。
6. 底下**支援的帳戶類型**標題之下，選取存取層級使用這些指導方針：
   - 若要以您的組織內部的帳戶為目標，請選取**只有此組織目錄中的帳戶**。
   - 若要只商務或教育客戶為目標，請選取**任何組織的目錄中的帳戶**。
   - 若要以最寬的 Microsoft 身分識別集合為目標，請選取**任何組織的目錄和個人 Microsoft 帳戶中的帳戶**。
7. 在 **重新導向 URI**標題之下，選取**公開用戶端 （行動和桌面）**，然後輸入您的應用程式的重新導向 URI。
8. 選取 讀**Microsoft 平台原則**，然後選取**註冊**。 建立新的應用程式登錄 [概觀] 頁面，並顯示。

如需詳細資訊建立新的應用程式註冊，請參閱[整合應用程式與 Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)。

## <a name="step-3-grant-access-to-your-proxy-application"></a>步驟 3：授與存取權，您的 proxy 應用程式

既然您已註冊原生應用程式，您可以讓它能夠存取其他應用程式在您目錄中，在此情況下存取的 proxy 應用程式。 若要啟用原生應用程式 proxy 應用程式公開：

1. 在新的應用程式註冊頁面的 [資訊看板]，選取**API 的權限**。 **API 的權限**頁面會顯示新的應用程式註冊。
2. 選取 [新增權限]。 **要求的 API 權限**頁面隨即出現。
3. 底下**選取 API**設定中，選取**我的組織使用的 Api**。 清單隨即出現，其中包含您目錄中公開 Api 的應用程式。
4. 在搜尋] 方塊或捲軸來尋找您在發佈的 proxy 應用程式的型別[步驟 1:將應用程式 proxy 發佈](#step-1-publish-your-proxy-application)，然後選取 [proxy 應用程式。
5. 在 **應用程式需要何種權限？** 標題之下，選取權限類型。 如果原生應用程式需要存取 proxy 的應用程式 API，以登入的使用者，請選擇**委派的權限**。 如果原生應用程式執行為背景服務或精靈沒有登入的使用者，請選擇**應用程式權限**。
6. 在 **選取 權限**標題之下，選取所需的權限，然後選取**新增權限**。 **API 的權限**頁面會針對原生應用程式現在會顯示 proxy 您新增的應用程式和權限的 API。

## <a name="step-4-edit-the-active-directory-authentication-library"></a>步驟 4：編輯 Active Directory 驗證程式庫

在 Active Directory 驗證程式庫 (ADAL) 的驗證內容中編輯原生應用程式程式碼，以包含下列文字：

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

在範例程式碼所需的資訊可在 Azure AD 入口網站中，如下所示：

| 所需的資訊 | 如何在 Azure AD 入口網站中找到它 |
| --- | --- |
| \<租用戶識別碼 > | **Azure Active Directory** > **屬性** > **目錄識別碼** |
| \<外部 Url 的 Proxy 應用程式 > | **企業應用程式** > *proxy 應用程式* > **應用程式 proxy** > **外部 Url** |
| \<原生應用程式的應用程式識別碼 > | **企業應用程式** > *原生應用程式* > **屬性** > **應用程式識別碼** |
| \<重新導向 URI 的原生應用程式 > | **Azure Active Directory** > **應用程式註冊** > *原生應用程式* > **重新導向 Uri** |
| \<Proxy 應用程式 API Url > | **Azure Active Directory** > **應用程式註冊** > *原生應用程式* > **API 權限**  >  **API / 權限名稱** |

編輯使用這些參數的 ADAL 後，您的使用者可以驗證原生用戶端應用程式，即使它們是在公司網路外部。

## <a name="next-steps"></a>後續步驟

如需有關原生應用程式流程的詳細資訊，請參閱[Azure Active Directory 中的原生應用程式](../develop/native-app.md)。

了解如何設定[單一登入 Azure Active Directory 中的應用程式](what-is-single-sign-on.md#choosing-a-single-sign-on-method)。
