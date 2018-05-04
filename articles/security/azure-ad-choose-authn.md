---
title: 針對 Azure AD 混合式身分識別解決方案選擇正確的驗證方法 | Microsoft Docs
description: 本指南旨在協助中大型組織內的 CEO、CIO、CISO、首席身分識別架構設計人員、企業架構設計人員，以及安全性和 IT 決策者負責選擇 Azure AD 混合式身分識別解決方案。
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 102fa06be3734fa6993616f752922433ee0dee7f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="choosing-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>針對 Azure Active Directory 混合式身分識別解決方案選擇正確的驗證方法 

本文是一系列文章中的第一篇，可協助組織實作完整的 Azure AD 混合式身分識別解決方案。 已將完整的 Azure AD 混合式身分識別解決方案概述為混合式身分識別數位轉換架構並涵蓋商務結果，而目標是讓組織應該專注於確保他們已實作強固且安全的混合式身分識別解決方案。 架構的第一個商務結果會列出組織的需求，以便在使用者存取雲端應用程式時保護驗證程序。 驗證安全商務結果的第一個商務目標是，讓使用者能夠使用其內部部署使用者名稱和密碼登入雲端應用程式。 這個登入程序和使用者驗證方式，讓一切都可在雲端中達成。

選擇正確的驗證方法是組織想要將自己的應用程式移至雲端的第一個考量。 基於下列因素，不應輕易做出此決策：

1. 它是組織想要移至雲端的第一項決策。 

2. 驗證方法是組織存在雲端的重要元件，可控制對所有雲端資料和資源的存取。

3. 它是 Azure AD 中所有其他進階安全性和使用者體驗功能的基礎。

4. 實作之後就很難變更驗證方法。

利用身分識別作為 IT 安全性的新控制平面，驗證就能在組織存取新的雲端世界時提供防護。 組織應該確定身分識別控制平面可使其安全性變得更強大，並保護其雲端應用程式的安全以防止入侵者。

### <a name="out-of-scope"></a>超出範圍

目前不含內部部署目錄使用量的組織不在本文的適用範圍內。 這類企業通常只需在雲端建立身分識別，不需要混合式身分識別解決方案。 僅限雲端的身分識別只存在雲端，不會與對應的內部部署身分識別相關聯。  

## <a name="choosing-the-right-authentication-method"></a>選擇正確的驗證方法

利用 Azure AD 混合式身分識別解決方案作為新的控制平面，驗證就能成為雲端存取的基礎。 選擇正確的驗證方法是設定 Azure AD 混合式身分識別解決方案的第一項重要決策。 實作驗證方法是使用也會在雲端佈建使用者的 Azure AD Connect 所設定的。 

若要選擇驗證方法，需要考慮實作您選取項目的時間、現有基礎結構、複雜度及成本。 這些因素對每個組織而言不同，並可能隨時間改變。 

Azure AD 針對混合式身分識別解決方案支援下列驗證方法：

### <a name="cloud-authentication"></a>雲端驗證
當您選擇此驗證方法時，Azure AD 會處理使用者的登入程序。 搭配無縫單一登入 (SSO)，使用者不必重新輸入認證，就能登入雲端應用程式。 使用雲端驗證，您有兩個選項可供選擇： 

**密碼雜湊同步處理 (PHS)**：在 Azure AD 中啟用內部部署目錄物件驗證的最簡單方式。 密碼雜湊同步處理可讓使用者使用其在內部部署中所使用的相同使用者名稱和密碼，而不需部署任何其他基礎結構。 無論選取了何種驗證方法，Azure AD 的某些進階功能 (例如 Identity Protection) 還是需要密碼雜湊同步處理。

> [!NOTE] 
> 密碼永遠都不會以純文字儲存，或在 Azure AD 中使用可回復的演算法進行加密。 如需密碼雜湊同步處理實際程序的詳細資訊，請參閱[使用 Azure AD Connect 同步來實作密碼雜湊同步處理](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)。 

**傳遞驗證 (PTA)**：使用在一或多部內部部署伺服器上執行的軟體代理程式，為 Azure AD 驗證服務提供簡單的密碼驗證，直接使用您的內部部署 Active Directory 來驗證使用者，以確保密碼驗證不會在雲端發生。 如果公司的法規需求防止他們在雲端擁有雜湊密碼，因而無法使用密碼雜湊同步處理，則會使用此驗證方法。 如需實際傳遞驗證程序的詳細資訊，請參閱[使用 Azure Active Directory 傳遞驗證來進行使用者登入](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)。

### <a name="federated-authentication"></a>同盟驗證
當您選擇此驗證方法時，Azure AD 會將驗證程序交給另一個信任的驗證系統 (例如內部部署 Active Directory 同盟服務 (AD FS)) 來驗證使用者的密碼。 驗證系統可以提供額外的驗證需求，例如，以智慧卡為基礎的驗證或協力廠商的多重要素驗證。 如需詳細資訊，請參閱[部署 Active Directory 同盟服務](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide) \(機器翻譯\)。

下一節將使用決策樹，協助您判斷何種驗證方法最適合您。 它將協助您判斷是否要針對 Azure AD 混合式身分識別解決方案部署雲端或同盟驗證。

## <a name="azure-ad-authentication-decision-tree"></a>Azure AD 驗證決策樹

![image1](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations-on-authentication-methods"></a>驗證方法的詳細考量

### <a name="cloud-authentication-password-hash-sync"></a>雲端驗證：密碼雜湊同步處理 

* **投入量：**針對只需讓使用者登入 Office 365、SaaS 應用程式及其他以 Azure AD 為基礎之資源的組織，密碼雜湊同步處理在部署、維護和基礎結構方面所需投入的時間和努力最少。 啟用時，密碼雜湊同步處理屬於 Azure AD Connect 同步處理程序的一部分，每兩分鐘執行一次。 

* **使用者體驗：**建議組織搭配密碼雜湊同步處理來部署無縫單一登入 (SSO)，藉由避免在使用者登入之後出現不必要的提示來改善其登入體驗。

* **進階案例：**如果組織選擇此選項，就能搭配 Azure AD Identity Protection 報表來使用身分識別的見解，例如認證外洩的報表。 Windows Hello 企業版是另一個選項，其中具有[當您使用密碼雜湊同步處理時的特殊需求](https://docs.microsoft.com/en-us/windows/access-protection/hello-for-business/hello-identity-verification) \(英文\)。如果組織需要搭配密碼雜湊同步處理使用多重要素驗證，就必須使用 Azure AD Multi-Factor Authentication，而且無法使用協力廠商或內部部署的多重要素驗證方法。

* **商務持續性：**密碼雜湊同步處理本身就是一個高可用性的雲端服務，可擴展到所有 Microsoft 資料中心。 建議您基於災害復原目的，利用待命設定將第二部 Azure AD Connect 伺服器部署於預備模式中。

* **考量：**密碼雜湊同步處理不會立即強制執行目前在內部部署帳戶狀態中的變更。 在此情況下，使用者將可存取雲端應用程式，直到將使用者帳戶狀態同步處理到 Azure AD 為止。 如果組織想要克服這項限制，建議系統管理員在對內部部署使用者帳戶狀態執行大量更新 (例如停用帳戶) 之後，啟用新的同步處理循環。 帳戶鎖定是另一個要在下一個循環中同步處理的使用者帳戶狀態。 

> [!NOTE] 
> 目前不會使用 Azure AD Connect，將密碼過期狀態同步處理到 Azure AD。 

如需部署步驟，請參閱[實作密碼雜湊同步處理](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)。

### <a name="cloud-authentication-pass-through-authentication"></a>雲端驗證：傳遞驗證  

* **投入量：**針對傳遞驗證，您需要在可存取內部部署 Active Directory 網域服務 (包括存取內部部署 AD 網域控制站) 的現有伺服器上，安裝一或多個 (建議為三個) 輕量型代理程式。 這些代理程式需要對外存取網際網路，並存取您的網域控制站。 基於這個理由，它不支援在周邊網路部署代理程式，因為它需要對網域控制站進行不受限制的網路存取。 所有網路流量均會加密並受限於驗證要求。 如需此程序的詳細資訊，請參閱關於傳遞驗證的[安全性深入探討](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive)。

* **使用者體驗：**建議組織搭配傳遞驗證部署無縫單一登入，藉由避免在使用者登入之後出現不必要的提示來改善其登入體驗。

* **進階案例：**傳遞驗證可確保在內部部署使用者的帳戶狀態為已停用、鎖定、密碼過期，或超出對使用者允許的登入時數時，會立即拒絕驗證要求。 如果組織需要搭配傳遞驗證使用多重要素驗證，就必須使用 Azure AD Multi-Factor Authentication，而且無法使用協力廠商或內部部署的多重要素驗證方法。 不論您是否選擇了傳遞驗證，進階功能 (例如 Identity Protection 的認證外洩報表) 都需要部署密碼雜湊同步處理。

* **商務持續性：**除了 Azure AD Connect 伺服器上的第一個代理程式之外，建議您部署兩個額外的傳遞代理程式，以確保驗證要求的高可用性。 當您部署三個代理程式時，其中一個代理程式仍可在另一個代理程式關閉以進行維護時失敗。 除了傳遞驗證之外，部署密碼雜湊同步處理的另一個優點是，當主要驗證方法不再可用時 (例如，當內部部署伺服器無法使用時)，它可以用來作為備份驗證方法。

* **考量：**如果您使用密碼雜湊同步處理作為傳遞驗證的備份驗證方法，而且代理程式無法驗證使用者的認證，則不會自動發生容錯移轉至密碼雜湊同步處理。 您必須使用 Azure AD Connect，以手動方式切換登入方法。 傳遞驗證只支援使用新式驗證和 ActiveSync、POP3 及 IMAP4 等特定 Exchange Online 通訊協定的雲端應用程式。 例如，[Microsoft Office 2013 和更新版本支援新式驗證，但舊版不支援](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/) \(英文\)，進一步了解 Office 應用程式支援的資訊。 請參閱[常見問題集](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)，以及關於傳遞驗證 (包括替代識別碼) 支援的其他考量。

如需部署步驟，請參閱[實作傳遞驗證](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)。

### <a name="federated-authentication"></a>同盟驗證

* **投入量：**使用同盟驗證系統會依賴外部系統來驗證使用者。 某些公司想要搭配 Azure AD 混合式身分識別解決方案，重複使用他們現有的同盟系統投資。 同盟系統的維護和管理已超出 Azure AD 的控制範圍。 這取決於使用同盟系統的組織，以確定它會安全地部署並可處理驗證負載。 

* **使用者體驗：**同盟驗證的使用者體驗取決於功能的實作、拓樸以及同盟伺服器陣列的設定。 某些組織需要這種彈性來調整和設定對於同盟伺服器陣列的存取，以符合他們的安全性需求。 例如，可將內部連線的使用者和裝置設定為自動登入使用者，而不會提示他們提供認證，因為他們已經登入到裝置。 另一方面，某些進階安全性功能會在必要時，讓使用者的登入程序變得更加困難。

* **進階案例：**當客戶具備 Azure AD 原本不支援的驗證需求時，通常需要使用同盟驗證解決方案，詳細資訊[列於此處](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/) \(英文\)，但一般需求包括：

    * 需要智慧卡或憑證的驗證
    * 使用內部部署 MFA Server 或協力廠商的多重要素驗證提供者。
    * 使用協力廠商的驗證解決方案進行驗證。 請參閱 [Azure AD 同盟相容性清單](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)。
    * 使用者必須使用其 sAMAccountName (例如網域\使用者名稱) 登入，而不是利用使用者主體名稱 (UPN) (例如 user@domain.com)。
    * 依賴內部企業公開金鑰基礎結構 (PKI) 進行憑證信任的 Windows Hello 企業版實作

* **商務持續性：**同盟系統通常需要負載平衡的伺服器陣列 (也稱為伺服器陣列)，其設定於內部網路與周邊網路拓撲中，以確保驗證要求的高可用性。 密碼雜湊同步處理可以和同盟驗證一起部署，當主要驗證方法不再可用時 (例如，當內部部署伺服器無法使用時)，用來作為備份驗證方法。 某些大型企業組織需要同盟解決方案，以支援針對低度延遲的驗證要求使用地理 DNS 設定的多個網際網路輸入點。

* **考量：**同盟系統通常需要對內部部署基礎結構進行更大量的投資。 如果他們已經對內部部署同盟進行投資，而且它是強烈要求使用單一身分識別提供者的商務需求，則大部分的組織都會選擇此選項。 相較於雲端驗證解決方案，同盟在操作和疑難排解方面更為複雜。 將使用者識別碼與無法經路由傳送的網域搭配使用時，無法在 Azure AD 中進行驗證，需要實作額外的設定才能登入。 此需求稱為替代登入識別碼支援。 如需限制和需求，請參閱[設定替代登入識別碼](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) \(機器翻譯\)。

如需部署步驟，請參閱[實作同盟服務](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/deploying-federation-servers) \(機器翻譯\)。

> [!NOTE] 
> 當您部署 Azure AD 混合式身分識別解決方案時，必須確定您會實作其中一個支援的 Azure AD Connect 拓撲。 若要深入了解支援與不支援的設定，請參閱 [Azure AD Connect 的拓撲](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-topologies)。

## <a name="architecture-diagrams"></a>架構圖

下圖概述每個驗證方法所需的高階架構元件，您可以將其與 Azure AD 混合式身分識別解決方案搭配使用。 它提供比較解決方案之間差異的概觀。

下圖概述簡化的密碼雜湊同步處理解決方案：

![PHS](media/azure-ad/azure-ad-authn-image2.png)

下圖概述傳遞驗證的代理程式需求：

![PTA](media/azure-ad/azure-ad-authn-image3.png)

下圖概述在組織的周邊與內部網路中同盟所需的元件：

![ADFS](media/azure-ad/azure-ad-authn-image4.png)

## <a name="recommendations-and-considerations-from-azure-ad"></a>來自 Azure AD 的建議與考量

您的身分識別系統可確保使用者能夠存取您移轉的雲端應用程式和商務營運應用程式，並使其可在雲端使用。 驗證會控制對應用程式的存取，讓授權的使用者保有生產力，並讓有心人士遠離組織的敏感性資料。 基於這個理由，為組織選擇正確的驗證方法時，請考量下列建議。 

不論您選擇了何種驗證方法，請基於下列因素使用或啟用密碼雜湊同步處理：

1. **高可用性和災害復原：**傳遞驗證和同盟均依賴內部部署基礎結構。 針對傳遞驗證，這包含伺服器硬體和網路。 它也會依賴網域控制站，以回應來自傳遞驗證代理程式的驗證要求。 針對同盟，內部部署使用量更多，因為它要求您周邊網路中的伺服器來為驗證要求和內部同盟伺服器設定 Proxy。 為了避免單一失敗點，組織應該部署備援伺服器，以確保即使有任何元件失敗，驗證要求永遠都能提供服務。 許多元件需要維護，才能維持狀況良好，而且若未正確地規劃與實作維護，很可能就會發生中斷。 中斷可以使用密碼雜湊同步處理來避免，因為 Microsoft 的 Azure AD 雲端驗證服務會進行全域調整且永遠可供使用。

2. **內部部署中斷存續：**因為發生網路攻擊或災害而導致的內部部署中斷後果可能很顯著，範圍可從聲譽品牌損毀到因無法處理攻擊而使組織癱瘓。 去年就有許多組織成為惡意程式碼攻擊的受害者，包括可使其內部部署伺服器當機的目標勒索軟體。 為了協助客戶處理這些類型的攻擊，Microsoft 注意到兩類組織：

   a. 有一類組織會藉由變更其驗證方法來使用密碼雜湊同步處理，幾小時內就能使先前啟用的密碼雜湊同步處理回到線上。透過 Office 365 使用電子郵件的存取，他們就能解決問題並存取其他雲端式工作負載。

   b. 另一類是先前未啟用密碼雜湊同步處理的組織，此時必須求助於未受信任的外部取用者電子郵件系統以進行通訊並解決問題。 在那些情況下，他們需要數週或更長的時間，才能再次上線並執行。

3. **Identity Protection：**保護雲端使用者的最佳方式之一是 Azure AD Identity Protection。 Microsoft 會針對使用者和密碼清單持續掃描網際網路，以防範有心人士在暗網上進行銷售和提供。 Azure AD 可以使用此資訊來驗證組織內的任何使用者名稱和密碼是否遭到洩漏。 因此，無論您使用的是何種驗證方法 (不論是同盟或傳遞驗證)，請務必啟用密碼雜湊同步處理。 外洩的認證會以報表呈現，而且可在使用者嘗試使用外洩的密碼登入時，用來封鎖或強制使用者變更其密碼。

最後，根據 [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html) \(英文\)，Microsoft 具備一組最完整的身分識別和存取管理功能。 Microsoft 每天均可抵擋七兆個網路事件，同時讓授權的使用者幾乎可從任何裝置登入數千個 SaaS 應用程式 (例如 Office 365)。 

## <a name="conclusion"></a>結論

本文概述了組織可用來設定並部署以支援存取雲端應用程式的各種驗證選項。 為了符合各種商務、安全性和技術需求，組織可以在密碼雜湊同步處理、傳遞驗證和同盟之間進行選擇。 利用每種驗證方法，組織就能選擇是否要花費心力來處理商務需求，以部署登入程序的解決方案和使用者體驗。 您也需要評估組織是否需要每個驗證方法的進階案例和商務持續性功能。 最後，您必須評估每個驗證方法的考量，以查看是否有任何項目會阻礙實作您的選擇。

## <a name="next-steps"></a>後續步驟

在現今的世界中，威脅時時刻刻、無所不在。 實作正確的驗證方法，將可協助降低安全性風險，並保護您的身分識別。 

[開始使用](https://docs.microsoft.com/en-us/azure/active-directory/get-started-azure-ad) Azure AD，並為組織部署正確的驗證解決方案。

如果您正考慮從同盟移轉到雲端驗證，請深入了解[變更使用者登入方法](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method)。 為了協助您規劃並實作移轉，您可以使用[這些專案計劃來提供協助](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/tree/master/Authentication) \(英文\)。
