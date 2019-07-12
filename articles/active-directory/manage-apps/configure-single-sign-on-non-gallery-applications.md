---
title: 單一登入-非資源庫應用程式-Microsoft 身分識別平台 |Microsoft Docs
description: Microsoft 身分識別平台 (Azure AD) 中的非資源庫應用程式設定單一登入 (SSO)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a72cb7bc7feeba984d568a0465d4f23a494496e8
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807642"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>在 Microsoft 身分識別平台中設定單一登入非資源庫應用程式

本文是關於此功能可讓系統管理員來設定單一登入應用程式的 Microsoft 身分識別平台應用程式的組件庫中遺漏*而不需要撰寫程式碼*。

如果您要改為尋找有關如何透過程式碼將自訂應用程式與 Azure AD 整合的開發人員指導方針，請參閱 [Azure AD 的驗證案例](../develop/authentication-scenarios.md)。

Microsoft 身分識別平台應用程式庫提供的已知能支援的單一登入 Microsoft 身分識別平台，表單中所述的應用程式清單[這篇文章](what-is-single-sign-on.md)。 在您 (假設您是 IT 專業人員或組織中的系統整合者) 找到所要連接的應用程式之後，就可以依照 Azure 入口網站中展示的逐步指示來啟用單一登入。

下列功能也會提供，根據您的授權合約中。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/active-directory/)。

- 自助服務整合的應用程式，使用最新的通訊協定，例如[OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols)來驗證使用者及取得權杖來[Microsoft Graph](https://graph.microsoft.com)。
- 自助整合任何支援的應用程式[安全性聲明標記語言 (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0)身分識別提供者 （SP 起始或 IdP 起始）
- Web 應用程式可在使用 [密碼型 SSO](what-is-single-sign-on.md#password-based-sso)
- 使用的應用程式的自助服務連線[System for Cross-domain Identity Management (SCIM) 通訊協定，來佈建使用者](use-scim-to-provision-users-and-groups.md)
- 能夠在 [Office 365 應用程式啟動器](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/)或 [Azure AD 存取面板](what-is-single-sign-on.md#linked-sign-on)中新增任何應用程式的連結

這些功能可以包含自助 software 整合為您使用的服務 (SaaS) 應用程式，即使沒有人具有應用程式至 Azure AD 應用程式庫尚未上架。 另一個功能是自助服務的整合，您的組織已部署到您控制，在雲端或內部部署伺服器的第三方 web 應用程式。

也稱為*應用程式整合範本*，這些功能支援 SAML、 SCIM 或表單型驗證的應用程式提供標準型連接點。 功能包括彈性的選項和與廣泛的應用程式的相容性的設定。

## <a name="adding-an-unlisted-application"></a>新增未列出的應用程式

Microsoft 身分識別平台提供兩種機制，來註冊應用程式。

使用等現代通訊協定的應用程式[OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md)來驗證其使用者註冊使用[應用程式註冊入口網站](../develop/quickstart-register-app.md)。

若要註冊應用程式使用所有其他類型的[支援的驗證機制](what-is-single-sign-on.md)，例如[SAML](../develop/single-sign-on-saml-protocol.md)通訊協定，請使用**企業應用程式**刀鋒視窗中，連接它們與 Microsoft 身分識別平台。

若要連接未列出的應用程式，使用應用程式整合範本，執行下列步驟：

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)使用 Microsoft 身分識別平台系統管理員帳戶。
1. 選取 **企業應用程式** > **新的應用程式**。
1. （選擇性但建議使用）在 **從資源庫新增**搜尋方塊中，輸入應用程式的顯示名稱。 如果應用程式出現在搜尋結果中，選取它，並略過此程序的其餘部分。
1. 選取 **非資源庫應用程式**。 **新增您自己的應用程式**頁面隨即出現。

   ![顯示 新增您自己的應用程式頁面](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)

1. 輸入新的應用程式的顯示名稱。
1. 選取 [新增]  。

藉由這種方式新增應用程式，您可以提供類似於預先整合的應用程式。 先選取**單一登入**從應用程式的資訊看板。 下一個頁面 (**選取 單一登入方法**) 提供選項，以設定 SSO:

- **SAML**
- **密碼式**
- **已連結**

![顯示 [選取單一登入方法] 頁面](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

如需這些選項的詳細資訊，請參閱這篇文章的下列各節。

## <a name="saml-based-single-sign-on"></a>SAML 型單一登入

選取  **SAML**選項來設定應用程式的 SAML 型驗證。 （這個選項需要應用程式支援 SAML 2.0）。**設定單一登入使用 SAML 設定**頁面隨即出現。

![顯示 設定單一登入 SAML 頁面](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

此頁面具有五個不同的標題：

| 標題號碼 | 標題名稱 | 如需此標題的摘要，請參閱： |
| --- | --- | --- |
| 1 | **基本的 SAML 設定** | [輸入基本的 SAML 組態](#enter-basic-saml-configuration) |
| 2 | **使用者屬性與宣告** | [檢視或自訂在 SAML 權杖中發出的宣告](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **SAML 簽署憑證** | [檢閱憑證到期資料、 狀態和電子郵件通知](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **設定\<應用程式名稱 >** | [設定目標應用程式](#set-up-target-application) |
| 5 | **測試單一登入與\<應用程式名稱 >** | [測試 SAML 應用程式](#test-the-saml-application) |

現在如何使用之 SAML 功能的應用程式，然後再繼續收集資訊。 完成下列各節，應用程式與 Azure AD 之間設定 SSO。

### <a name="enter-basic-saml-configuration"></a>輸入基本 SAML 組態

若要設定 Azure AD，請前往**基本 SAML 組態**標題並選取其**編輯**圖示 （鉛筆）。 您可手動輸入值或上傳中繼資料檔案，以擷取欄位的值。

![顯示基本的 SAML 組態 頁面](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

下列兩個欄位是必要欄位：

- **識別項**。 此值應專門用於識別的單一登入設定的應用程式。 您可以找到此值作為**簽發者**中的項目**AuthnRequest**應用程式傳送 （SAML 要求）。 在應用程式中提供的任何 SAML 中繼資料中，這個值也會顯示為實體識別碼  。 查看應用程式的 SAML 文件，如需有關什麼其**實體識別碼**或是**對象**值。

  下列程式碼示範如何**識別碼**或是**簽發者**會出現在應用程式傳送至 Azure AD SAML 要求：

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **回覆 URL**。 回覆 URL 是應用程式預期接收 SAML 權杖。 此 URL 也稱為 「 判斷提示取用者服務 (ACS) URL。 查看應用程式的 SAML 文件，了解 SAML 權杖回覆 URL 或 ACS URL 的詳細資訊。

  若要設定多個回覆 Url，您可以使用下列 PowerShell 指令碼。

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

下列的三個欄位都是選用項目：

- **登入 URL (SP 起始只)** 。 這個值表示使用者登入此應用程式所前往的地方。 如果應用程式執行 SP 啟始的 SSO，然後當使用者瀏覽至此 url，預存程序會執行必要的重新導向來驗證及登入使用者的 Azure ad。 如果您指定這個欄位時，Azure AD 會使用此 URL 來啟動從 Office 365 和 Azure AD 存取面板應用程式。 如果您省略此欄位時，Azure AD 改為執行 IdP 初始化登入期間從 Office 365、 Azure AD 存取面板或 Azure AD SSO URL 的應用程式啟動 (您可以複製這**儀表板**頁面)。

- **轉送狀態**。 您可以指定，指示應用程式如何將使用者重新導向之後驗證的 SAML 轉送狀態。 值通常是 URL 或 URL 會將使用者帶到應用程式內的特定位置的路徑。

- **登出 URL**。 這個值用來傳送傳回給應用程式的 SAML 登出回應。

如需詳細資訊，請參閱 <<c0> [ 單一登入 SAML 通訊協定](../develop/single-sign-on-saml-protocol.md)。

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>檢閱或自訂 SAML 權杖簽發的宣告

當使用者驗證應用程式時，Azure AD 會發出應用程式的 SAML 語彙基元資訊 （或宣告） 可唯一識別使用者的相關。 根據預設，這項資訊會包含使用者的使用者名稱、 電子郵件地址、 名字和姓氏。

若要檢視或編輯應用程式的 SAML 權杖中傳送的宣告：

- 移至**使用者屬性和宣告**標題並選取**編輯**圖示。 **使用者屬性] & [宣告**頁面隨即出現。

![顯示 使用者屬性與宣告頁面](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

您可能需要編輯在 SAML 權杖中發出，原因有兩個宣告：

- 應用程式需要不同的宣告 URI 或宣告值組。
- 您的應用程式需要**命名的識別項值**宣告必須是在 Microsoft 身分識別平台中所儲存之 username （也稱為使用者主體名稱） 以外的項目。

如需詳細資訊，請參閱[如何：自訂的企業應用程式在 SAML 權杖中發出的宣告](../develop/active-directory-saml-claims-customization.md)。

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>檢閱憑證到期資料、狀態和電子郵件通知

當您建立資源庫 」 或 「 非資源庫應用程式時，Azure AD 會建立從其建立日期的三年到期的應用程式特定憑證。 您需要此憑證才能設定 Azure AD 和應用程式之間的信任。 如需憑證格式的詳細資料，請參閱應用程式的 SAML 文件。

從 Azure AD 中，您可以下載作用中的憑證，直接從主要的 Base64 或 Raw 格式**設定單一登入使用 SAML 設定**頁面。 或者，您可以取得作用中的憑證，藉由下載應用程式中繼資料 XML 檔案，或使用應用程式同盟中繼資料 URL。

若要檢視、 建立或下載您的憑證 （作用中或非使用中），移至**SAML 簽署憑證**標題並選取**編輯**圖示。 **SAML 簽署憑證**隨即出現。

![顯示 [SAML 簽署憑證] 頁面](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

請確認憑證具有：

- *所需的到期日。* 未來，您可以設定最多三年的到期日。
- *狀態為作用中的所需的憑證。* 如果狀態是  **Inactive**，將狀態變更為**Active**。 若要變更狀態，以滑鼠右鍵按一下所需的憑證資料列，然後選取**製作憑證 active**。
- *正確的簽章選項和演算法。*
- *正確的通知電子郵件地址。* 作用中的憑證接近到期日時，Azure AD 會傳送通知，在此欄位中設定電子郵件地址。  

如需詳細資訊，請參閱 <<c0> [ 管理同盟單一登入的憑證](manage-certificates-for-federated-single-sign-on.md)並[進階的憑證簽署 SAML 權杖中的選項](certificate-signing-options.md)。

### <a name="set-up-target-application"></a>設定目標應用程式

若要設定 SSO 的應用程式，找出應用程式的文件。 若要尋找的文件，請前往**設定\<應用程式名稱 >** 標題並選取**檢視的逐步指示**。 文件中會出現**設定登入**頁面。 該頁面會引導您填寫**登入 URL**， **Azure AD 識別項**，並**登出 URL**中的值**設定\<應用程式名稱>** 標題。

必要的值會根據應用程式而有所不同。 如需詳細資訊，請參閱應用程式的 SAML 文件。 **登入 URL**並**登出 URL**值解析為相同的端點，也就是您的 Azure AD 執行個體的 SAML 要求處理端點。 **Azure AD 識別碼**的值**簽發者**SAML 權杖中發出給應用程式。

### <a name="assign-users-and-groups-to-your-saml-application"></a>將使用者和群組指派給您的 SAML 應用程式

一旦您已設定您的應用程式使用 Azure AD 作為 SAML 型身分識別提供者，就幾乎可以進行測試。 為了控制安全性，Azure AD 只會發出權杖，允許使用者登入的應用程式，如果 Azure AD 已授與存取權的使用者。 使用者可存取，直接或透過群組成員資格。

若要將新的使用者或群組指派給您的應用程式中：

1. 在 應用程式提要欄位中，選取**使用者和群組**。 **\<應用程式名稱 >-使用者和群組**頁面隨即出現，顯示目前指派的使用者和群組的清單。
1. 選取 **將使用者新增**。 **加入指派**頁面隨即出現。
1. 選取 **使用者和群組 (\<數字 > 選取)** 。 **使用者和群組**頁面隨即出現，顯示一份可用的使用者和群組。
1. 型別或捲軸來尋找使用者或您想要從清單指派的群組。
1. 選取每個使用者或群組，您要新增，然後選取**選取** 按鈕。 **使用者和群組**頁面就會消失。
1. 在 **新增的工作分派**頁面上，選取**指派**。 **\<應用程式名稱 >-使用者和群組**與顯示在清單中的其他使用者的頁面隨即出現。

   ![顯示 [應用程式的使用者和群組] 頁面](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

從這份清單中，您可以：

- 移除使用者。
- 編輯其角色。
- 更新其認證 （使用者名稱和密碼），讓使用者可以從應用程式使用者的存取面板來進行驗證。

您可以編輯，或一次移除多個使用者或群組。

將使用者指派時，可讓 Azure AD 發出權杖的使用者。 也會導致該應用程式磚出現在使用者的存取面板中。 如果使用者使用 Office 365，則應用程式圖格也會出現在 Office 365 應用程式啟動程式。

> [!NOTE]
> 您可以在應用程式的 [設定]  索引標籤上使用 [上傳標誌]  按鈕，來上傳應用程式的圖格標誌。

### <a name="test-the-saml-application"></a>測試 SAML 應用程式

測試 SAML 應用程式之前，您必須有已設定與 Azure AD 應用程式並指派給應用程式的使用者或群組。 若要測試 SAML 應用程式，請選取**單一登入**，回到**SAML 型登入**頁面。 (如果不同的 SSO 方法已在作用中，選取**變更單一登入模式** > **SAML**太。)然後在**單一登入以測試\<應用程式名稱 >** 標題之下，選取**測試**。 如需詳細資訊，請參閱 <<c0> [ 偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式](../develop/howto-v1-debug-saml-sso-issues.md)。

## <a name="password-single-sign-on"></a>密碼單一登入

選取此選項可設定[密碼型單一登入](what-is-single-sign-on.md)具有 HTML 登入頁面的 web 應用程式。 密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。 它也可用於使用情況下，數個使用者需要共用單一帳戶，例如共用組織的社交媒體應用程式帳戶。

選取後**密碼型**，系統會提示您輸入的應用程式的 web 型登入頁面 URL。

![顯示 [登入 URL] 頁面，輸入登入 URL](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

然後執行下列步驟：

1. 輸入的 URL。 此字串必須包含使用者名稱輸入的欄位的頁面。
1. 選取 [ **儲存**]。 Azure AD 會嘗試剖析輸入的使用者名稱和密碼輸入的登入頁面。
1. 如果 Azure AD 的剖析嘗試失敗，請選取**設定\<應用程式名稱 > 密碼單一登入設定**以顯示**設定登入**頁面。 （如果該嘗試成功，您可以忽略此程序的其餘部分）。
1. 選取 **手動偵測登入欄位**。 會出現描述手動偵測登入欄位的其他指示。

   ![手動設定密碼型單一登入](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)

1. 選取 **擷取登入欄位**。 擷取狀態 頁面隨即開啟新的索引標籤，顯示的訊息**中繼資料擷取目前正在進行**。
1. 如果**需要的存取面板擴充功能**方塊會出現在新的索引標籤中，選取**立即安裝**安裝**My Apps 安全登入延伸模組**瀏覽器延伸模組。 （瀏覽器擴充功能需要 Microsoft Edge、 Chrome 或 Firefox）。然後安裝、 啟動，並啟用延伸模組，並重新整理擷取狀態 頁面。

   瀏覽器延伸模組則會開啟另一個索引標籤會顯示輸入的 URL。

1. 在輸入的 url 索引標籤上，進行登入程序。 在 [使用者名稱和密碼] 欄位中，填滿，然後再次嘗試登入。 （您不需要提供正確的密碼）。

   出現提示，詢問您要儲存所擷取的登入欄位。

1. 選取 [確定]  。 索引標籤關閉、 瀏覽器延伸模組更新擷取狀態 頁面與訊息**中繼資料已更新應用程式**，以及索引標籤也會關閉該瀏覽器。
1. 在 Azure AD**設定登入**頁面上，選取**好，我才能夠登入應用程式成功**。
1. 選取 [確定]  。

之後擷取的登入頁面，您可能會指派使用者和群組，以及您可以設定認證原則，就像一般[密碼 SSO 應用程式](what-is-single-sign-on.md)。

> [!NOTE]
> 您可以在應用程式的 [設定]  索引標籤上使用 [上傳標誌]  按鈕，來上傳應用程式的圖格標誌。

## <a name="existing-single-sign-on"></a>現有單一登入

選取此選項可將貴組織的 Azure AD 存取面板或 Office 365 入口網站中的應用程式的連結。 您可以將連結新增至目前使用 Active Directory Federation Services （或其他同盟服務） 的自訂 web 應用程式中使用這個方法，而不是 Azure AD 進行驗證。 或者，您可以新增特定 SharePoint 網頁或其他只要出現在使用者存取面板上的網頁的深層連結。

選取後**連結**，系統會提示您輸入要連結到的應用程式的 URL。 輸入 URL，然後選取**儲存**。 您可能將使用者和群組指派給應用程式，這會導致應用程式中出現[Office 365 應用程式啟動器](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)或[Azure AD 存取面板](end-user-experiences.md)為那些使用者。

> [!NOTE]
> 您可以在應用程式的 [設定]  索引標籤上使用 [上傳標誌]  按鈕，來上傳應用程式的圖格標誌。

## <a name="related-articles"></a>相關文章

- [如何：自訂的企業應用程式在 SAML 權杖中發出的宣告](../develop/active-directory-saml-claims-customization.md)
- [偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式](../develop/howto-v1-debug-saml-sso-issues.md)
- [Microsoft 身分識別平台 (先前稱為 Azure Active Directory 為開發人員)](../develop/index.yml)
