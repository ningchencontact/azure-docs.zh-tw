---
title: Azure Active Directory 條件式存取中的條件 | Microsoft Docs
description: 了解如何在 Azure Active Directory 條件式存取中使用指派來觸發原則。
services: active-directory
keywords: 應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5f0ff092a7535448d48642e972d1d36652f1b83f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34735136"
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory 條件式存取中的條件 

透過 [Azure Active Directory (Azure AD) 條件式存取](active-directory-conditional-access-azure-portal.md)，您可以控制授權使用者如何存取您的雲端應用程式。 在條件式存取原則中，您會定義對觸發原則之原因 ("when this happens") 做出的回應 ("do this")。 

![控制](./media/active-directory-conditional-access-conditions/10.png)


在條件式存取的情境中：

- "**When this happens**" 稱為**條件**。 
- "**Then do this**" 稱為**存取控制**。

條件與存取控制的組合即代表了條件式存取原則。

![控制](./media/active-directory-conditional-access-conditions/61.png)


您未在條件式存取原則中設定的條件不會套用。 某些條件會[強制](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work)將條件式存取原則套用至環境。 

本文將簡要說明條件及如何在條件式存取原則中使用條件。 

## <a name="users-and-groups"></a>使用者和群組

使用者和群組條件是條件式存取原則中的必要條件。 在原則中，您可以選取 [所有使用者] 或選取特定的使用者或群組。

![控制](./media/active-directory-conditional-access-conditions/111.png)

當您選取：

- [所有使用者] 時，您的原則會套用至目錄內的所有使用者。 這包括來賓使用者。

- [選取使用者和群組] 時，您可以設定下列選項：

    - [所有來賓使用者] - 可讓您將原則目標鎖定為 B2B 來賓使用者。 此條件符合 [userType] 屬性設為 [來賓] 的任何使用者帳戶。 如果在 Azure AD 的邀請流程中建立帳戶時需立即套用原則，便可使用此設定。

    - [目錄角色] - 可讓您根據使用者的角色指派來指定目標原則。 此條件支援目錄角色，例如 [全域管理員] 或 [密碼管理員]。

    - [使用者和群組] - 可讓您將目標設定為幾組特定的使用者。 例如，當您選取 HR 應用程式作為雲端應用程式時，可以選取包含 HR 部門所有成員的群組。

某個群組，可以是 Azure AD 中任何類型的群組，包括動態或已指派的安全性與通訊群組

您也可以將特定使用者或群組自原則中排除。 其中一個常見的使用案例是原則強制執行多重要素驗證 (MFA) 時的服務帳戶。 

對於部署新原則來說，以特定的幾組使用者為目標會相當有用。 在新原則中，您應該僅以一組初始使用者為目標來驗證原則行為。 



## <a name="cloud-apps"></a>雲端應用程式 

雲端應用程式是網站或服務。 這包括受「Azure 應用程式 Proxy」保護的網站。 如需所支援雲端應用程式的詳細描述，請參閱[雲端應用程式指派](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments)。    

雲端應用程式條件是條件式存取原則中的必要條件。 在原則中，您可以選取 [所有雲端應用程式] 或選取特定的應用程式。

![控制](./media/active-directory-conditional-access-conditions/03.png)

您可以選取：

- [所有雲端應用程式]，以針對要套用至整個組織的原則設定基準。 此選項的其中一個常見使用案例，就是在偵測到任何雲端應用程式有登入風險時，要求執行多重要素驗證的原則。 套用至**所有雲端應用程式**的原則，也會套用至所有網站和服務的存取。 此設定並非僅限於 [選取雲端應用程式] 清單上出現的雲端應用程式。

- 個別雲端應用程式，以依據原則將特定服務當作目標。 例如，您可以要求使用者必須具備[符合規範的裝置](active-directory-conditional-access-policy-connected-applications.md)，才能存取 SharePoint Online。 當使用者存取 SharePoint 內容 (例如 Microsoft Teams) 時，此原則也會套用至其他服務。 

您也可以將特定應用程式自原則中排除；不過，這些應用程式仍須遵守在它們所存取的服務上套用的原則。 



## <a name="sign-in-risk"></a>登入風險

登入風險是一項可能性指標 (高、中或低)，代表執行登入嘗試的使用者不是使用者帳戶合法擁有者的可能性。 Azure AD 會計算使用者登入期間的登入風險層級。 您可以使用計算出的登入風險層級作為條件式存取原則中的條件。 

![條件](./media/active-directory-conditional-access-conditions/22.png)

若要使用此條件，您必須[啟用 Azure Active Directory Identity Protection](active-directory-identityprotection.md)。
 
此條件的常見使用案例是會採取下列措施的原則：

- 封鎖具有高度登入風險層級的使用者，以防止可能的非合法使用者存取雲端應用程式。 
- 針對具有中度登入風險的使用者，要求執行多重要素驗證。 藉由強制執行多重要素驗證，您可以更加確信執行登入的使用者是帳戶的合法擁有者。

如需詳細資訊，請參閱[有風險的登入](active-directory-identityprotection.md#risky-sign-ins)。  

## <a name="device-platforms"></a>裝置平台

裝置平台是以您裝置上執行的作業系統為特徵。 Azure AD 會使用裝置 (例如使用者代理程式) 所提供的資訊來識別平台。 由於這項資訊未經驗證，因此建議您藉由封鎖存取、要求符合 Intune 原則規範或要求裝置加入網域，對所有平台套用原則。 預設是會將原則套用至所有裝置平台。 


![條件](./media/active-directory-conditional-access-conditions/24.png)

如需支援的裝置平台完整清單，請參閱[裝置平台條件](active-directory-conditional-access-technical-reference.md#device-platform-condition)。


此條件的其中一個常見使用案例，就是限制只有[受控的裝置](active-directory-conditional-access-policy-connected-applications.md#managed-devices)才能存取您雲端應用程式的原則。 如需更多案例 (包括裝置平台條件)，請參閱 [Azure Active Directory 應用程式型條件式存取](active-directory-conditional-access-mam.md)。



## <a name="device-state"></a>裝置狀態

本裝置狀態條件允許將已加入的混合式 Azure AD 和標示為符合規範的裝置，從條件式存取原則中排除。 當原則只應套用至未受控裝置，以提供多一層工作階段安全性時，此功能就相當實用。 例如裝置未受控時，可以只強制執行 Microsoft Cloud App Security 工作階段控制。 


![條件](./media/active-directory-conditional-access-conditions/112.png)

如果您想要封鎖未受控裝置的存取權，則應採取[裝置型條件式存取](active-directory-conditional-access-policy-connected-applications.md)。


## <a name="locations"></a>位置

透過位置，您可以選擇定義以連線嘗試起始點作為基礎的條件。 
     
![條件](./media/active-directory-conditional-access-conditions/25.png)

此條件的常見使用案例是會採取下列措施的原則：

- 針對在公司網路範圍外存取服務的使用者，要求執行多重要素驗證。  

- 針對從特定國家或地區存取服務的使用者，封鎖存取權。 

如需詳細資訊，請參閱 [Azure Active Directory 條件式存取中的位置條件](active-directory-conditional-access-locations.md)。


## <a name="client-apps"></a>用戶端應用程式

用戶端應用程式條件可讓您將原則套用至不同類型的應用程式，例如：

- 網站和服務
- 行動應用程式和傳統型應用程式。 



應用程式會被歸類為：

- 網站或服務 - 如果應用程式針對機密用戶端使用 Web SSO 通訊協定、SAML、WS-Fed 或 OpenID Connect。

- 行動應用程式或傳統型應用程式 - 如果應用程式針對原生用戶端使用行動應用程式 OpenID Connect。

如需可在條件式存取原則中使用的用戶端應用程式完整清單，請參閱「Azure Active Directory 條件式存取的技術參考」中的[用戶端應用程式條件](active-directory-conditional-access-technical-reference.md#client-apps-condition)。

此條件的常見使用案例是會採取下列措施的原則：

- 針對將大量資料下載到裝置的行動應用程式或傳統型應用程式，要求使用[符合規範的裝置](active-directory-conditional-access-policy-connected-applications.md)，但仍允許從任何裝置透過瀏覽器進行存取。

- 封鎖來自任何 Web 應用程式的存取，但允許來自行動應用程式和傳統型應用程式的存取。

除了使用 Web SSO 和新式驗證通訊協定之外，您也可以將此條件套用至使用 Exchange ActiveSync (例如大多數智慧型手機上的原生郵件應用程式) 的郵件應用程式。 目前，必須使用 AD FS 來保護使用傳統通訊協定的用戶端應用程式。

只有在 **Office 365 Exchange Online** 是唯一選取的雲端應用程式時，才可以選取此條件。

![雲端應用程式](./media/active-directory-conditional-access-conditions/32.png)

只有在原則中並未設定其他條件時，才支援選取 **Exchange ActiveSync** 作為用戶端應用程式條件。 不過，您可以將此條件的範圍縮小至只會套用到支援的平台。

 
![支援的平台](./media/active-directory-conditional-access-conditions/33.png)

只將此條件套用到支援的平台相當於套用到[裝置平台條件](active-directory-conditional-access-conditions.md#device-platforms)中的所有裝置平台。

![支援的平台](./media/active-directory-conditional-access-conditions/34.png)


 如需詳細資訊，請參閱

- [設定 SharePoint Online 和 Exchange Online，以便採用 Azure Active Directory 條件式存取](active-directory-conditional-access-no-modern-authentication.md)
 
- [Azure Active Directory 應用程式型條件式存取](active-directory-conditional-access-mam.md) 


### <a name="legacy-authentication"></a>舊版驗證  

條件式存取現在適用於不支援新式驗證的舊版 Office 用戶端，以及使用 POP、IMAP、SMTP 等郵件通訊協定的用戶端。這可讓您設定**封鎖其他用戶端的存取**之類的原則。


![舊版驗證](./media/active-directory-conditional-access-conditions/160.png)
 



#### <a name="known-issues"></a>已知問題

- 為**其他用戶端**設定原則會讓整個組織封鎖特定用戶端，例如 SPConnect。 這是因為這些舊版用戶端會以非預期的方式進行驗證。 主要的 Office 應用程式 (例如，較舊的 Office 用戶端) 不會有這個問題。 

- 原則最慢可能需要 24 小時才會生效。 


#### <a name="frequently-asked-questions"></a>常見問題集

**這會封鎖 Exchange Web 服務 (EWS) 嗎？**

視 EWS 所使用的驗證通訊協定而定。 如果 EWS 應用程式使用新式驗證，則會在「行動應用程式和桌面用戶端」用戶端應用程式的涵蓋範圍內。 如果 EWS 應用程式使用基本驗證，則會在「其他用戶端」用戶端應用程式的涵蓋範圍內。


**我可以對其他用戶端使用哪些控制項**

您可以對「其他用戶端」設定任何控制項。 不過，所有案例的終端使用者體驗都會是封鎖存取。 「其他用戶端」不支援 MFA、符合規範的裝置、網域加入等控制項。 
 
**我可以對其他用戶端使用哪些條件**

您可以對「其他用戶端」設定任何條件。

**Exchange ActiveSync 是否支援所有條件和控制項？**

編號 以下是 Exchange ActiveSync (EAS) 支援的摘要：

- EAS 僅支援使用者和群組目標。 它不支援來賓、角色。 如果設定了來賓/角色條件，由於我們無法判斷是否應將原則套用至使用者，所以會封鎖所有使用者。

- EAS 只能以雲端應用程式的形式與 Exchange 搭配運作。 

- EAS 不支援用戶端應用程式本身以外的任何條件。

- 您可以對 EAS 設定任何控制項 (但裝置相容性會導致封鎖)。

**原則之後是否會預設適用於所有用戶端應用程式？**

編號 預設原則行為不會有任何變更。 原則預設會繼續適用於瀏覽器和行動應用程式/桌面用戶端。



## <a name="next-steps"></a>後續步驟

- 如果您想要知道如何設定條件式存取原則，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。

- 如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)。 

