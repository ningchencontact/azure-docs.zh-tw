---
title: 使用 Azure Active Directory 連接 Active Directory。 | Microsoft Docs
description: Azure AD Connect 會整合您的內部部署目錄與 Azure Active Directory。 這可讓您為與 Azure AD 整合的 Office 365、Azure 和 SaaS 應用程式提供通用身分識別。
keywords: Azure AD Connect 簡介, Azure AD Connect 概觀, 何謂 Azure AD Connect, 安裝 active directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/02/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2aca42c23cc213d5d7e451105052d5d5d697b77d
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979466"
---
# <a name="hybrid-identity-and-microsoft-identity-solutions"></a>混合式身分識別和 Microsoft 身分識別解決方案
[Microsoft Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 混合式身分識別解決方案可讓您同步處理內部部署目錄物件與 Azure AD，仍同時在內部部署環境管理使用者。 在規劃同步處理內部部署 Windows Server Active Directory 與 Azure AD 時，首先要決定您要使用受控識別還是同盟身分識別。 

- **受控識別** - 從內部部署 Active Directory 和使用者驗證同步處理的使用者帳戶和群組會由 Azure 管理。   
- [同盟身分識別] 能藉由區隔使用者驗證與 Azure，以及將驗證委派給受信任的內部部署識別提供者，更進一步掌控使用者。 

有數個選項可用來設定混合式身分識別。 當您考慮哪個身分識別模型最適合貴組織的需求時，您也需要考慮時間、現有的基礎結構、複雜度和成本。 這些因素對每個組織而言不同，並可能隨時間改變。 不過，如果您的需求有所變更，您也有彈性切換至不同的身分識別模型。

## <a name="managed-identity"></a>受控識別 

受控識別是同步處理內部部署目錄物件 (使用者和群組) 與 Azure AD 的最簡單方法。 

![已同步處理的混合式身分識別](./media/whatis-hybrid-identity/managed.png)

雖然受控識別是最簡單且最快速的方法，但使用者仍需為雲端式資源維護個別的密碼。 若要避免這個問題，您也可以 (選擇性) [將使用者密碼的雜湊同步處理](how-to-connect-password-hash-synchronization.md)至 Azure AD 目錄。 同步處理密碼雜湊，可讓使用者使用其在使用內部部署環境使用的相同使用者名稱和密碼來登入雲端式組織資源。 Azure AD Connect 會定期檢查您的內部部署目錄變更，並且讓 Azure AD 目錄保持同步狀態。 當內部部署 Active Directory 的使用者屬性或密碼變更時，它會自動在 Azure AD 中更新。 

由於大部分組織只想要讓使用者登入 Office 365、SaaS 應用程式和其他 Azure AD 資源，因此建議使用預設的密碼雜湊同步處理選項。 如果您適用該選項，您必須在傳遞驗證與 AD FS 之間做決定。

> [!TIP]
> 使用者密碼會以代表實際使用者密碼的雜湊值形式儲存在內部部署 Windows Server Active Directory 中。 雜湊值是單向數學函式 (雜湊演算法) 的計算結果。 沒有任何方法可將單向函式的結果還原為純文字版本的密碼。 您無法使用密碼雜湊來登入您的內部部署網路。 當您選擇要同步處理密碼時，Azure AD Connect 會從內部部署 Active Directory 擷取密碼雜湊，並且在密碼雜湊同步處理至 Azure AD 之前套用額外的安全性處理。 密碼雜湊同步處理也可以搭配密碼回寫功能一起使用，以啟用 Azure AD 中的自助式密碼重設功能。 此外，您可以針對連至公司網路且加入網域的電腦使用者啟用單一登入 (SSO)。 使用單一登入，啟用的使用者只需要輸入使用者名稱即可安全地存取雲端資源。 
>

## <a name="pass-through-authentication"></a>傳遞驗證

[Azure AD 傳遞驗證](how-to-connect-pta.md)會針對使用內部部署 Active Directory 並以 Azure AD 為基礎的服務，提供簡單的密碼驗證解決方案。 如果貴組織的安全性與合規性原則不允許傳送使用者的密碼 (即使以雜湊表單形式)，而您只需要對已加入網域的裝置支援桌面 SSO，建議您評估使用傳遞驗證。 相較於 AD FS，傳遞驗證不需要在 DMZ 中部署，可簡化部署基礎結構。 當使用者使用 Azure AD 登入時，此驗證方法會向您的內部部署 Active Directory 直接驗證使用者的密碼。

![傳遞驗證](./media/whatis-hybrid-identity/pass-through-authentication.png)

使用傳遞驗證時，不需要複雜的網路基礎結構，而且您不需要在雲端儲存內部部署密碼。 與單一登入結合的傳遞驗證，可在登入 Azure AD 或其他雲端服務時，提供真正整合的經驗。

傳遞驗證已透過 Azure AD Connect 設定，其使用可接聽密碼驗證要求的簡單內部部署代理程式。 代理程式可以輕鬆地部署到多部電腦，以提供高可用性和負載平衡。 因為所有通訊都是輸出通訊，所以不需要在 DMZ 中安裝連接器。 連接器的伺服器電腦需求如下：

- Windows Server 2012 R2 或更新版本
- 加入樹系中將透過它驗證使用者的網域

## <a name="federated-identity-ad-fs"></a>同盟身分識別 (AD FS)

如需更進一步控制使用者如何存取 Office 365 和其他雲端服務，您可以使用 [Active Directory Federation Services (AD FS)](how-to-connect-fed-whatis.md) 設定與單一登入 (SSO) 的目錄同步作業。 讓使用者的登入與 AD FS 結成同盟，可將驗證委派給會驗證使用者認證的內部部署伺服器。 在此模型中，決不會將內部部署 Active Directory 認證傳送至 Azure AD。

![同盟身分識別](./media/whatis-hybrid-identity/federated-identity.png)

也稱為身分識別同盟，此登入方法可確保所有使用者驗證都是在內部部署環境中控制，並可讓系統管理員實作更嚴格的存取控制層級。 與 AD FS 的身分識別同盟是最複雜的選項，而且需要在內部部署環境中部署額外的伺服器。 身分識別同盟也認可您為 Active Directory 和 AD FS 基礎結構提供全天候的支援。 您必須提供此高層級的支援，因為如果內部部署網際網路存取、網域控制站或 AD FS 伺服器無法使用，使用者便無法登入雲端服務。

> [!TIP]
> 如果您選擇使用「與 Active Directory 同盟服務 (AD FS) 同盟」，則可以視需要設定密碼雜湊同步處理來作為 AD FS 基礎結構失敗時的備用方式。
>

## <a name="common-scenarios-and-recommendations"></a>常見案例和建議

以下是一些常見的混合式身分識別和存取管理案例，其中包含各自適合的混合式身分識別選項建議。

|我需要：|PHS 和 SSO<sup>1</sup>| PTA 和 SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|自動將我的內部部署 Active Directory 中建立的新使用者、連絡人及群組帳戶同步至雲端。|![建議](./media/whatis-hybrid-identity/ic195031.png)| ![建議](./media/whatis-hybrid-identity/ic195031.png) |![建議](./media/whatis-hybrid-identity/ic195031.png)|
|設定 Office 365 混合式案例的租用戶|![建議](./media/whatis-hybrid-identity/ic195031.png)| ![建議](./media/whatis-hybrid-identity/ic195031.png) |![建議](./media/whatis-hybrid-identity/ic195031.png)|
|讓我的使用者可以使用其內部部署密碼登入及存取雲端服務|![建議](./media/whatis-hybrid-identity/ic195031.png)| ![建議](./media/whatis-hybrid-identity/ic195031.png) |![建議](./media/whatis-hybrid-identity/ic195031.png)|
|使用公司認證實作單一登入|![建議](./media/whatis-hybrid-identity/ic195031.png)| ![建議](./media/whatis-hybrid-identity/ic195031.png) |![建議](./media/whatis-hybrid-identity/ic195031.png)|
|確定雲端中未儲存任何密碼雜湊| |![建議](./media/whatis-hybrid-identity/ic195031.png)|![建議](./media/whatis-hybrid-identity/ic195031.png)|
|啟用雲端多重要素驗證解決方案| |![建議](./media/whatis-hybrid-identity/ic195031.png)|![建議](./media/whatis-hybrid-identity/ic195031.png)|
|啟用內部部署 Multi-Factor Authentication 解決方案| | |![建議](./media/whatis-hybrid-identity/ic195031.png)|
|對使用者支援智慧卡驗證<sup>4</sup>| | |![建議](./media/whatis-hybrid-identity/ic195031.png)|
|在 Office 入口網站中和 Windows 10 桌面上顯示密碼到期通知| | |![建議](./media/whatis-hybrid-identity/ic195031.png)|

> <sup>1</sup> 單一登入的密碼雜湊同步處理。
>
> <sup>2</sup> 傳遞驗證和單一登入。 
>
> <sup>3</sup> 與 AD FS 同盟的單一登入。
>
> <sup>4</sup> AD FS 可與您的企業 PKI 整合，以允許使用憑證登入。 這些憑證可以是透過信任的佈建管道 (例如 MDM、GPO、智慧卡憑證 (包括 PIV/CAC 卡) 或 Hello for Business (cert-trust)) 部署的軟性憑證。 如需智慧卡驗證支援的詳細資訊，請參閱[這個部落格](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)。
>

## <a name="what-is-azure-ad-connect"></a>何謂 Azure AD Connect？

Azure AD Connect 是一種 Microsoft 工具，其設計目的是要符合並完成混合式身分識別的目標。  這可讓您為與 Azure AD 整合之 Office 365、Azure 和 SaaS 應用程式的使用者提供通用身分識別。  它可提供下列功能：
    
- [同步處理](how-to-connect-sync-whatis.md) - 此元件負責建立使用者、群組和其他物件。 它也負責確保您的內部部署使用者和群組的身分識別資訊符合雲端。  它會負責同步處理密碼雜湊與 Azure AD。
- [密碼雜湊同步處理](how-to-connect-password-hash-synchronization.md) - 選擇性元件，可藉由同步處理使用者密碼雜湊與 Azure AD，讓使用者在內部部署環境和雲端使用相同的密碼。
-   [AD FS 與同盟整合](how-to-connect-fed-whatis.md) - 同盟是 Azure AD Connect 的選用組件，可用來使用內部部署 AD FS 基礎結構來設定混合環境。 它也提供 AD FS 管理功能，例如憑證更新與其他的 AD FS 伺服器部署。
-   [傳遞驗證](how-to-connect-pta.md) - 另一個選用元件，可讓使用者在內部部署環境和雲端中使用相同的密碼，但不需要額外的同盟環境基礎結構
-   [PingFederate 和同盟整合](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) - 另一個同盟選項，可讓您使用 PingFederate 作為您的識別提供者。
-   [狀況監控](whatis-hybrid-identity-health.md) - Azure AD Connect Health 可以提供健全監控，並在 Azure 入口網站中提供檢視此活動的中央位置。 


![何謂 Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>何謂 Azure AD Connect Health？

Azure Active Directory (Azure AD) Connect Health 可協助您監視和了解內部部署身分識別基礎結構和同步處理服務。 它提供重要身分識別元件 (例如 Active Directory Federation Services (AD FS) 伺服器、Azure AD Connect 伺服器 (也稱為同步處理引擎)、Active Directory 網域控制站等) 的監視功能，讓您維護可靠的 Office 365 或 Microsoft Online Services 連線。它也使這些元件的相關重要資料點變得容易存取，讓您取得使用情況和其他重要情資，以做出明智的決策。

這些資訊會呈現在 [Azure AD Connect Health 入口網站](https://aka.ms/aadconnecthealth)中。 在 Azure AD Connect Health 入口網站中，您可以檢視警示、效能監視、使用情況分析等資訊。 Azure AD Connect Health 就像功能濾鏡，可讓您集中監控重要身分識別元件的健康狀態。

![Azure AD Connect Health 是什麼](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


隨著 Azure AD Connect Health 的功能越來越多，入口網站透過身分識別的功能濾鏡，提供單一儀表板。 您會有更為穩固健全的整合式環境，讓使用者的辦事能力更強。


## <a name="why-use-azure-ad-connect"></a>為何要使用 Azure AD Connect？
將內部部署目錄與 Azure AD 整合可提供一個通用身分識別來存取雲端和內部部署資源，讓使用者變得更有生產力。 使用者和組織可以享受到下列好處：

* 使用者可以使用單一身分識別來存取內部部署應用程式和雲端服務，例如 Office 365。
* 單一工具即可提供輕鬆進行同步處理和登入的部署經驗。
* 提供您案例的最新功能。 Azure AD Connect 會取代舊版的身分識別整合工具，如 DirSync 和 Azure AD Sync。如需詳細資訊，請參閱 [混合式身分識別目錄整合工具比較](plan-hybrid-identity-design-considerations-tools-comparison.md)。

## <a name="why-use-azure-ad-connect-health"></a>為何使用 Azure AD Connect Health？
將內部部署目錄與 Azure AD 整合時，使用者會更具生產力，因為只要一個通用身分識別，即可存取雲端和內部部署資源。 然而，此整合也會帶來挑戰，必須確保此環境良好健全，才能讓使用者從任何裝置可靠地存取內部部署和雲端中的資源。 Azure AD Connect Health 協助您監視及深入了解用來存取 Office 365 或其他 Azure AD 應用程式的內部部署身分識別基本結構。 使用方式相當簡單，您只需將代理程式安裝在各個內部部署身分識別伺服器中即可。

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[適用於 AD FS 的 Azure AD Connect Health](how-to-connect-health-adfs.md)
在 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016 上，適用於 AD FS 的 Azure AD Connect Health 支援 AD FS 2.0。 它也支援監視可驗證外部網路存取的 AD FS Proxy 或 Web 應用程式 Proxy 伺服器。 藉由輕鬆且快速地安裝健康情況代理程式，適用於 AD FS 的 Azure AD Connect Health 可提供您一組重要功能。

#### <a name="key-benefits-and-best-practices"></a>重要優勢和最佳做法

- *強化的安全性*
  - [外部網路鎖定趨勢](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [失敗的登入報告](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - 在[符合隱私權規範](reference-connect-health-user-privacy.md)    
- 取得所有[重大 ADFS 系統問題](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)的警示
    - 伺服器設定和可用性 
    - [效能和連線能力](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - 定期維護    
- *容易部署及管理*
  - 快速[代理程式安裝](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) 
  - 代理程式自動升級至最新版 
  - 幾分鐘內就可在入口網站中取得資料    
- *豐富的[使用量計量](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)* 
  - 應用程式使用量排名
  - 網路位置與 TCP 連線
  - 每一部伺服器的權杖要求數    
- 良好的使用者體驗 
  - 來自 Azure 入口網站的儀表板方式
  - [透過電子郵件警示](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>功能特點

*   利用警示進行監視，以了解 AD FS 與 AD FS Proxy 伺服器何時健康狀態不良
*   重大警示的電子郵件通知
*   效能資料的趨勢，適合用於 AD FS 的容量規劃
*   透過樞紐 (應用程式、使用者、網路位置等) 提供 AD FS 登入的使用情況分析
*   AD FS 報告，例如嘗試使用錯誤使用者名稱/密碼的前 50 名使用者及其最後一個 IP 位址
*   失敗 AD FS 登入的高風險 IP 報告

如需深入了解，請參閱[搭配 AD FS 使用 Azure AD Connect Health](how-to-connect-health-adfs.md)

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[適用於同步處理的 Azure AD Connect Health](how-to-connect-health-sync.md)
適用於同步處理的 Azure AD Connect Health 可監視及提供內部部署 Active Directory 與 Azure AD 之間發生的同步處理相關資訊。 適用於同步處理的 Azure AD Connect Health 提供下列一組主要功能：

* 利用警示進行監視，以了解 Azure AD Connect 伺服器 (也稱為同步處理引擎) 何時健康狀態不良
* 重大警示的電子郵件通知
* 同步處理操作情資，包括同步處理作業的延遲圖表以及不同作業 (如新增、更新、刪除) 的趨勢
* 有關同步處理屬性和上次成功匯出至 Azure AD 的快速概覽資訊
* 物件層級同步處理錯誤的相關報告\(不需要 Azure AD Premium\)

如需深入了解，請參閱[使用 Azure AD Connect Health 進行同步處理](how-to-connect-health-sync.md)

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[適用於 AD DS 的 Azure AD Connect Health](how-to-connect-health-adds.md)
適用於 Active Directory Domain Services (AD DS) 的 Azure AD Connect Health 可以監視安裝在 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 及 Windows Server 2016 上的網域控制站。 健康狀態代理程式可讓您從雲端監視內部部署 AD DS 環境。 適用於 AD DS 的 Azure AD Connect Health 提供下列一組主要功能：

* 監視警示可偵測網域控制站健全狀況不良，並發送重大警示的電子郵件通知
* 網域控制站儀表板可讓您快速檢視網域控制站的健全狀況和操作狀態
* 複寫狀態儀表板擁有最新的複寫資訊，以及偵測到錯誤時可用的疑難排解指南連結
* 可在任一處快速存取常用效能計數器的效能資料圖形，其為疑難排解和監視用途所必需

如需深入了解，請參閱[搭配 AD DS 使用 Azure AD Connect Health](how-to-connect-health-adds.md)

## <a name="next-steps"></a>後續步驟


- [硬體和先決條件](how-to-connect-install-prerequisites.md) 
- [快速設定](how-to-connect-install-express.md)
- [自訂設定](how-to-connect-install-custom.md)
- [密碼雜湊同步處理](how-to-connect-password-hash-synchronization.md)|
- [傳遞驗證](how-to-connect-pta.md)
- [Azure AD Connect 和同盟](how-to-connect-fed-whatis.md)
- [安裝 Azure AD Connect Health 代理程式](how-to-connect-health-agent-install.md) 
- [Azure AD Connect 同步處理](how-to-connect-sync-whatis.md)
- [版本歷程記錄](reference-connect-version-history.md)
- [目錄整合工具比較](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Azure AD Connect 常見問題集](reference-connect-faq.md)









