---
title: "從 Azure 存取控制服務移轉 |Microsoft 文件"
description: "移動應用程式和服務從 Azure 存取控制服務的選項"
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: f3de9016fe29a51ab2c7fb9e93fcd33af0f0e871
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="migrate-from-the-azure-access-control-service"></a>從 Azure 存取控制服務移轉

Azure 存取控制服務的 Azure Active Directory (Azure AD)，將在 11 月版 2018年淘汰。 應用程式和目前使用存取控制的服務必須完全移轉到那一個不同的驗證機制。 本文說明目前的客戶，建議您在規劃為已被取代的存取控制使用。 如果您目前並未使用存取控制，您不需要採取任何動作。


## <a name="overview"></a>概觀

存取控制是提供簡單的方式，來驗證和授權使用者存取您的 web 應用程式和服務的雲端驗證服務。 它可讓許多驗證和授權功能從您的程式碼中分解出來。 開發人員和 Microsoft.NET 用戶端、 ASP.NET web 應用程式和 Windows Communication Foundation (WCF) web 服務的架構設計人員主要使用存取控制。

存取控制的使用案例可以分成三個主要類別：

- 驗證特定的 Microsoft 雲端服務，包括 Azure 服務匯流排和 Dynamics CRM。 用戶端應用程式會取得權杖，向存取控制來驗證這些服務來執行各種動作。
- 加入驗證 web 應用程式、 自訂和的話 （例如 SharePoint)。 使用存取控制 「 被動 」 驗證，web 應用程式可以支援登入 Microsoft 帳戶 (先前稱為 Live ID)，與來自 Google、 Facebook、 Yahoo、 Azure AD 的帳戶與 Active Directory Federation Services (AD FS)。
- 設定自訂的 web 服務安全性與存取控制發出的權杖。 使用 「 作用中 」 的驗證，web 服務可以確保它們允許只對已知已通過驗證的用戶端使用存取控制的存取。

每一種使用情況和其建議的移轉策略會討論下列各節中。 

> [!WARNING]
> 在大部分情況下，若要將現有的應用程式和服務移轉至較新的技術需要重大的程式碼變更。 我們建議您立即開始規劃和執行您的移轉，可避免潛在的中斷或發生停機。

存取控制具有下列元件：

- 安全權杖服務 (STS)，並在接收驗證的要求，並傳回簽發安全性權杖。
- 在 Azure 傳統入口網站，其中您建立、 刪除和啟用及停用存取控制命名空間。
- 不同的存取控制管理入口網站中，您自訂及設定存取控制命名空間。
- 管理服務，可用來自動化入口網站的函式。
- 權杖轉換規則引擎，它可用來定義複雜的邏輯來操作輸出格式的存取控制發出的權杖。

若要使用這些元件，您必須建立一或多個 Access Control 命名空間。 A*命名空間*是與通訊的應用程式和服務的存取控制的專用執行個體。 命名空間會與所有其他存取控制客戶隔離。 其他存取控制客戶使用他們自己的命名空間。 在存取控制命名空間有專用的 URL，看起來像這樣：

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

與 STS 和管理作業的所有通訊都在這個 URL 已都完成。 您可以使用不同的路徑不同用途。 若要判斷您的應用程式或服務使用存取控制，監視的任何流量為 https://\<命名空間\>。 accesscontrol.windows.net。 任何對這個 URL 的流量由存取控制，而且必須停止。 

這個例外狀況是 https://accounts.accesscontrol.windows.net 任何流量。 這個 url 已由不同的服務，並不會受到存取控制已被取代。 

您應該也登入 Azure 傳統入口網站並檢查您所擁有的訂用帳戶中的任何存取控制命名空間。 存取控制命名空間會列在**存取控制命名空間**索引標籤，下方**Active Directory**服務。

如需存取控制的詳細資訊，請參閱[Access Control Service 2.0 （封存）](https://msdn.microsoft.com/library/hh147631.aspx)。

## <a name="retirement-schedule"></a>停用排程

自 11 月版 2017，所有存取控制元件都是完全受支援且可運作。 唯一的限制是，您[無法建立新的存取控制命名空間，透過在 Azure 傳統入口網站](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)。

以下是淘汰存取控制元件的排程：

- **2017 年 11 月**： 在 Azure 傳統入口網站體驗 Azure AD 管理員[淘汰](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/)。 此時，存取控制命名空間管理是在新的專用的 URL 可用： http://manage.windowsazure.com?restoreClassic=true。 若要檢視現有的命名空間、 啟用和停用命名空間，以及刪除命名空間，如果您選擇，使用此 URl。
- **年 4 月 2018年**： 存取控制命名空間管理已無法再使用在 http://manage.windowsazure.com?restoreClassic=true 專用的 URL。 此時，您無法停用或啟用、 刪除或列舉程式的存取控制命名空間。 不過，存取控制管理入口網站將會完全正常運作，且位於 https://\<命名空間\>。 accesscontrol.windows.net。 存取控制的所有其他元件會繼續正常運作。
- **11 月版 2018年**： 所有存取控制元件會永久關閉。 這包括存取控制管理入口網站、 管理服務、 STS 和權杖轉換規則引擎。 此時，任何要求傳送至存取控制 (位於\<命名空間\>。 accesscontrol.windows.net) 失敗。 您應該已移轉所有現有的應用程式和服務至其他技術也可以在此時間之前。


## <a name="migration-strategies"></a>移轉策略

下列章節說明從存取控制移轉至其他 Microsoft 技術高層級的建議。

### <a name="clients-of-microsoft-cloud-services"></a>Microsoft 雲端服務的用戶端

接受權杖由存取控制會立即發出每個 Microsoft 雲端服務支援至少一個替代形式的驗證。 正確的驗證機制會針對每個服務而有所不同。 我們建議您先參閱官方指引的每個服務的特定文件。 為了方便起見，以下提供每一組文件：

| 服務 | 指引 |
| ------- | -------- |
| Azure 服務匯流排 | [移轉至共用的存取簽章](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure 服務匯流排轉送 | [移轉至共用的存取簽章](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure 受管理快取 | [移轉至 Azure Redis 快取](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [移轉至認知服務 API](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk 服務 | [移轉至 Azure App Service 的 Logic Apps 功能](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure 媒體服務 | [移轉至 Azure AD 驗證](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure 備份 | [升級 Azure 備份代理程式](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-that-use-passive-authentication"></a>使用被動驗證 web 應用程式

使用存取控制進行使用者驗證的 web 應用程式，存取控制使用 web 應用程式開發人員和設計師提供的下列特性和功能：

- 與 Windows Identity Foundation (WIF) 深入整合。
- Google、 Facebook、 Yahoo、 Azure Active Directory 和 AD FS 帳戶與 Microsoft 帳戶的同盟。
- 下列的驗證通訊協定的支援： OAuth 2.0 草稿 13、 Ws-trust 和 Web 服務同盟 （WS-同盟）。
- 支援下列權杖格式： JSON Web Token (JWT)、 SAML 1.1、 SAML 2.0 和簡單 Web 權杖 (SWT)。
- 主領域探索體驗，整合至 WIF，可讓使用者選擇他們用來登入帳戶的類型。 這項體驗所裝載的 web 應用程式，而且可完全自訂。
- 語彙基元，可讓許多自訂向存取控制的 web 應用程式所接收的宣告轉換包括：
    - 傳遞從身分識別提供者的宣告。
    - 新增額外的自訂宣告。
    - 在某些情況下，簡單的 if-then 邏輯，來發出宣告。

不幸的是，沒有一項服務可提供所有這些對等的功能。 您應該評估哪些功能的存取控制，也選擇使用[Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/)， [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) 或另一個雲端驗證服務。

#### <a name="migrate-to-azure-active-directory"></a>移轉至 Azure Active Directory

要考慮的路徑直接與 Azure AD 整合您的應用程式和服務。 Azure AD 是以雲端為基礎的身分識別提供者的 microsoft 工作或學校帳戶。 Azure AD 為 Office 365、 Azure，以及執行更多的身分識別提供者。 它會提供類似同盟驗證功能來執行存取控制，但是不支援所有的存取控制功能。 

主要範例是與社交識別提供者的同盟，例如 Facebook、Google 和 Yahoo。 如果您的使用者登入認證這些類型，Azure AD 不是您的方案。 

Azure AD 也不一定支援完全相同驗證通訊協定做為存取控制。 例如，雖然存取控制與 Azure AD 支援 OAuth，是每個實作之間的些微差異。 不同的實作需要您修改程式碼做為移轉的一部分。

然而，Azure AD 存取控制的客戶提供數個潛在優點。 它原生支援 Microsoft 工作或學校帳戶裝載於雲端，常用的存取控制的客戶。 

Azure AD 租用戶也可以透過 AD FS 在內部部署 Active Directory 的一或多個執行個體為同盟。 如此一來，您的應用程式可以以雲端為基礎的使用者進行驗證的使用者，裝載於內部。 它也支援 WS-同盟通訊協定，這樣很容易就可以使用 WIF 與 web 應用程式整合。

下表比較與 web 應用程式與 Azure AD 中可用的功能相關的存取控制功能。 

在高層級， *Azure Active Directory 如果，則可能進行移轉，建議您讓使用者登入中只使用其 Microsoft 帳戶或學校帳戶*。

| 功能 | 存取控制支援 | Azure AD 支援 |
| ---------- | ----------- | ---------------- |
| **帳戶類型** | | |
| Microsoft 工作或學校帳戶 | 支援 | 支援 |
| 從 Windows Server Active Directory 和 AD FS 的帳戶 |支援透過同盟與 Azure AD 租用戶 <br />支援透過直接的同盟，使用 AD FS | 僅透過與 Azure AD 租用戶的同盟支援 | 
| 其他企業身分識別管理系統的帳戶 |透過同盟與 Azure AD 租用戶可能 <br />支援透過直接的同盟 | 可能透過與 Azure AD 租用戶的同盟 |
| 供個人使用的 Microsoft 帳戶 | 支援 | 支援透過 Azure AD v2.0 OAuth 通訊協定，但未透過任何其他通訊協定支援 | 
| Facebook、Google、Yahoo 帳戶 | 支援 | 不支援擔保 |
| **通訊協定與 SDK 相容性** | | |
| WIF | 支援 | 使用的支援，而有限的指示 |
| WS-同盟 | 支援 | 支援 |
| OAuth 2.0 | 支援 Draft 13 | 支援 RFC 6749，大多數最新型的規格 |
| Ws-trust | 支援 | 不支援 |
| **權杖格式** | | |
| JWT | 在搶鮮版 (Beta) 中支援 | 支援 |
| SAML 1.1 | 支援 | 預覽 |
| SAML 2.0 | 支援 | 支援 |
| SWT | 支援 | 不支援 |
| **自訂** | | |
| 可自訂的主領域探索/帳戶選擇 UI | 可下載的程式碼，可以納入應用程式 | 不支援 |
| 上傳自訂權杖簽署憑證 | 支援 | 支援 |
| 在權杖中自訂宣告 |-傳遞輸入宣告來自身分識別提供者<br />-取得存取權杖的身分識別提供者當做宣告<br />-發出根據輸入宣告值的輸出宣告<br />-發行輸出宣告具有常數值 |-無法通過同盟身分識別提供者的宣告<br />-無法取得存取權杖的身分識別提供者當做宣告<br />-不發出輸出宣告式的輸入宣告值<br />-可以與常數值發出輸出宣告<br />-可以發出同步至 Azure AD 使用者的內容為基礎的輸出宣告 |
| **自動化** | | |
| 將設定和管理工作自動化 | 支援透過存取控制管理服務 | 透過 Microsoft Graph 和 Azure AD Graph API 支援 |

如果您決定 Azure AD 是最佳的移轉路徑，您的應用程式和服務，您應該注意的兩種方式可與 Azure AD 整合您的應用程式。

若要使用 WS-同盟或 WIF 整合與 Azure AD，我們建議的方法中所述的下列[設定同盟單一登入非組件庫的應用程式](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)。 發行項是指設定 Azure AD SAML 型單一登入，但也適用於設定 WS-同盟。 遵循此方法需要 Azure AD Premium 授權。 這個方法有兩個優點：

- 您有 Azure AD 權杖自訂的完整彈性。 您可以自訂要比對的宣告，會由存取控制發出的 Azure AD 所發出的宣告。 特別是，這會包括使用者識別碼或名稱識別碼宣告。 若要繼續接收一致的使用者識別項，為您的使用者變更技術之後，請確定使用者識別碼所簽發的 Azure AD 的相符項目由存取控制所發出的。
- 您可以設定特定應用程式與您所控制的存留期的權杖簽署憑證。

<!--

Possible nameIdentifiers from Access Control (via AAD or AD FS):
- AD FS - Whatever AD FS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> 這個方法會要求 Azure AD Premium 授權。 如果您設定單一登入應用程式需要 premium 授權的存取控制客戶，請與我們連絡。 我們會很樂意與提供開發人員授權，您可以使用。

替代方式是依照[此程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)，提供設定 WS-同盟稍有不同的指示。 此程式碼範例不使用 WIF，而是使用 ASP.NET 4.5 OWIN 中介軟體。 不過，應用程式註冊的指示適用於使用 WIF，應用程式，且不需要 Azure AD Premium 授權。 

如果您選擇這種方法，您必須了解[在 Azure AD 中簽署金鑰變換](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)。 這個方法會使用 Azure AD 全域簽署金鑰來簽發權杖。 根據預設，WIF 不會自動重新整理簽署金鑰。 當 Azure AD 轉換其全域簽署金鑰時，您的 WIF 實作必須準備好接受變更。

如果您可以使用 OpenID Connect 或 OAuth 通訊協定透過 Azure AD 整合，建議進行。 我們有更詳盡的文件集和有關如何將 Azure AD 整合到 web 應用程式中提供的指導方針我們[Azure AD 開發人員指南](http://aka.ms/aaddev)。

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrate-to-azure-active-directory-b2c"></a>移轉至 Azure Active Directory B2C

要考量的其他移轉路徑是 Azure AD B2C。 Azure AD B2C 是雲端驗證服務，例如存取控制，可讓開發人員外包給雲端服務及其驗證和身分識別管理邏輯。 它是專為消費者導向應用程式設計可能高達數百萬個作用中的使用者付費的服務 （使用免費與付費層）。

存取控制，類似的 Azure AD B2C 最具吸引力功能之一是它支援許多不同類型的帳戶。 與 Azure AD B2C，您可以登入的使用者使用其 Microsoft 帳戶，或 Facebook、 Google、 LinkedIn、 GitHub 或 Yahoo 的帳戶等等。 Azure AD B2C 也支援 「 本機帳戶 」，或使用者名稱和密碼的使用者建立專為您的應用程式。 Azure AD B2C 還提供豐富的擴充性，您可以使用來自訂您的登入流程。 

不過，Azure AD B2C 不支援的驗證通訊協定的範圍，且權杖格式的客戶可能會需要的存取控制。 您也無法使用 Azure AD B2C 取得語彙基元和查詢如需有關使用者身分識別提供者，Microsoft 或其他方式。

下表比較使用 Azure AD B2C 中提供的 web 應用程式相關的存取控制 」 的功能。 在高階中，*如果您的應用程式是針對取用者，或者如果它支援不同類型的帳戶，則 Azure AD B2C 對於您的移轉可能是正確的選擇。*

| 功能 | 存取控制支援 | Azure AD B2C 支援 |
| ---------- | ----------- | ---------------- |
| **帳戶類型** | | |
| Microsoft 工作或學校帳戶 | 支援 | 透過自訂原則支援  |
| 從 Windows Server Active Directory 和 AD FS 的帳戶 | 透過直接的同盟，使用 AD FS 支援 | 支援透過 SAML 同盟，使用自訂原則 |
| 其他企業身分識別管理系統的帳戶 | 透過直接透過 WS-同盟的同盟支援 | 支援透過 SAML 同盟，使用自訂原則 |
| 供個人使用的 Microsoft 帳戶 | 支援 | 支援 | 
| Facebook、Google、Yahoo 帳戶 | 支援 | Facebook 和 Google 支援原生的 Yahoo 支援透過 OpenID Connect 的同盟，使用自訂原則 |
| **通訊協定與 SDK 相容性** | | |
| Windows Identity Foundation (WIF) | 支援 | 不支援 |
| WS-同盟 | 支援 | 不支援 |
| OAuth 2.0 | 支援 Draft 13 | 支援 RFC 6749，大多數最新型的規格 |
| Ws-trust | 支援 | 不支援 |
| **權杖格式** | | |
| JWT | 在搶鮮版 (Beta) 中支援 | 支援 |
| SAML 1.1 | 支援 | 不支援 |
| SAML 2.0 | 支援 | 不支援 |
| SWT | 支援 | 不支援 |
| **自訂** | | |
| 可自訂的主領域探索/帳戶選擇 UI | 可下載的程式碼，可以納入應用程式 | 透過自訂 CSS 可完全自訂的 UI |
| 上傳自訂權杖簽署憑證 | 支援 | 自訂簽署金鑰，不是憑證，支援透過自訂原則 |
| 在權杖中自訂宣告 |-傳遞輸入宣告來自身分識別提供者<br />-取得存取權杖的身分識別提供者當做宣告<br />-發出根據輸入宣告值的輸出宣告<br />-發行輸出宣告具有常數值 |-可以通過身分識別提供者; 的宣告所需的部分宣告的自訂原則<br />-無法取得存取權杖的身分識別提供者當做宣告<br />-可以發出自訂原則透過輸入宣告值為基礎的輸出宣告<br />-可以與常數值的自訂原則透過發出輸出宣告 |
| **自動化** | | |
| 將設定和管理工作自動化 | 支援透過存取控制管理服務 |-建立的使用者透過 Azure AD Graph API 允許<br />-無法以程式設計方式建立 B2C 租用戶、 應用程式或原則 |

如果您決定 Azure AD B2C 是最佳的移轉路徑，您的應用程式和服務，開始的下列資源：

- [Azure AD B2C 文件](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C 自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Azure AD B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="migrate-to-ping-identity-or-auth0"></a>移轉至 Ping 身分識別或 Auth0

在某些情況下，您可能會發現 Azure AD 與 Azure AD B2C 不足取代 web 應用程式中的存取控制，而不進行重大的程式碼變更。 一些常見的範例可能包括：

- 使用 WIF 或 WS-同盟使用社交身分識別提供者，例如 Google 或 Facebook 登入的 web 應用程式。
- Web 應用程式執行直接到企業聯盟識別提供者透過 WS-同盟通訊協定。
- Web 應用程式需要社交身分識別提供者 （例如 Google 或 Facebook） 所發出存取權杖由存取控制發出的權杖中宣告的形式。
- 使用複雜的權杖轉換規則，Azure AD 或 Azure AD B2C 無法重現的 web 應用程式。
- 集中管理許多不同的身分識別提供者的同盟，使用 ACS 的多租用戶 web 應用程式

在這些情況下，您可能要考慮您 web 應用程式移轉至另一個雲端驗證服務。 我們建議您瀏覽下列選項。 下列選項中的每個提供的功能類似於存取控制：



|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs)是已建立彈性的雲端身分識別服務[客戶的存取控制的高層級的移轉指引](https://auth0.com/acs)，並支援 ACS 沒有幾乎每項功能。 |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Ping 識別](https://www.pingidentity.com)提供類似於 ACS 的兩個解決方案。 PingOne 支援許多 ACS，與相同的功能的雲端身分識別服務，而 PingFederate 類似的內部識別產品可提供更大的彈性。 請參閱[Ping 的 ACS 淘汰指引](https://www.pingidentity.com/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html)如需有關使用這些產品。  |

在使用 Ping 身分識別和 Auth0 我們目標是確保所有存取控制客戶都有其應用程式和服務的移轉路徑，移動向存取控制所需的數量降到最低。

<!--

## Sharepoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>使用中的驗證的 web 服務

為受到存取控制發出的權杖的 web 服務，存取控制會提供下列功能和功能：

- 能夠註冊一或多個*服務身分識別*存取控制命名空間中。 服務身分識別可以用於要求權杖。
- 支援 OAuth WRAP 和 OAuth 2.0 草稿 13 通訊協定要求語彙基元，使用下列類型的認證：
    - 建立服務身分識別的簡單密碼
    - 帶正負號的 SWT 使用對稱金鑰或 X509 憑證
    - 受信任的身分識別提供者 （一般而言，AD FS 執行個體） 所發出的 SAML 權杖
- 支援下列權杖格式： JWT、 SAML 1.1、 SAML 2.0 和 SWT。
- 簡單的權杖轉換規則。

存取控制服務身分識別通常用於實作伺服器對伺服器驗證。  

#### <a name="migrate-to-azure-active-directory"></a>移轉至 Azure Active Directory

這種類型的驗證流程的建議是移轉至[Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/)。 Azure AD 是以雲端為基礎的身分識別提供者的 microsoft 工作或學校帳戶。 Azure AD 為 Office 365、 Azure，以及執行更多的身分識別提供者。 

您也可以使用的伺服器對伺服器驗證的 Azure AD 使用 OAuth 用戶端認證授與的 Azure AD 實作。 下表比較在 Azure AD 中可用的伺服器對伺服器驗證的存取控制能力。

| 功能 | 存取控制支援 | Azure AD 支援 |
| ---------- | ----------- | ---------------- |
| 如何註冊 Web 服務 | 在 存取控制管理入口網站中建立信賴憑證者的合作對象 | 在 Azure 入口網站中建立的 Azure AD web 應用程式 |
| 如何註冊用戶端 | 存取控制管理入口網站中建立的服務身分識別 | 在 Azure 入口網站中建立另一個 Azure AD web 應用程式 |
| 使用的通訊協定 |-OAuth WRAP 通訊協定<br />-OAuth 2.0 草稿 13 用戶端認證授與 | OAuth 2.0 用戶端認證授與 |
| 用戶端驗證方法 |-簡單密碼<br />-已簽署的 SWT<br />-從同盟識別身分提供者 SAML 權杖 |-簡單密碼<br />-已簽署 JWT |
| 權杖格式 |-JWT<br />-SAML 1.1<br />-SAML 2.0<br />-SWT<br /> | 僅限 JWT |
| 權杖轉換 |新增自訂宣告<br />-簡單的 if-then 宣告發行邏輯 | 新增自訂宣告 | 
| 將設定和管理工作自動化 | 支援透過存取控制管理服務 | 透過 Microsoft Graph 和 Azure AD Graph API 支援 |

如需有關實作伺服器對伺服器案例的指引，請參閱下列資源：

- Service to Service 區段[Azure AD 開發人員指南](https://aka.ms/aaddev)
- [使用簡單密碼的用戶端認證協助程式程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [使用憑證的用戶端認證協助程式程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>移轉至 Ping 身分識別或 Auth0

在某些情況下，您可能會發現 Azure AD 用戶端認證和 OAuth 授與實作不足以取代您不需要主要程式碼變更的架構中的存取控制。 一些常見的範例可能包括：

- 使用非 Jwt 權杖格式的伺服器對伺服器驗證。
- 使用外部識別提供者所提供的輸入的權杖的伺服器對伺服器驗證。
- 使用 Azure AD 無法重現的權杖轉換規則的伺服器對伺服器驗證。

在這些情況下，您可能會考慮您 web 應用程式移轉至另一個雲端驗證服務。 我們建議您瀏覽下列選項。 下列選項中的每個提供的功能類似於存取控制：

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs)是已建立彈性的雲端身分識別服務[客戶的存取控制的高層級的移轉指引](https://auth0.com/acs)，並支援 ACS 沒有幾乎每項功能。 |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Ping 識別](https://www.pingidentity.com)提供類似於 ACS 的兩個解決方案。 PingOne 支援許多 ACS，與相同的功能的雲端身分識別服務，而 PingFederate 類似的內部識別產品可提供更大的彈性。 請參閱[Ping 的 ACS 淘汰指引](https://www.pingidentity.com/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html)如需有關使用這些產品。  |

在使用 Ping 身分識別和 Auth0 我們目標是確保所有存取控制客戶都有其應用程式和服務的移轉路徑，移動向存取控制所需的數量降到最低。

## <a name="questions-concerns-and-feedback"></a>問題、 考量，與意見反應

我們了解許多存取控制客戶不會清除的移轉路徑找出閱讀本文之後。 您可能需要一些協助或判斷正確的計畫指南。 如果您想要討論您的移轉案例和問題，請在此頁面上保留註解。
