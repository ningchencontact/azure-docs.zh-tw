---
title: 四個步驟來搭配 Azure Active Directory 的增強式身分識別基礎
description: 本主題說明混合式身分識別的客戶可以用來建立增強式身分識別基礎的四個步驟。
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96b5e8ab63c1784ff073c7ba38cd4a6319db43c5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452731"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>四個步驟來搭配 Azure Active Directory 的增強式身分識別基礎

管理應用程式和資料的存取權可以不再依賴傳統網路安全性界限策略，例如周邊網路和防火牆因為快速移動至雲端的應用程式。 現在組織必須信任其身分識別解決方案，來控制人員以及何者得到存取組織的應用程式和資料。 更多的組織允許員工以自攜裝置工作，並從他們可以連線到網際網路的任何地方使用自己的裝置。 確保這些裝置的合規性及安全性已成為組織選擇實作的身分識別解決方案中的重要考量。 在現今的數位工作地點[身分識別是主要的控制層](https://www.microsoft.com/security/technology/identity-access-management?rtc=1)的任何組織要移至雲端。

採用 Azure Active Directory (Azure AD) 混合式身分識別解決方案，組織會獲得解除鎖定透過自動化、 委派、 自助式和單一登入功能的產能的進階功能的存取權。 它可讓您從完成其工作，同時允許您的 IT 團隊，以確保適當人員，有適當的資源，以建立安全的產能的權限存取管理該存取權所需的任何地方存取公司資源的背景工作角色。

根據我們所獲得的經驗，這份檢查清單的最佳作法將協助您快速部署建議的動作，來建置*強式*貴組織中的 identity foundation:

* 輕鬆地連線到應用程式
* 自動建立一個身分識別的每位使用者
* 安全地讓您的使用者
* 操作您的深入解析

## <a name="step-1---connect-to-apps-easily"></a>步驟 1-輕鬆地連線到應用程式

連接您的應用程式與 Azure AD，您可以藉由啟用單一登入 (SSO) 提升使用者生產力和安全性，並執行使用者佈建。 藉由管理您的應用程式，在單一位置，Azure AD 中，您可以將系統管理額外負荷降到最低，並達到安全性與合規性原則的單一控制點。

本章節涵蓋您的選項，來管理使用者存取應用程式，啟用安全的遠端存取內部應用程式，並將您的應用程式移轉至 Azure AD 的優點。

### <a name="make-apps-available-to-your-users-seamlessly"></a>將應用程式提供給您的使用者順暢地

Azure AD 可讓系統管理員[新增的應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)中的企業應用程式資源庫來[Azure 入口網站](https://portal.azure.com/)。 新增企業應用程式資源庫應用程式，讓您更輕鬆地設定為使用 Azure AD 作為身分識別提供者的應用程式。 它也可讓您管理使用者存取應用程式條件式存取原則和，讓使用者不需要重複輸入其密碼，並會自動登入這兩個內部部署應用程式設定單一登入 (SSO) 和雲端式應用程式。

一旦應用程式新增至 Azure AD 資源庫時，使用者可以查看指派給他們並搜尋，然後視需要來要求其他應用程式的應用程式。 Azure AD 提供[幾種方法](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)供使用者存取他們的應用程式：

* 存取面板 / 我的應用程式
* Office 365 應用程式啟動器
* 直接登入同盟應用程式
* 直接登入連結

若要深入了解應用程式的使用者存取，請參閱**步驟 3 – 讓您的使用者**這篇文章中。

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>從 Active Directory 同盟服務的應用程式移轉至 Azure AD

移轉單一登入組態從 Active Directory Federation Services (ADFS) 至 Azure AD 可讓安全性、 更一致的管理能力，以及共同作業的其他功能。 為獲得最佳結果，建議您從 AD FS 移轉您的應用程式，Azure ad。 將您的應用程式驗證和授權帶到 Azure AD 可以提供下列優點：

* 管理成本
* 管理風險
* 提高產能
* 定址的合規性和控管

若要進一步了解，請參閱[將您應用程式移轉至 Azure Active Directory](https://aka.ms/migrateapps/whitepaper) （英文） 白皮書。

### <a name="enable-secure-remote-access-to-apps"></a>啟用應用程式的安全遠端存取

[Azure AD 應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)提供簡易的解決方案，組織可以將內部部署應用程式發佈至雲端以遠端使用者需要存取內部應用程式以安全的方式。 單一登入之後將 Azure AD，使用者可以透過外部 Url 或內部應用程式入口網站存取雲端和內部部署應用程式。

Azure AD 應用程式 Proxy 提供下列優點：

* 將 Azure AD 擴充至內部部署資源
  * 雲端級別安全性和保護
  * 條件式存取以及可輕鬆地啟用的 Multi-factor Authentication 等功能
* VPN 等傳統的反向 proxy 解決方案的周邊網路中沒有任何元件
* 沒有所需的輸入連線
* 單一登入 (SSO) 跨裝置、 資源和雲端和內部部署中的應用程式
* 提高產能隨時隨地與任何位置的使用者

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>探索影子 IT 使用 Microsoft Cloud App Security

在現代企業中，IT 部門並通常不知道的所有雲端應用程式使用者用來執行其工作。 當 IT 系統管理員會詢問幾個雲端應用程式他們認為其員工使用，平均它們假設 30 或 40。 事實上，平均值是超過 1,000 個不同的應用程式正在您的組織中員工所使用。 80%的員工使用非獲批准的應用程式沒有人已檢閱，並可能不符合安全性與合規性原則。

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) 可協助您識別與使用者受歡迎的實用應用程式，就可能批准，並新增至 企業應用程式庫，讓使用者可獲得功能，例如 SSO 和條件式存取。

*"* * Cloud App Security** 有助於確保我們的人員適當地使用我們的雲端和 SaaS 應用程式，以支援基本的安全性原則，協助您保護 Accenture 的方式。 」*--- [John Blasi，管理總監資訊安全性 Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

除了偵測影子 IT，MCAS 也可以判斷風險層級的應用程式，防止未經授權的存取公司資料、 可能發生資料外洩和其他安全性風險的應用程式中。

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>步驟 2-自動建立一個身分識別的每位使用者

結合使用內部部署和雲端目錄中的 Azure AD 混合式身分識別解決方案，可讓您藉由佈建您現有的身分識別，在雲端中重複使用現有的內部部署 Active Directory 投資。 解決方案會同步處理內部部署身分識別與 Azure AD，同時 IT 會保留做為主要身分識別的真實來源執行任何現有的管理解決方案與內部部署 Active Directory。 Microsoft 的 Azure AD 混合式身分識別解決方案跨越內部部署和雲端架構的功能，建立一般的使用者識別進行驗證和授權，不論其位置的所有資源。

整合您的內部部署目錄與 Azure AD 讓使用者更具生產力防止使用者在應用程式和服務之間使用多個帳戶，提供常見的身分識別來存取這兩個雲端和內部部署資源。 使用多個帳戶是使用者的痛苦點和類似的 IT。 從使用者的觀點而言，擁有多個帳戶表示需要記住多組密碼。 若要避免這個問題，許多使用者重複使用相同的密碼，針對每個帳戶，也就是安全性的觀點而言不正確。 IT 的觀點而言，重複使用通常會導致多個密碼重設和技術服務人員成本，以及使用者抱怨。

Azure AD Connect 是用於同步處理您的內部部署身分識別給 Azure AD，然後可以用來存取雲端應用程式的工具。 在 Azure AD 中 身分識別之後，它們可以佈建到 SaaS 應用程式，例如 Salesforce 或 Concur。

在本節中，我們列出了提供高可用性，新式驗證的雲端，並減少您的內部部署使用量的建議。

> [!NOTE]
> 如果您想要深入了解 Azure AD Connect，請參閱[什麼是 Azure AD Connect 同步處理？](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>設定 Azure AD Connect 的預備伺服器，並維持最新狀態

Azure AD Connect 會在佈建程序中扮演重要的角色。 如果因為任何原因離線同步處理伺服器，在內部部署環境的變更不會在雲端中更新，並會造成使用者的存取問題。 請務必定義可讓系統管理員之後同步處理伺服器離線，快速地繼續同步處理的容錯移轉策略。

若要提供高可用性，萬一您主要的 Azure AD Connect 伺服器離線，建議您部署個別[預備伺服器](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server)Azure AD connect。 部署伺服器，可讓系統管理員 「 升級 」 預備到生產環境伺服器的簡單的組態參數。 具有設定為預備模式的待命伺服器也可讓您測試及部署新的組態變更，並引進新的伺服器，如果解除委任舊。

> [!TIP]
> Azure AD Connect 會定期更新。 因此，強烈建議您保留預備伺服器目前若要利用效能改進、 bug 修正，以及每個新版本所提供的新功能。

### <a name="enable-cloud-authentication"></a>啟用雲端驗證

與內部部署 Active Directory 的組織應該將其目錄擴充至 Azure AD 中使用 Azure AD Connect，並設定適當的驗證方法。 [選擇正確的驗證方法](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)組織為您的應用程式移至雲端的旅程的第一個步驟。 因為它會控制所有雲端資料和資源的存取，它就會是重要的元件。

啟用 Azure AD 中的雲端驗證內部部署目錄物件的最簡單且建議方法是能夠[密碼雜湊同步處理](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)(PHS)。 或者，有些組織可能會考慮啟用[傳遞驗證](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start)(PTA)。

不論您選擇 PHS 或 PTA，不要忘記啟用[無縫單一登入](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)允許存取雲端應用程式，而不需要持續時，輸入使用者名稱和密碼的應用程式中使用 Windows 7 和 8 的裝置上您公司的使用者網路。 沒有單一登入，使用者必須記住應用程式專用的密碼並登入到每個應用程式。 同樣地，IT 人員必須建立並更新每個應用程式，例如 Office 365、 Box 及 Salesforce 的使用者帳戶。 使用者必須記住其密碼，再加上花費的時間，來登入每個應用程式。 提供標準化單一登入機制，對整個企業至關重要的最佳使用者體驗、 降低風險、 能夠報告和控管。

已使用 AD FS 或其他內部部署驗證提供者的組織，移至 Azure AD 作為身分識別提供者可以降低複雜度並提高可用性。 除非您有特定使用案例，使用同盟，我們建議從同盟驗證移轉 PHS 和無縫 SSO 或 PTA 並無縫 SSO 才能享有內部降低的使用量和彈性的雲端提供的優點改善的使用者體驗。 如需詳細資訊，請參閱 <<c0> [ 從同盟移轉至 Azure Active directory 密碼雜湊同步處理](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)。

### <a name="enable-automatic-deprovisioning-of-accounts"></a>啟用自動解除佈建帳戶

啟用自動化佈建和解除佈建您的應用程式是跨多個系統管理的身分識別生命週期的最佳策略。 Azure AD 支援[自動化、 原則型佈建和解除佈建](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal)例如 ServiceNow 和 Salesforce 等熱門實作的 SaaS 應用程式的各種不同的使用者帳戶的[SCIM 2.0通訊協定](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)。 不同於傳統的佈建解決方案，需要自訂程式碼或手動上傳的 CSV 檔，佈建服務裝載在雲端中，而且功能預先整合可以設定並使用 Azure 入口網站管理的連接器。 自動解除佈建的主要優點是它可協助保護您的組織，立即於使用者離開組織時，從主要 SaaS 應用程式移除使用者的身分識別。

如需深入了解自動化使用者佈建，請參閱 [自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

## <a name="step-3---empower-your-users-securely"></a>步驟 3-安全地讓您的使用者

在現今的數位工作地點，務必平衡安全性與生產力。 不過，終端使用者通常推回上降低其產能和存取雲端應用程式的安全性措施。 為了協助解決此問題，Azure AD 會提供自助式功能，可讓使用者保持生產力，同時將 系統管理額外負荷降至最低。

此區段會列出建議移除您的組織中的摩擦，藉由讓使用者能夠同時保有警覺。

### <a name="enable-self-service-password-reset-for-all-users"></a>所有使用者都啟用自助式密碼重設

Azure 的[自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)(SSPR) 提供簡單的方法，讓 IT 系統管理員允許使用者重設，以及解除鎖定其密碼或帳戶不需要系統管理員介入。 系統包含詳細的報告，可追蹤使用者何時存取系統與通知來警示您誤用或濫用。

根據預設，Azure AD 可以在執行密碼重設時解除鎖定帳戶。 不過，當您啟用 Azure AD Connect[整合內部](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)，您也可以選擇將這兩項作業，可讓使用者解除鎖定其帳戶，而不必重設密碼。

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>請確定所有的使用者已註冊的 MFA 和 SSPR

Azure 提供可供您和貴組織以確保使用者已註冊的 MFA 和 SSPR 的報告。 尚未註冊的使用者可能需要告知的處理程序。

MFA[登入報告](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting)包含 MFA 使用量的相關資訊，並可讓您深入了解 MFA 在您的組織中的運作方式。 具有存取權登入活動 （和稽核和風險事件） 的 Azure AD 是重要的疑難排解、 使用情況分析和鑑識調查。

同樣地，[自助式密碼管理報告](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)可用以判斷誰具有 （或尚未） 完成 SSPR 註冊。

### <a name="self-service-app-management"></a>自助應用程式管理

您的使用者可以自行探索應用程式從存取面板之前，您必須啟用[自助式應用程式存取](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access)您想要允許使用者自行探索，並要求的任何應用程式存取權。 自助應用程式存取是適合用來允許使用者自行探索應用程式，並可選擇讓商務群組核准這些應用程式的存取。 您可以讓商務群組來管理指派給這些使用者的認證[密碼單一登入應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-the-application-for-password-single-sign-on)直接從他們的存取面板。

### <a name="self-service-group-management"></a>自助式群組管理

將使用者指派給應用程式最適合對應時使用群組，因為它們允許更多的彈性和大規模管理功能：

* 以屬性為基礎來使用動態群組成員資格
* 委派給應用程式擁有者

Azure AD 可讓您使用安全性群組和 Office 365 群組來管理資源的存取權。 這些群組可以被管理群組擁有者可以核准或拒絕成員資格要求並委派控制權的群組成員資格。 又稱為[自助式群組管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)，這項功能會節省時間讓群組擁有者未獲指派系統管理角色，才能建立和管理群組，而不必依賴系統管理員來處理其要求。

## <a name="step-4---operationalize-your-insights"></a>步驟 4-實作您的深入解析

稽核與安全性相關事件及相關的警示的記錄是策略的不可或缺的元件有效，以確保使用者保有生產力，而且您的組織安全。 安全性記錄和報告可協助回答問題這類：

* 使用您付費嗎？
* 是否有任何可疑或惡意租用戶中發生的情況？
* 誰被影響在安全性事件？

安全性記錄和報告會提供可疑活動的電子記錄，並協助您偵測可能指出從外部嘗試或成功滲透網路以及內部攻擊的模式。 您可以使用稽核來監視使用者活動、 文件法規合規性、 執行鑑識分析等等。 警示會提供安全性事件通知。

### <a name="assign-least-privileged-admin-roles-for-operations"></a>指派作業的最小權限系統管理員角色

當您思考您作業的方式，有幾個層級的管理考量。 第一層會置於您的全域系統管理員的管理負擔。 一律使用全域管理員角色，可能適合小型公司。 但支援服務中心人員與系統管理員負責特定工作的較大型組織，全域系統管理員的角色指派可能會有安全性風險因為它提供的個人能夠管理後話的工作什麼它們應該能執行。

在此情況下，您應該考慮系統管理上一層的樓。 使用 Azure AD，您可以指定為 「 有限 」 管理系統管理員可以在較低權限角色中的工作的使用者。 比方說，您可能會指派您的協助支援人員[安全性讀取者](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader)角色提供管理具有唯讀存取權的安全性相關功能的能力。 或可能是合理的指派[驗證系統管理員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator)的個人，讓他們能夠重設非密碼認證或讀取及設定 Azure 服務健康狀態的角色。

若要進一步了解，請參閱[Azure Active Directory 中的系統管理員角色權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>監視混合式元件 （Azure AD Connect 同步處理，AD FS） 使用 Azure AD Connect Health

Azure AD Connect 與 AD FS 是可可能中斷生命週期管理和驗證，並最終可能導致中斷的重要元件。 因此，您應該部署 Azure AD Connect Health 進行監視和報告這些元件。

若要深入了解，請閱讀[使用 Azure AD Connect Health 監視 AD FS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)。

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>使用 Azure 監視器來收集分析資料記錄檔

[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)是統一監視入口網站，為所有的 Azure AD 記錄檔，它提供進階分析和智慧機器學習服務的深入解析。 使用 Azure 監視器中，您可以使用度量和記錄檔在入口網站中，並透過 Api 來取得更多的可見性的狀態與資源的效能。 它可讓單一窗格，在入口網站中的半透明效果體驗，同時啟用各種不同的產品整合，透過支援傳統的第三方 SIEM 系統中的 Api 和資料匯出選項。 Azure 監視器也可讓您設定警示規則時收到通知，或採取自動化的動作，影響資源的問題。

![Azure 監視器](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>建立您的領導和您日的自訂儀表板

沒有 SIEM 解決方案的組織可下載[Power BI 內容套件](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack)適用於 Azure AD。 Power BI 內容套件包含預先建置的報告，可協助您了解您的使用者採用及使用 Azure AD 功能，可讓您深入探索您的目錄中的所有活動的方式。 您也可以建立您自己[自訂儀表板](https://docs.microsoft.com/power-bi/service-dashboards)並與您的日常活動所回報的領導小組共用。 儀表板是適合用來監視您的業務，並查看所有您最重要計量一目了然。 在儀表板的視覺效果可能來自從一個基礎資料集或數量，以及一份基礎報表或多個。 儀表板將內部部署和雲端資料，提供的合併的檢視，不論資料位於何處。

![Power BI 自訂儀表板](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>了解您的支援呼叫驅動程式

當您實作這篇文章中所述的混合式身分識別解決方案時，您應該最後會注意到減少支援電話中。 常見的問題例如遺忘的密碼和帳戶鎖定會降低藉由實作 Azure 的自助式密碼重設，同時啟用自助式應用程式存取可讓使用者自行探索，並要求存取應用程式，而不需要在您的 IT 人員。

如果您沒有觀察減少支援來電，我們建議您確認是否 SSPR 或自助式應用程式存取已設定正確的試圖分析您的支援呼叫驅動程式，或者如果有任何其他新的問題可以有系統地解決。

*「 在我們的數位轉型旅程圖中，我們需要可靠的身分識別和存取管理提供者，以協助我們、 夥伴和雲端服務提供者，為有效的生態系統; 之間的無縫但安全的整合Azure AD 是最好的選擇，提供我們所需的功能和我們能夠偵測及回應風險的可見度。 」* --- [Yazan Almasri，通用的資訊安全經理 Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>監視您的應用程式來推動深入解析的使用量

除了探索影子 IT，跨組織中使用監視應用程式使用量[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)可以協助您的組織，當您移至充分利用雲端應用程式所保證。 它可協助讓您控制您的資產透過改良的活動可見度，並增加跨雲端應用程式的重要資料的保護。 監視使用 MCAS 貴組織中的應用程式使用量，可協助您回答下列問題：

* 員工使用哪些待批准的應用程式來儲存資料？
* 位置和時間的敏感性資料儲存在雲端中？
* 誰正在存取雲端中的敏感性資料？

*「 與 Cloud App Security，我們可以快速找出異常狀況並採取動作。 」* --- [Eric LePenske 資深管理員 中，資訊安全性 Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>總結

若要實作混合式身分識別解決方案的許多層面，但這四個步驟檢查清單可協助您快速完成，可讓使用者更具生產力且更安全的身分識別基礎結構。

* 輕鬆地連線到應用程式
* 自動建立一個身分識別的每位使用者
* 安全地讓您的使用者
* 操作您的深入解析

我們希望這份文件會很有用的藍圖，以建立強式身分識別基礎，讓您的組織。

## <a name="identity-checklist"></a>身分識別檢查清單

我們建議您列印參考的下列檢查清單，當您在組織中開始您邁向更穩固的身分識別基礎。

### <a name="today"></a>今天

|完成了嗎？|Item|
|:-|:-|
||試驗自我服務密碼重設 (SSPR) 群組|
||使用 Azure AD Connect Health 監視混合式元件|
||指派作業的最小權限系統管理員角色|
||探索影子 IT 使用 Microsoft Cloud App Security|
||使用 Azure 監視器來收集資料進行分析的記錄檔|

### <a name="next-two-weeks"></a>接下來的兩週

|完成了嗎？|Item|
|:-|:-|
||為您的使用者提供應用程式|
||試驗選擇的 SaaS 應用程式的 Azure AD 佈建|
||Azure AD connect 安裝暫存伺服器，並將它保持在最新狀態|
||開始從 ADFS 的應用程式移轉至 Azure AD|
||建立您的領導和您日的自訂儀表板|

### <a name="next-month"></a>下個月

|完成了嗎？|Item|
|:-|:-|
||監視您的應用程式來推動深入解析的使用量|
||試驗應用程式的安全遠端存取|
||請確定所有的使用者已註冊的 MFA 和 SSPR|
||啟用雲端驗證|

### <a name="next-three-months"></a>接下來的三個月

|完成了嗎？|Item|
|:-|:-|
||啟用自助應用程式管理|
||啟用自助式群組管理|
||監視您的應用程式來推動深入解析的使用量|
||了解您的支援呼叫驅動程式|

## <a name="next-steps"></a>後續步驟

了解如何增加您使用的功能的安全狀態的 Azure Active Directory 與這五步驟檢查清單-[保護您的身分識別基礎結構的五個步驟](https://aka.ms/securitysteps)。

了解在 Azure AD 中的身分識別功能如何協助您加速轉換至雲端所提供的解決方案和功能，可讓組織得以快速採用，並從傳統移動多個其身分識別管理的控管的管理在內部部署至 Azure AD-系統[如何在 Azure AD 提供雲端控管管理內部部署工作負載](https://aka.ms/cloudgoverned)。
