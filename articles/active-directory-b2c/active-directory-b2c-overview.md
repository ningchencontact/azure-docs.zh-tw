---
title: 什麼是 Azure Active Directory B2C？ | Microsoft Docs
description: 了解如何使用 Azure Active Directory B2C 在應用程式中建立和管理身分識別體驗，例如註冊/登入和設定檔管理。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 33c6f9e06439ebeb17608aa374532ac4f7a020cf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701364"
---
# <a name="what-is-azure-active-directory-b2c"></a>什麼是 Azure Active Directory B2C？

Azure Active Directory (Azure AD) B2C 是一項企業對消費者身分識別管理服務。 此服務可讓您自訂和控制使用者如何安全地與您的 Web、桌面、行動或單頁應用程式互動。 透過 Azure AD B2C，使用者可以註冊、登入、重設密碼及編輯設定檔。 Azure AD B2C 可實作某種形式的 OpenID Connect 和 OAuth 2.0 通訊協定。 安全性權杖及其宣告可讓您提供資源的安全存取，這是此類通訊協定實作中的關鍵要素。

*使用者旅程圖*是一個指定原則的要求，可控制使用者和您的應用程式與 Azure AD B2C 互動的行為。 您可以透過兩種途徑在 Azure AD B2C 中定義使用者旅程圖。 

如果您是應用程式開發人員，無論是否具備身分識別專業知識，您都可以選擇使用 Azure 入口網站來定義常用的身分識別使用者流程。 如果您是身分識別專業人員、系統整合者、顧問或內部身分識別小組成員，且熟悉 OpenID Connect 流程並了解識別提供者和宣告式驗證，則可以選擇以 XML 為基礎的自訂原則。

在定義使用者旅程圖之前，您必須先建立 Azure AD B2C 租用戶，並在租用戶中註冊您的應用程式和 API。 完成這些工作後，即可開始定義具有使用者流程或自訂原則的使用者旅程圖。 您也可以選擇性地新增或變更識別提供者，或自訂使用者體驗旅程圖的方式。

## <a name="protocols-and-tokens"></a>通訊協定和權杖

Azure AD B2C 支援在使用者旅程圖中使用 [OpenID Connect 和 OAuth 2.0 通訊協定](active-directory-b2c-reference-protocols.md)。 在 Azure AD B2C 的 OpenID Connect 實作中，您的應用程式會向 Azure AD B2C 發出驗證要求，以起始使用者旅程圖。 

提出 Azure AD B2C 的要求後會產生安全性權杖，例如[識別碼權杖或存取權杖](active-directory-b2c-reference-tokens.md)。 此安全性權杖會定義使用者的身分識別。 權杖接收自 Azure AD B2C 端點，例如 `/token` 或 `/authorize` 端點。 經由這些權杖，您可以存取可用來驗證身分識別和允許存取安全資源的宣告。

## <a name="tenants-and-applications"></a>租用戶和應用程式

在 Azure AD B2C 中，*租用戶*代表您的組織，而實際上是一個使用者目錄。 每個 Azure AD B2C 租用戶都不相同，並與其他 Azure AD B2C 租用戶分開。 您可能已有 Azure Active Directory 租用戶，而 Azure AD B2C 租用戶是不同的租用戶。 租用戶包含已註冊要使用您的應用程式之使用者的相關資訊。 例如，密碼、設定檔資料和權限。 如需詳細資訊，請參閱[教學課程：建立 Azure Active Directory B2C 租用戶](tutorial-create-tenant.md)。

在設定應用程式以使用 Azure AD B2C 之前，您必須先使用 Azure 入口網站在租用戶中註冊應用程式。 註冊程序會收集和指派一些值給您的應用程式。 這些值包括可唯一識別應用程式的應用程式識別碼，以及用來將回應傳回給應用程式的重新導向 URI。

每個應用程式的互動都遵循類似的高階模式：

1. 應用程式引導使用者執行原則。
2. 使用者完成根據原則定義來完成原則。
3. 應用程式接收權杖。
4. 應用程式使用權杖嘗試存取資源。
5. 資源伺服器驗證權杖，以確認可授與存取權。
6. 應用程式定期重新整理權杖。

若要註冊 Web 應用程式，請完成[教學課程：註冊應用程式以啟用使用 Azure AD B2C 的註冊和登入](tutorial-register-applications.md)中的步驟。 您也可以[將 web API 應用程式新增至您的 Azure Active Directory B2C 租用戶](add-web-application.md)，或[將原生用戶端應用程式新增至您的 Azure Active Directory B2C 租用戶](add-native-application.md)。

## <a name="user-journeys"></a>使用者旅程

使用者旅程圖中的原則可定義為[使用者流程](active-directory-b2c-reference-policies.md)或[自訂原則](active-directory-b2c-overview-custom.md)。 在 Azure 入口網站中，可以取得針對最常用身分識別工作預先定義的使用者流程，而這些工作包括註冊、登入和設定檔編輯。

使用者旅程圖可讓您進行下列設定來控制行為：

- 使用者可用來註冊應用程式的社交帳戶
- 對使用者收集的資料，例如名字或郵遞區號
- Multi-Factor Authentication
- 頁面的外觀與風格
- 傳回至應用程式的資訊

自訂原則是一種組態檔，可定義 Azure AD B2C 租用戶中的[身分識別體驗架構](trustframeworkpolicy.md)的行為。 身分識別體驗架構是建立多方信任並完成使用者旅程圖中各個步驟的基礎平台。 

您可變更自訂原則來完成多項工作。 自訂原則是一個或多個 XML 格式的檔案，各檔案在階層鏈中彼此參考。 [入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)可供自訂原則用來啟用常見的身分識別工作。 

您可以視需要在 Azure AD B2C 租用戶中使用不同類型的自訂原則或使用者流程，並且可以跨應用程式重複使用。 此彈性可讓您在稍微變更或完全不變更程式碼的情況下，定義及修改使用者身分識別體驗。 您可以在 HTTP 驗證要求中新增特定查詢參數，以使用原則。 若要建立您自己的自訂原則，請參閱[在 Azure Active Directory B2C 中開始使用自訂原則](active-directory-b2c-get-started-custom.md)。

## <a name="identity-providers"></a>識別提供者 

在您的應用程式中，您可以讓使用者使用不同的識別提供者登入。 *識別提供者*可建立、維護及管理身分識別資訊，同時對應用程式提供驗證服務。 您可以使用 Azure 入口網站新增 Azure AD B2C 支援的識別提供者。 

您在應用程式中通常只會使用一個識別提供者，但您可以選擇新增更多個。 若要在您的 Azure AD B2C 租用戶中設定身分識別提供者，您必須先在識別提供者開發人員網站上建立應用程式，然後從您建立的識別提供者應用程式中記下應用程式識別碼或用戶端識別碼以及密碼或用戶端密碼。 此識別碼和密碼後續將用來設定您的應用程式。 

下列文章說明將一些常見的識別提供者新增至使用者流程的步驟：

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Microsoft 帳戶](active-directory-b2c-setup-msa-app.md)

下列文章說明將一些常見的識別提供者新增至自訂原則的步驟：
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Microsoft 帳戶](active-directory-b2c-custom-setup-msa-idp.md)

如需詳細資訊，請參閱[教學課程：在 Azure Active Directory B2C 中將識別提供者新增至您的應用程式](tutorial-add-identity-providers.md)。


## <a name="page-customization"></a>自訂頁面

在使用者旅程圖中對客戶呈現的多數 HTML 和 CSS 內容都是可控制的。 您可以使用頁面自訂功能，自訂任何自訂原則或使用者流程的外觀與風格。 您可以使用此自訂功能來維護應用程式與 Azure AD B2C 之間的品牌和視覺一致性。 

Azure AD B2C 會在使用者的瀏覽器中執行程式碼，並使用名為「跨原始資源共用 (CORS)」的新式方法。 首先，您必須在原則中使用自訂 HTML 內容指定 URL。 Azure AD B2C 會合併使用者介面元素與從您 URL 載入的 HTML 內容，然後對使用者顯示此頁面。

您可以在查詢字串中將參數傳送至 Azure AD B2C。 將參數傳遞至您的 HTML 端點，即可動態變更網頁內容。 例如，您可以根據您從 Web 或行動裝置應用程式傳遞的參數，變更註冊或登入頁面的背景影像。

若要自訂使用者流程中的頁面，請參閱[教學課程：在 Azure Active Directory B2C 中自訂使用者介面體驗](tutorial-customize-ui.md)。 若要對自訂原則中的頁面進行自訂，請參閱[在 Azure Active Directory B2C 中使用自訂原則來自訂應用程式的使用者介面](active-directory-b2c-ui-customization-custom.md)或[在 Azure Active Directory B2C 中使用自訂原則設定具有動態內容的 UI](active-directory-b2c-ui-customization-custom-dynamic.md)。

## <a name="developer-resources"></a>開發人員資源

### <a name="client-applications"></a>用戶端應用程式

您可以選擇 [iOS](active-directory-b2c-devquickstarts-ios.md)、[Android](active-directory-b2c-devquickstarts-android.md) 和 .NET 等平台的應用程式。 Azure AD B2C 可讓您的使用者執行這些動作，同時保護其身分識別。

如果您是 ASP.NET Web 應用程式開發人員，請使用[教學課程：使用 Azure AD B2C 讓 Web 應用程式透過帳戶進行驗證](active-directory-b2c-tutorials-web-app.md)中的步驟設定您的應用程式，以進行帳戶驗證。

如果您是桌面應用程式開發人員，請使用[教學課程：使用 Azure AD B2C 讓桌面應用程式透過帳戶進行驗證](active-directory-b2c-tutorials-desktop-app.md)中的步驟設定您的應用程式，以進行帳戶驗證。

如果您是使用 Node.js 的單頁應用程式開發人員，請使用[教學課程：使用 Azure AD B2C 讓單頁應用程式透過帳戶進行驗證](active-directory-b2c-tutorials-spa.md)中的步驟設定您的應用程式，以進行帳戶驗證。

### <a name="apis"></a>API
如果您的用戶端或 Web 應用程式需要呼叫 API，您可以在 Azure AD B2C 中設定這些資源的安全存取。

如果您是 ASP.NET Web 應用程式開發人員，請使用[教學課程：使用 Azure Active Directory B2C 授與存取 ASP.NET Web API 的權限](active-directory-b2c-tutorials-web-api.md)中的步驟設定您的應用程式，以呼叫受保護的 API。

如果您是桌面應用程式開發人員，請使用[教學課程：使用 Azure Active Directory B2C 授與從桌面應用程式存取 Node.js Web API 的權限](active-directory-b2c-tutorials-desktop-app-webapi.md)中的步驟設定您的應用程式，以呼叫受保護的 API。

如果您是使用 Node.js 的單頁應用程式開發人員，請使用[教學課程：使用 Azure Active Directory B2C 授與從單頁應用程式存取 ASP.NET Core Web API 的權限](active-directory-b2c-tutorials-spa-webapi.md)中的步驟設定您的應用程式，以進行帳戶驗證。

### <a name="javascript"></a>JavaScript

您可以將自己的 JavaScript 用戶端程式碼新增至 Azure AD B2C 中的應用程式。 若要在您的應用程式中設定 JavaScript，您可以定義[頁面合約](page-contract.md)，並在使用者流程或自訂原則中啟用 [JavaScript](javascript-samples.md)。

### <a name="user-accounts"></a>使用者帳戶

有許多常見的租用戶管理工作需要以程式設計方式執行。 使用者管理是主要範例。 您可能需要將現有的使用者存放區移轉至 Azure AD B2C 租用戶。 您希望在自己的頁面上裝載使用者註冊，並在幕後的 Azure AD B2C 目錄中建立使用者帳戶。 這類工作需要能夠建立、讀取、更新和刪除使用者帳戶。 這些工作都可以透過 [Azure AD 圖形 API](active-directory-b2c-devquickstarts-graph-dotnet.md) 來完成。

## <a name="next-steps"></a>後續步驟

繼續進行教學課程，開始為您的應用程式進行註冊和登入體驗的設定。

> [!div class="nextstepaction"]
> [教學課程：建立 Azure Active Directory B2C 租用戶](tutorial-create-tenant.md)
