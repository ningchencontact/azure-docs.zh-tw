---
title: 存取內部 Api 使用 Azure AD 應用程式 Proxy
description: Azure Active Directory 的應用程式 Proxy 可讓安全地存取 Api 的原生應用程式和商務邏輯，裝載在內部部署或雲端 Vm 上。
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c2b99525e3d0a61c02dc502fcd0927ea65993e5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108533"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 的內部部署 Api 的安全存取

您可能會有商務邏輯執行於內部，Api，或在雲端中的虛擬機器上託管。 原生 Android、 iOS、 Mac 或 Windows 應用程式需要使用的資料，或提供使用者互動的 API 端點進行互動。 Azure AD 應用程式 Proxy 並[Azure Active Directory 驗證程式庫 (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries)可讓您的原生應用程式安全地存取您的內部 Api。 Azure Active Directory 應用程式 Proxy 是更快速且更安全的解決方案，比開啟防火牆連接埠，並且控制驗證和授權應用程式層級。 

這篇文章會引導您設定 Azure AD 應用程式 Proxy 解決方案，來裝載原生應用程式可以存取的 web API 服務。 

## <a name="overview"></a>概觀

下圖顯示傳統的方式，將內部部署 Api 發行。 這個方法需要開啟連入連接埠 80 和 443。

![傳統的 API 存取](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

下圖顯示如何使用 Azure AD 應用程式 Proxy，以安全地將 Api 發行而不需要開啟任何連入連接埠：

![Azure AD 應用程式 Proxy API 存取](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD 應用程式 Proxy 的骨幹方案，身為 API 存取的公用端點，並提供驗證和授權。 您可以從各式各樣的平台存取您的 Api，利用[ADAL](/azure/active-directory/develop/active-directory-authentication-libraries)程式庫。 

因為 Azure AD 應用程式 Proxy 的驗證和授權都建置在 Azure AD 之上，您可以使用 Azure AD 條件式存取，以確保只有受信任的裝置可以存取透過應用程式 Proxy 發佈的 Api。 針對桌上型電腦和受 Intune 管理中使用 Azure AD Join 或 Azure AD 混合式已加入裝置。 您也可以充分利用 Azure Active Directory Premium 功能，例如 Azure Multi-factor Authentication 和機器學習服務為基礎的安全性[Azure Identity Protection](/azure/active-directory/active-directory-identityprotection)。

## <a name="prerequisites"></a>必要條件

若要依照本逐步解說中，您需要：

- 與 Azure 目錄，可以建立並註冊應用程式的帳戶系統管理員存取權
- 範例 web API 和原生用戶端應用程式 [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>發行的 API，透過應用程式 Proxy

若要發行的 API 應用程式 Proxy 透過內部網路之外，您可以遵循相同的模式與發佈的 web 應用程式。 如需詳細資訊，請參閱[教學課程：Azure Active Directory 中新增透過應用程式 Proxy 遠端存取內部部署應用程式](application-proxy-add-on-premises-application.md)。

若要發佈 SecretAPI web API，以透過 Application Proxy:

1. 建置及發佈範例 SecretAPI 專案為您的本機電腦或內部網路上的 ASP.NET web 應用程式。 請確定您可以在本機的 web 應用程式。 
   
1. 在  [Azure 入口網站](https://portal.azure.com)，選取**Azure Active Directory**在左側導覽中。 然後，在**概觀**頁面上，選取**企業應用程式**。
   
1. 在頂端**企業應用程式-所有應用程式**頁面上，選取**新的應用程式**。
   
1. 在 **新增應用程式**頁面的 **新增您自己的應用程式**，選取**在內部部署應用程式**。 
   
1. 如果您沒有安裝應用程式 Proxy 連接器，系統會提示您安裝它。 選取 **下載應用程式 Proxy 連接器**下載並安裝連接器。 
   
1. 一旦您在安裝應用程式 Proxy 連接器**新增您自己的內部部署應用程式**頁面：
   
   1. 請輸入*SecretAPI*旁**名稱**。
      
   1. 輸入您用來存取內部網路中的 API 旁的 URL**內部 Url**。 
      
   1. 請確定**預先驗證**設為**Azure Active Directory**。 
      
   1. 選取 **新增**上方的頁面上，並等候建立應用程式。
   
   ![新增 API 應用程式](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. 在 **企業應用程式-所有應用程式**頁面上，選取**SecretAPI**應用程式。 
   
1. 在  **SecretAPI-概觀**頁面上，選取**屬性**在左側導覽中。
   
1. 您不想 Api 中的使用者可以使用**MyApps**  面板中，因此，請將**是否要向使用者顯示**來**No**底部**屬性**頁面上，然後按**儲存**。
   
   ![使用者看不到](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
您已發行您的 web API 透過 Azure AD 應用程式 Proxy。 現在，新增可存取應用程式使用者。 

1. 在  **SecretAPI-概觀**頁面上，選取**使用者和群組**在左側導覽中。
   
1. 在 **使用者和群組**頁面上，選取**加入使用者**。  
   
1. 在 **將作業加入**頁面上，選取**使用者和群組**。 
   
1. 在 **使用者和群組**頁面上，搜尋並選取可以存取應用程式，至少包括您自己的使用者。 選取 所有使用者之後, 再選擇**選取**。 
   
   ![選取並指派使用者](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. 回到**將作業加入**頁面上，選取**指派**。 

> [!NOTE]
> 使用整合式 Windows 驗證的 Api 可能會需要[額外的步驟](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)。

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>註冊原生應用程式，並授與存取 API

原生應用程式是在特定平台或裝置上使用開發的程式。 原生應用程式可以連接並存取 API 之前，您必須在 Azure AD 中註冊它。 下列步驟示範如何註冊原生應用程式，並讓它存取 web API，您透過應用程式 Proxy 發佈。

若要註冊 AppProxyNativeAppSample 原生應用程式：

1. 在 Azure Active Directory**概觀**頁面上，選取**應用程式註冊**，並在頂端**應用程式註冊**窗格中，選取**新增註冊**.
   
1. 在 **註冊應用程式**頁面：
   
   1. 底下**名稱**，輸入*AppProxyNativeAppSample*。 
      
   1. 在 [支援的帳戶類型]  底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]  。 
      
   1. 底下**重新導向 URL**、 下拉式清單，然後選取**公開用戶端 （行動和桌面）** ，然後輸入*https:\//appproxynativeapp*。 
      
   1. 選取 **註冊**，並等候 已成功註冊應用程式。 
      
      ![新增應用程式註冊](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
您現在已在 Azure Active Directory 中註冊 AppProxyNativeAppSample 應用程式。 若要 SecretAPI web API 可讓您的原生應用程式存取權：

1. 在 Azure Active Directory**概觀** > **應用程式註冊**頁面上，選取**AppProxyNativeAppSample**應用程式。 
   
1. 在  **AppProxyNativeAppSample**頁面上，選取**API 的權限**在左側導覽中。 
   
1. 在  **API 的權限**頁面上，選取**新增權限**。
   
1. 在第一天**要求的 API 權限**頁面上，選取**我的組織使用的 Api**索引標籤，然後搜尋並選取**SecretAPI**。 
   
1. 在下一次**要求的 API 權限**頁面上，選取旁邊的核取方塊**user_impersonation**，然後選取**新增權限**。 
   
    ![選取 API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. 回到**API 的權限**頁面上，您可以選取**授與 Contoso 的系統管理員同意**不必個別同意的應用程式時，防止其他使用者。 

## <a name="configure-the-native-app-code"></a>設定原生應用程式程式碼

最後一個步驟是設定原生應用程式。 下列程式碼片段*Form1.cs* Nativeclient-windowsstore 範例應用程式中的檔案會導致 ADAL 程式庫取得的權杖要求 API 呼叫時，並將它附加為持有人的應用程式標頭。 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
若要設定連接到 Azure Active Directory，並呼叫 API 應用程式 Proxy 的原生應用程式，更新中的預留位置值*App.config* Nativeclient-windowsstore 範例應用程式，以從 Azure AD 的值的檔案： 

- 貼上**目錄 （租用戶） 識別碼**在`<add key="ida:Tenant" value="" />`欄位。 您可以尋找並複製此值 (GUID)**概觀**的其中一個應用程式的頁面。 
  
- 貼上 AppProxyNativeAppSample**應用程式 （用戶端） 識別碼**在`<add key="ida:ClientId" value="" />`欄位。 您可以尋找並複製此值 (GUID)，從 AppProxyNativeAppSample**概觀**頁面。
  
- 貼上 AppProxyNativeAppSample**重新導向 URI**在`<add key="ida:RedirectUri" value="" />`欄位。 您可以尋找並複製此值 (URI)，從 AppProxyNativeAppSample**驗證**頁面。 
  
- 貼上 SecretAPI**應用程式識別碼 URI**在`<add key="todo:TodoListResourceId" value="" />`欄位。 您可以尋找並複製此值 (URI)，從 SecretAPI**公開 API**頁面。
  
- 貼上 SecretAPI **Home Page URL**在`<add key="todo:TodoListBaseAddress" value="" />`欄位。 您可以尋找並複製此值 (URL) 從 SecretAPI**商標**頁面。

設定參數之後，建置並執行原生應用程式。 當您選取**登入** 按鈕，應用程式可以可讓您登入，並接著顯示成功畫面中，以確認它已成功連線至 SecretAPI。

![成功](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>後續步驟

- [教學課程：Azure Active Directory 中新增透過應用程式 Proxy 遠端存取內部部署應用程式](application-proxy-add-on-premises-application.md)
- [快速入門：設定用戶端應用程式以存取 web Api](../develop/quickstart-configure-app-access-web-apis.md)
- [如何啟用與 proxy 應用程式互動的原生用戶端應用程式](application-proxy-configure-native-client-application.md)
