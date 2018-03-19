---
title: "將 AD FS 內部部署應用程式移轉至 Azure AD。 | Microsoft Docs"
description: "本文旨在協助組織了解如何將內部部署應用程式移轉至 Azure AD (內容著重於同盟 SaaS 應用程式)。"
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2018
ms.author: billmath
ms.openlocfilehash: f1256e2fefe1ad3d37403f37804888ab9dcf6e99
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="migrate-ad-fs-on-premises-apps-to-azure"></a>將 AD FS 內部部署應用程式移轉至 Azure AD 

本文旨在協助組織了解如何將內部部署應用程式移轉至 Azure AD。  其內容著重於同盟 SaaS 應用程式。  本文件不提供逐步指引。  其提供的概念性指引可協助您藉由了解內部部署設定如何平移至 Azure AD 來達成移轉。 其中也涵蓋大多數常見案例的需求。

## <a name="introduction"></a>簡介

如果您有包含使用者帳戶的內部部署目錄，則您可能有至少一個或兩個應用程式。  而且這些應用程式已設定為允許使用者利用這些身分識別登入來進行存取。

而如果您就像大部分的組織一樣，您可能踏上了採用雲端應用程式和身分識別的路程。  您可能已啟用並執行 Office 365 和 Azure AD Connect。  或許您已針對一些重要工作負載 (但並非全部) 安裝雲端架構 SaaS 應用程式。  

許多組織都讓 SaaS 或自訂企業營運 (LoB) 應用程式，連同以 Office 365 和 Azure AD 為基礎的應用程式一起同盟至內部部署登入服務 (例如 Active Directory 同盟服務 (AD FS))。  本移轉指南說明為何及如何將內部部署應用程式移轉至 Azure AD。

>[!NOTE]
>本指南提供 SaaS 應用程式設定和移轉的詳細資訊，以及有關自訂 LoB 應用程式的概觀資訊。  未來計劃提供自訂 LoB 應用程式的更詳細指引。

圖 1：與內部部署服務直接連線的應用程式![內部部署](media/migrate-adfs-apps-to-azure/migrate1.png)

圖 2：透過 Azure AD 同盟的應用程式![Azure](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="why-migrate-apps-to-azure-ad"></a>為何要將應用程式移轉至 Azure AD？

對於已使用 AD FS、Ping 或其他內部部署驗證提供者的組織而言，將應用程式移轉至 Azure AD 具有下列優點：

**更安全的存取**
- 使用 [Azure AD 條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)，為每個應用程式設定更細微的存取控制項，包括 Multi-Factor Authentication (MFA)。  您可以用現今處理 Office 365 的相同方式，將原則套用至 SaaS 和自訂應用程式
- 若要以可識別不安全流量的機器學習和啟發學習法為基礎，來偵測威脅及保護登入，請利用 Azure AD 內建且持續進化的 [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 功能

**Azure AD B2B 共同作業**
- 一旦 SaaS 應用程式的登入作業是以 Azure AD 為基礎，您即可讓合作夥伴透過 [Azure AD B2B 共同作業](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)存取雲端資源。

**更輕鬆管理 Azure AD 的體驗與其他功能**
- Azure AD 是 SaaS 應用程式的識別提供者，可支援額外的功能，例如每個應用程式的權杖簽署憑證、[可設定的憑證到期日](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)，以及根據 Azure AD 身分識別在主要資源庫應用程式中[自動佈建](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)使用者帳戶

**保留內部部署識別提供者的優點**
- 獲得 Azure AD 優點的同時，您可以繼續使用內部部署解決方案進行驗證，因此內部部署 Multi-Factor Authentication (MFA) 解決方案、記錄和稽核等優點都會保留下來 

**快速了解如何汰換內部部署識別提供者**
- 對於想要汰換內部部署驗證產品的組織而言，將應用程式移轉至 Azure AD 可讓您經由減輕工作而更輕鬆地轉換 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>將內部部署的應用程式類型對應至 Azure AD 中的應用程式類型
大部分的應用程式都會根據其使用的登入類型而歸納為其中一個類別。  這些類別決定了在 Azure AD 中表示應用程式的方式。

簡單地說，SAML 2.0 應用程式可以透過 Azure AD 應用程式資源庫來與 Azure AD 整合，或整合為非資源庫應用程式。  使用 OAuth 2.0 或 OpenID Connect 的應用程式可與 Azure AD 整合 (類似於「應用程式註冊」)。  請繼續閱讀來取得更多詳細資料。

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>同盟 SaaS 應用程式與自訂 LoB 應用程式
同盟應用程式包括下列類別的應用程式。

- SaaS 應用程式 
    - 如果您的使用者登入 SaaS 應用程式 (例如 Salesforce、ServiceNow 或 Workday)，而您正要整合至內部部署識別提供者 (例如 AD FS 或 Ping)，那麼您就是使用 SaaS 應用程式的同盟登入。
    - 應用程式通常使用 SAML 2.0 通訊協定進行同盟登入。
    - 此類別的應用程式可經由資源庫來與 Azure AD 整合為企業應用程式，或整合為非資源庫的應用程式。
- 自訂 LoB 應用程式
    - 這是指非 SaaS 應用程式，這類應用程式是由組織內部開發，或當作資料中心安裝的標準套裝產品提供。  其中包括 SharePoint 應用程式以及在 Windows Identity Foundation (WIF) 上建置的應用程式。
    - 應用程式可以使用 SAML 2.0、WS-同盟、OAuth 或 OpenID Connect 進行同盟登入
    - 使用 Oauth 2.0、OpenID Connect 或 WS-同盟的應用程式可與 Azure AD 整合為應用程式註冊，而使用 SAML 2.0 或 WS-同盟的自訂應用程式則可整合為企業應用程式內的非資源庫應用程式

### <a name="non-federated-apps"></a>非同盟應用程式
此外，使用 Azure AD 應用程式 Proxy 和相關功能，可以整合非同盟應用程式與 Azure AD。  如需這些功能的詳細資訊，請遵循下列連結：
- 使用 Windows 整合式驗證 (WIA) 直接整合至 Active Directory 的應用程式
    - 這類應用程式可以透過 [Azure AD 應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) 整合至 Azure AD
- 透過代理程式來與單一登入提供者整合並使用標頭進行授權的應用程式
    - 搭配使用 Azure AD 應用程式 Proxy 和[適用於 Azure AD 的 Ping Access](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/)，您可以為使用所安裝代理程式進行登入和標頭式授權的內部部署應用程式，設定以 Azure AD 為基礎的登入

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>將內部部署同盟應用程式平移至 Azure AD 
幸運的是，AD FS 與 Azure AD 的運作方式相似，所以設定信任、登入及登出 URL 和識別碼的概念適用於兩者。  不過，當您進行轉換時，需要了解一些小差異。

在下表中，我們對應了由 AD FS、Azure AD 和 SaaS 應用程式共用的幾個重要概念，協助您進行平移。 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>代表 Azure AD 或 AD FS 中的應用程式
移轉一開始會評估應用程式在內部部署環境中的設定方式，然後將該設定對應至 Azure AD。  以下是 AD FS 信賴憑證者組態元素與 Azure AD 中相對應元素的對應。  
- AD FS 字詞：信賴憑證者或信賴憑證者信任
- Azure AD 字詞：企業應用程式或應用程式註冊 (取決於應用程式的類型)

|應用程式組態元素|說明|在 AD FS 組態內|Azure AD 組態中的相對應位置|SAML 權杖元素|
|-----|-----|-----|-----|-----|
|應用程式登入 URL|此應用程式的登入頁面 URL。 使用者要在 SP 起始的 SAML 流程中起始登入應用程式時會前往此處。|N/A|在 Azure AD 中，此登入 url 會在 Azure 入口網站內應用程式的 [單一登入屬性] 中設定為 [登入 URL]。</br></br>(您必須按一下 [顯示進階 URL] 設定才能查看登入 URL)||
|應用程式回覆 URL|以 IdP 觀點看待的應用程式 URL。  使用者在 IDP 登入後，使用者和權杖會被送往此 URL。</br></br>  有時候，這稱為 SAML 判斷提示消費者端點。|可在應用程式的 [AD FS 信賴憑證者信任] 中找到。  以滑鼠右鍵按一下信賴憑證者，並選擇 [屬性] -> [端點] 索引標籤。|在 Azure AD 中，此回覆 url 會在 Azure 入口網站內應用程式的 [單一登入屬性] 中設定為 [回覆 URL]。</br></br>(您必須按一下 [顯示進階 URL] 設定才能查看回覆 URL)|對應至 SAML 權杖中的目的地元素。</br></br>  範例值：https://contoso.my.salesforce.com|
|應用程式登出 URL|當使用者登出某個應用程式時，該 URL 會收到「登出清除」要求，也就是要從 IDP 讓使用者登入的其他所有應用程式登出。|可在 AD FS 管理的 [信賴憑證者信任] 之下找到。  以滑鼠右鍵按一下信賴憑證者並選擇 [屬性]，然後按一下 [端點] 索引標籤|N/A – Azure AD 不支援「單一登出」(表示登出所有的應用程式)。  它只會讓使用者登出 Azure AD 本身。|NA|
|應用程式識別碼|以 IdP 觀點看待的應用程式識別碼。登入 URL 值經常用於此識別碼 (但並不一定)</br></br>  有時候，應用程式會將此稱為「實體 ID」。|在 AD FS 中，這是 [信賴憑證者識別碼]：以滑鼠右鍵按一下信賴憑證者信任，並選擇 [屬性]-> 按一下 [識別碼] 索引標籤|在 Azure AD 中，此識別碼會在 Azure 入口網站內應用程式的 [單一登入屬性] 中設定為 [網域及 URL] 之下的 [識別碼] (您必須按一下 [顯示進階 URL 設定] 核取方塊)|對應到 SAML 權杖中的 [對象] 元素|
|應用程式同盟中繼資料|應用程式的同盟中繼資料位置。  由 IdP 用於自動更新特定組態設定，例如端點或加密憑證。|在應用程式的 AD FS 信賴憑證者信任中可找到應用程式的 [同盟中繼資料 URL]。  以滑鼠右鍵按一下信任並選擇 [屬性]，然後按一下 [監視] 索引標籤。|N/A - Azure AD 不支援直接取用應用程式同盟中繼資料|NA|
|使用者識別碼 / NameID|此屬性用來唯一表示由 Azure AD 或 AD FS 提供給應用程式的使用者識別。</br></br>  通常是 UPN 或使用者的電子郵件地址。|在 AD FS 中，這會被視為信賴憑證者的宣告規則。  在大部分情況下，這就是發出類型以 "nameidentifier" 結尾之宣告的宣告規則|在 Azure AD 中，可在 Azure 入口網站內應用程式的 [單一登入屬性] 中 [使用者屬性] 標頭之下找到 [使用者識別碼]。</br></br>預設會使用 UPN。|從 IDP 傳達至應用程式，以作為 SAML 權杖中的 "NameID" 元素。|
|要傳送至應用程式的其他宣告|除了 [使用者識別碼 / NameID] 以外，其他宣告資訊通常會從 IDP 傳送至應用程式，例如名字、姓氏、電子郵件地址和使用者所屬的群組|在 AD FS 中，這會被視為信賴憑證者的其他宣告規則。|在 Azure AD 中，可在 Azure 入口網站內應用程式的 [單一登入屬性] 中 [使用者屬性] 標頭之下找到，按一下 [檢視] 並編輯其他所有使用者屬性。|| 

### <a name="representing-azure-ad-as-an-identity-provider-idp-in-a-saas-app"></a>在 SaaS 應用程式中將 Azure AD 當作識別提供者 (IdP)
在移轉過程中，必須將應用程式設定為指向 Azure AD (而不是內部部署識別提供者)。  本節主要著重於使用 SAML 通訊協定的 SaaS 應用程式，而不是自訂/LOB 應用程式。 不過，所述的概念會延伸到自訂/LOB 應用程式。 

大致上，有幾個重要項目可將 SaaS 應用程式指向 Azure AD
- 識別提供者簽發者：https&#58;//sts.windows.net/{tenant-id}/
- 識別提供者登入 URL：https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- 識別提供者登出 URL：https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- 同盟中繼資料位置：https&#58;//login.windows.net/{tenant-id} <tenant-id>/federationmetadata/2007-06/federationmetadata.xml?appid={<application-id} 

其中 {tenant-id} 會取代為您的租用戶識別碼 (在 Azure 入口網站中 Azure Active Directory -> [屬性] 之下找到的 [目錄識別碼])，而 {application-id} 會取代為您的應用程式識別碼 (在應用程式的 [屬性] 中找到的 [應用程式識別碼])

下表更詳細地說明在應用程式中進行 SSO 設定時所需的重要 IdP 設定元素，以及其在 AD FS 和 Azure AD 中的值或位置。  下表的參考架構是 SaaS 應用程式，而 SaaS 應用程式必須知道會將驗證要求傳送到哪裡，以及如何驗證所收到的權杖。

|設定元素|說明|AD FS|Azure AD|
|---|---|---|---|
|IdP </br>登入 </br>URL|以應用程式觀點看待的 IdP 登入 URL (使用者會被重新導向以便登入)。|AD FS 登入 URL 為 AD FS 同盟服務名稱後面加上 “/adfs/ls/”，例如：https&#58;//fs.contoso.com/adfs/ls/|Azure AD 的對應值會遵循此模式，其中 {tenant-id} 會取代為您的租用戶識別碼 (在 Azure 入口網站中 Azure Active Directory -> [屬性] 之下找到的 [目錄識別碼])。</br></br>若為使用 SAML-P 通訊協定的應用程式：https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2 </br></br>若為使用 WS-同盟通訊協定的應用程式：https&#58;//login.microsoftonline.com</br>/{tenant-id}/wsfed|
|IdP </br>登出 </br>URL|以應用程式觀點看待的 IdP 登出 URL (使用者選擇「登出」應用程式時會被重新導向)。|在 AD FS 中，登出 URL 與登入 URL 相同，或是附加 “wa=wsignout1.0” 的相同 url，例如 https&#58;//fs.contoso.com/adfs/ls /?wa=wsignout1.0|Azure AD 的對應值取決於應用程式是否能夠支援 SAML 2.0 登出。</br></br>如果應用程式支援 SAML 登出，此值會遵循此模式，其中 {tenant-id} 會取代為租用戶識別碼 (在 Azure 入口網站中 Azure Active Directory -> [屬性] 之下找到的 [目錄識別碼])。 https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2</br></br>如果應用程式不支援 SAML 登出：https&#58;//login.microsoftonline.com</br>/common /wsfederation?wa=wsignout1.0|
|token </br>簽署 </br>憑證|IDP 使用其私密金鑰來簽署已發行權杖的憑證。  驗證權杖是否來自應用程式設定要信任的相同 IDP。|在 AD FS 管理的 [憑證] 之下可找到 AD FS 權杖簽署憑證。|在 Azure AD 中，可在 Azure 入口網站內應用程式的 [單一登入屬性] 中 [SAML 簽署憑證] 標頭之下找到權杖簽署憑證，您可以在此處下載憑證以便上傳至應用程式。</br></br>  如果應用程式有多個憑證，則可在同盟中繼資料 xml 檔案中找到所有的憑證。|
|識別碼 / </br>「簽發者」|以應用程式觀點看待的 IdP 識別碼 (有時稱為「簽發者」或「簽發者識別碼」)</br></br>在 SAML 權杖中，此值會顯示為「簽發者」元素|AD FS 的識別碼通常是 AD FS 管理中 [服務] -> [編輯同盟服務屬性] 之下的同盟服務識別碼。  例如：http&#58;//fs.contoso.com/adfs/services/trust|Azure AD 的對應值會遵循此模式，其中 {tenant-id} 的值會取代為租用戶識別碼 (在 Azure 入口網站中 Azure Active Directory -> [屬性] 之下找到的 [目錄識別碼])。  https&#58;//sts.windows.net/{tenant-id}/|
|IdP </br>同盟 </br>中繼資料|IDP 的公開可用同盟中繼資料位置。  (有些應用程式會使用同盟中繼資料，作為系統管理員個別設定 URL、識別碼和權杖簽署憑證的替代方式)|在 AD FS 管理的 [服務] -> [端點] -> [中繼資料] -> [類型: 同盟中繼資料] 之下尋找 AD FS 同盟中繼資料 URL，例如：https&#58;//fs.contoso.com/ FederationMetadata/2007-06/</br>FederationMetadata.xml|Azure AD 的對應值會遵循此模式 https&#58;//login.microsoftonline.com</br>/{TenantDomainName}/FederationMetadata/2007-06/</br>FederationMetadata.xml，其中 {TenantDomainName} 的值會取代為您的租用戶名稱 (格式為 “contoso.onmicrosoft.com”) </br></br>有關 Azure AD 中同盟中繼資料的[詳細資訊](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)。

## <a name="migrating-saas-apps"></a>移轉 SaaS 應用程式
將 SaaS 應用程式從 AD FS 或其他識別提供者移轉至 Azure AD 目前是手動程序。 如需應用程式專屬的指引，請參閱[資源庫中有關整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)。

整合教學課程假設您正在進行綠地 (green field) 整合。  當您規劃、評估、設定和完全移轉您的應用程式時，您應了解一些有關移轉的重要概念。  
- 雖然有些應用程式可以輕鬆移轉，但是具有更複雜需求 (例如自訂宣告) 的應用程式則可能需要在 Azure AD 及/或 Azure AD Connect 中額外進行設定
- 在大多數的常見情況下，應用程式只需要 NameId 宣告和其他常見的使用者識別碼宣告。若要判斷是否需要任何其他宣告，請檢查您正從 AD FS 或第三方識別提供者發出哪些宣告
- 一旦您判斷出需要額外的宣告，則必須確定這些宣告可用於 Azure AD 中。  您必須檢查 Azure AD Connect 同步設定，以確保必要屬性 (例如 samAccountName) 正同步處理至 Azure AD
- 這些屬性一旦可用於 Azure AD 中，請在 Azure AD 中新增宣告發行規則，進而將這些屬性納入為已發行權杖中的宣告。  您可在 Azure AD 中應用程式的單一登入屬性中進行。

### <a name="assessing-what-can-be-migrated"></a>評估可移轉的項目
SAML 2.0 應用程式可以透過 Azure AD 應用程式資源庫來與 Azure AD 整合，或整合為非資源庫應用程式。  

有些設定需要在 Azure AD 中執行額外的步驟，而有些則是目前不支援的設定。  若要判斷可以移動的項目，請查看每個應用程式的目前設定，特別是下列各項：
- 已設定的宣告規則 (發行轉換規則)
- SAML NameID 格式和屬性
- 已發行的 SAML 權杖版本
- 其他設定，例如發行授權規則或存取控制原則以及多重要素驗證 (其他驗證) 規則

#### <a name="what-can-be-migrated-today"></a>目前可以移轉的項目
目前可輕鬆移轉的應用程式包括使用一組標準設定元素和宣告的 SAML 2.0 應用程式。  這些應用程式可以包含
- 使用者主體名稱
- 電子郵件地址
- 名字
- Surname
- SAML NameID 的替代屬性，包括 Azure AD 郵件屬性、郵件前置詞、employeeid、擴充屬性 1 – 15 或內部部署 SamAccountName (請參閱[編輯 NameIdentifier 宣告)](./develop/active-directory-saml-claims-customization.md)
- 自訂宣告 (如需支援的宣告對應相關資訊，請參閱[這裡](active-directory-claims-mapping.md)和[這裡](./develop/active-directory-saml-claims-customization.md)的文件)

除了自訂宣告和 nameID 元素以外，在移轉過程中需要在 Azure AD 中進行額外設定步驟的設定如下：
- AD FS 中的自訂授權或 MFA 規則 (使用 [Azure AD 條件式存取](active-directory-conditional-access-azure-portal.md)功能進行設定)
- 在 Azure AD 中可以使用 PowerShell 來設定具有多個 SAML 端點的應用程式 (無法在入口網站中使用這項功能)
- 您必須使用 PowerShell 來手動設定 WS-同盟應用程式，例如需要 SAML 1.1 版權杖的 SharePoint 應用程式

#### <a name="appsconfigurations-not-supported-in-azure-ad-today"></a>Azure AD 目前不支援的應用程式/設定
目前無法移轉需要下列功能的應用程式。  如果您有需要這些功能的應用程式，請在註解區段中提供意見反應。
- 通訊協定功能
    - 支援所有已登入應用程式的 SAML 單一登出 (SLO)
    - 支援 WS-Trust ActAs 模式
    - SAML 成品解析 
    - 已簽署 SAML 要求的簽章驗證 (請注意，已簽署的要求已被接受，但未驗證簽章)
 - 權杖功能 
     - SAML 權杖加密 
     - SAML 通訊協定回應內的 SAML 1.1 版權杖 
- 權杖功能中的宣告
    - 將內部部署群組名稱發行為宣告
    - 來自 Azure AD 以外存放區的宣告
    - 複雜宣告發行轉換規則 (如需支援的宣告對應相關資訊，請參閱這份[文件](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)和這份[文件](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization))
    - 將目錄擴充發行為宣告
    - NameID 格式的自訂規格
    - 多重值屬性的發行

>[!NOTE]
>Azure AD 一直不斷進化，以在這個領域中新增其他功能。 我們將定期更新本文件。 

### <a name="configuring-azure-ad"></a>設定 Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>為 SaaS 應用程式設定單一登入 (SSO) 設定

在 Azure AD 中，可在應用程式的單一登入屬性中 [使用者屬性] 之下，依照應用程式要求設定 SAML 登入，如下所示：

![](media/migrate-adfs-apps-to-azure/migrate3.png)
- 按一下 [檢視和編輯所有其他使用者屬性]，可查看安全性權杖中以宣告形式傳送的屬性

![](media/migrate-adfs-apps-to-azure/migrate4.png)
- 按一下特定屬性資料列進行編輯，或按一下 [新增屬性] 來新增屬性。 
![](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>將使用者指派給應用程式
若要讓您的使用者能夠在 Azure AD 中登入指定的 SaaS 應用程式，您必須賦予他們從 Azure AD 存取的權限。

若要在 Azure AD 入口網站中指派使用者，請瀏覽至入口網站中 SaaS 應用程式的畫面，然後按一下側邊欄中的 [使用者和群組]。 若要新增使用者或群組，請按一下畫面頂端的 [新增使用者]。 

![](media/migrate-adfs-apps-to-azure/migrate6.png) 

![](media/migrate-adfs-apps-to-azure/migrate7.png)若要驗證存取權，使用者在登入時應會在其[存取面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)中看到前述的 SaaS 應用程式 (可在 http://myapps.microsoft.com 中找到)。例如，使用者已成功獲得 Salesforce 和 ServiceNow 的存取權。

![](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configuring-the-saas-app"></a>設定 SaaS 應用程式
從內部部署同盟完全移轉至 Azure AD 的程序取決於您正在使用的 SaaS 應用程式是否支援多個識別提供者 (IdP)。  以下是有關多個 IdP 支援的常見問題：

   **問：應用程式支援多個 IdP 是什麼意思？**
    
   答：支援多個 IdP 的 SaaS 應用程式可讓您先輸入關於新 IdP (在我們的例子中為 Azure AD) 的所有資訊，再認可變更登入體驗。  完成設定之後，您可以將應用程式的驗證設定切換成指向 Azure AD。

   問：如果 SaaS 應用程式支援多個 IdP，有何影響？

   答：如果不支援多個 IdP，管理員在將 Azure AD 設定為應用程式的新 IdP 時，就必須有一小段服務中斷或檢修停機的時間。 在此中斷期間，使用者會獲知他們將無法登入其帳戶。

   如果應用程式支援多個 IdP，則可以事先進行其他 IdP 的設定，因此管理員只要在 Azure 完全移轉時切換 IdP。

   此外，如果應用程式支援多個 IdP，而且您選擇多個 IdP 來同時處理登入的驗證，則使用者可以選擇要在其登入頁面上驗證的 IdP。

#### <a name="example-multiple-idp-support"></a>範例 – 多重 IdP 支援
例如，在 Salesforce 中，可以在 [設定] -> [公司設定] -> [我的網域]-> [驗證設定] 中找到 IDP 設定。

![](media/migrate-adfs-apps-to-azure/migrate9.png)

由於先前在 [身分識別] -> [單一登入] 設定之下建立的設定，您應可將驗證設定中的 IdP 從 AD FS 變更為 Azure AD。 

![](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>選用：在 Azure AD 中設定使用者佈建
(選擇性) 如果您希望 Azure AD 直接處理指定之 SaaS 應用程式的使用者佈建，請參閱有關在 Azure AD 中為企業應用程式管理使用者帳戶佈建的文件。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Active Directory 管理應用程式](active-directory-enable-sso-scenario.md)
- [管理應用程式的存取權](active-directory-managing-access-to-apps.md)
- [Azure AD Connect 同盟](active-directory-aadconnectfed-whatis.md)