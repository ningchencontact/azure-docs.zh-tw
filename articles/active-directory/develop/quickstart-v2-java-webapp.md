---
title: Microsoft 身分識別平台 Java Web 應用程式快速入門 | Azure
description: 了解如何使用 OpenID Connect 在 Java Web 應用程式上實作 Microsoft 登入
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/11/2019
ms.author: sagonzal
ms.custom: aaddev
ms.openlocfilehash: f1ceae17195c08af380a7448dd4dbf3bfa3f9de2
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211885"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>快速入門：將「使用 Microsoft 登入」新增至 Java Web 應用程式

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

在本快速入門中，您將了解如何整合 Java Web 應用程式與 Microsoft 身分識別平台。 您的應用程式會讓使用者登入、取得存取權杖來呼叫 Microsoft Graph API，以及對 Microsoft Graph API 提出要求。 

完成本指南後，您的應用程式會接受使用個人Microsoft 帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及採用 Azure Active Directory 的公司或組織所提供的公司或學校帳戶登入。

![示範本快速入門所產生之範例應用程式的運作方式](media/quickstart-v2-java-webapp/java-quickstart.svg)

> ## <a name="prerequisites"></a>必要條件
> 若要執行此範例，您將需要： 
> - Java 開發套件 (JDK) 8 或更新版本及 Maven。
>
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>註冊並下載快速入門應用程式
> 有兩個選項可用來啟動快速入門應用程式：快速 (選項 1) 或手動 (選項 2)
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例
>
> 1. 移至 [Azure 入口網站 - 應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)。
> 1. 輸入應用程式的名稱，並選取 [註冊]  。
> 1. 依照指示來下載並自動設定新應用程式。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>選項 2：註冊並手動設定您的應用程式和程式碼範例
> 
>
> #### <a name="step-1-download-the-code-sample"></a>步驟 1：下載程式碼範例
> 
> - [下載程式碼範例](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
>
> #### <a name="step-2-open-applicationproperties"></a>步驟 2：開啟 application.properties
>
> 1. 將 ZIP 檔案解壓縮至本機資料夾。
> 1. (選用) 如果您使用整合式開發環境，請在您慣用的 IDE 中開啟範例。
> 1. 開啟 application.properties  檔案。 當您在下一個步驟中註冊應用程式時，您將會插入 `aad.clientId`、`aad.authority` 和 `aad.secretKey` 的值。


> #### <a name="step-3-register-your-application"></a>步驟 3：註冊您的應用程式
> 若要註冊您的應用程式，並手動將應用程式註冊資訊新增到您的解決方案，請執行下列步驟：
>
> 1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
> 1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
> 1. 瀏覽至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
> 1. 選取 [新增註冊]  。
> 1. 當 [註冊應用程式]  頁面出現時，輸入您應用程式的註冊資訊：
>    - 在 [名稱]  區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `java-webapp`。
>    - 目前先將 [重新導向 URI]  留白，然後選取 [註冊]  。
> 1. 尋找應用程式的 [應用程式 (用戶端) 識別碼]  值。 更新 application.properties  檔案中的 `Enter_the_Application_Id_here` 值。
> 1. 尋找應用程式的 [目錄 (租用戶) 識別碼]  值。 更新 application.properties  檔案中的 `Enter_the_Tenant_Info_Here` 值。 
> 1. 選取 [驗證]  功能表，然後新增下列資訊：
>    - 在 [重新導向 URL]  中新增 `http://localhost:8080/msal4jsamples/secure/aad` 和 `https://localhost:8080/msal4jsamples/graph/users`。
>    - 選取 [ **儲存**]。
> 1. 在左側功能表上，選擇 [憑證與祕密]  ，然後在 [用戶端密碼]  區段中，按一下 [新增用戶端密碼]  ：
>     
>    - 輸入 (執行個體應用程式祕密的) 金鑰描述。
>    - 選取 [1 年]  作為 [金鑰持續時間]。
>    - 當您按一下 [新增]  時，金鑰值會隨即顯示。 
>    - 複製金鑰的值。 開啟您稍早下載的 application.properties  檔案，並將 `Enter_the_Client_Secret_Here` 值更新為金鑰值。 
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步驟 1：在 Azure 入口網站中設定您的應用程式
> 若要讓本快速入門中的程式碼範例能正常運作，您需要：
> 1. 將回覆 URL 新增為 `http://localhost:8080/msal4jsamples/secure/aad` 和 `https://localhost:8080/msal4jsamples/graph/users`。
> 1. 建立用戶端密碼。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這項變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-aspnet-webapp/green-check.png) 您的應用程式已設定了這些屬性。
> 
> #### <a name="step-2-download-the-code-sample"></a>步驟 2：下載程式碼範例
> 
> - [下載程式碼範例](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
> 
> #### <a name="step-3-configure-the-code-sample"></a>步驟 3：設定程式碼範例 
> 
> 1. 將 ZIP 檔案解壓縮至本機資料夾。
> 1. 如果您使用整合式開發環境，請在您慣用的 IDE 中開啟範例 (選用)。
> 1. 開啟 **application.properties** 檔案，該檔案位於 src/main/resources/  。
> 1. 取代應用程式屬性。
>   1. 找出 `aad.clientId`，並將 `Enter_the_Application_Id_here` 的值更新為已註冊應用程式的 [應用程式 (用戶端) 識別碼]  值。 
>   1. 找出 `aad.authority`，並將 `Enter_the_Tenant_Name_Here` 的值更新為已註冊應用程式的 [目錄 (租用戶) 識別碼]  值。
>   1. 找出 `aad.secretKey`，並將 `Enter_the_Client_Secret_Here` 的值更新為您在 [憑證與祕密]  中為已註冊應用程式建立的 [用戶端密碼]  。

#### <a name="step-4-run-the-code-sample"></a>步驟 4：執行程式碼範例
1. 執行程式碼範例，然後開啟瀏覽器並流覽至 http://localhost:8080  。
1. 首頁包含 [登入]  按鈕。 按一下 [登入]  按鈕，即可重新導向至 Azure Active Directory。 系統會提示使用者輸入其認證。  
1. 在 Azure Active Directory 上成功驗證之後，系統會將他們重新導向至 http://localhost:8080/msal4jsamples/secure/aad  。 他們已正式登入應用程式，而頁面應該會顯示已登入帳戶的資訊。 其中也會包含用於以下作業的按鈕： 
    - 登出  ：從應用程式登出目前的使用者，並將他們重新導向至首頁。
    - 顯示使用者  ：取得 Microsoft Graph 的權杖，然後使用連結至要求的權杖來呼叫 Microsoft Graph，以取得租使用者中的所有使用者。


## <a name="more-information"></a>詳細資訊

### <a name="getting-msal"></a>取得 MSAL
MSAL4J 是程式庫，用來登入使用者並要求權杖，該權杖是用來存取受 Microsoft 身分識別平台保護的 API。 藉由對應用程式中的 pom.xml 或 build.gradle 檔案進行下列變更，您就能使用 Maven 或 Gradle 來管理您的相依性，進而將 MSAL4J 新增至您的應用程式。 

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.5.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```


### <a name="msal-initialization"></a>MSAL 初始化
您可以藉由將下列程式碼新增至要使用 MSAL4J 的檔案頂端，以將參考新增至 MSAL4J： 

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>後續步驟

深入了解權限和同意：

> [!div class="nextstepaction"]
> [權限和同意](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-permissions-and-consent)

若要深入了解此案例的驗證流程，請參閱 OAuth 2.0 授權碼流程：

> [!div class="nextstepaction"]
> [授權碼 OAuth 流程](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-auth-code-flow)

協助我們改善 Microsoft 身分識別平台。 完成問卷調查簡短的兩個問題，告訴我們您的想法。

> [!div class="nextstepaction"]
> [Microsoft 身分識別平台問卷調查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
