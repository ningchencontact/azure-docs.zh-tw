---
title: Azure Active Directory 條件式存取中的條件有哪些？ | Microsoft Docs
description: 了解條件如何在 Azure Active Directory 條件式存取中使用來觸發原則。
services: active-directory
keywords: 條件式存取應用程式，與 Azure AD，安全地存取公司資源，條件式存取原則的條件式存取
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/17/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83263b3536100ce16819002c833754d18ac12816
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112603"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory 條件式存取中的條件有哪些？

您可以控制使用者如何存取您雲端應用程式使用[Azure Active Directory (Azure AD) 條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)。 在條件式存取原則中，您定義的回應 ("Then do this") 來觸發您的原則 ("When this happens") 的原因。

![原因和回應](./media/conditions/10.png)


內容中的條件式存取**當發生這種情況**稱為**條件**。 **則執行此動作**稱為**存取控制**。 您的條件與存取控制的組合代表條件式存取原則。

![條件式存取原則](./media/conditions/61.png)


您尚未設定條件式存取原則中的條件並不會套用。 某些條件皆[強制](best-practices.md)環境套用條件式存取原則。

這篇文章是條件，和它們的條件式存取原則中的使用方式的概觀。 

## <a name="users-and-groups"></a>使用者和群組

使用者和群組條件是條件式存取原則中的必要條件。 在原則中，您可以選取 [所有使用者]  或選取特定的使用者或群組。

![使用者和群組](./media/conditions/111.png)

當您選取 [所有使用者]  時，您的原則會套用至目錄內的所有使用者，包括來賓使用者。

當您選取 [使用者和群組]  時，您可以設定下列選項：

* [所有來賓使用者]  會以 B2B 來賓使用者作為原則的目標。 此條件會比對出 **userType** 屬性設為 [來賓]  的任何使用者帳戶。 如果在 Azure AD 的邀請流程中建立帳戶時需立即套用原則，便可使用此設定。

* [目錄角色]  會根據使用者的角色指派來設定原則的目標。 此條件支援目錄角色，例如 [全域管理員]  或 [密碼管理員]  。

* [使用者和群組]  會以幾組特定的使用者作為目標。 例如，在選取 HR 應用程式作為雲端應用程式時，您可以選取包含 HR 部門所有成員的群組。 群組可以是 Azure AD 中任何類型的群組，包括動態或已指派的安全性與通訊群組。

您也可以將特定使用者或群組自原則中排除。 其中一個常見的使用案例，是原則強制執行多重要素驗證 (MFA) 時的服務帳戶。

對於部署新原則來說，以特定的幾組使用者為目標會相當有用。 在新原則中，您應該僅以一組初始使用者為目標來驗證原則行為。

## <a name="cloud-apps-and-actions"></a>雲端應用程式和動作

雲端應用程式是網站、 服務或受 Azure AD 應用程式 Proxy 的端點。 如需所支援雲端應用程式的詳細描述，請參閱[雲端應用程式指派](technical-reference.md#cloud-apps-assignments)。 **雲端應用程式或動作**條件是條件式存取原則中的必要條件。 在原則中，您可以選取**所有雲端應用程式**，或指定的應用程式**選取 應用程式**。

組織可以從下列選擇：

* **所有雲端應用程式**時套用基準原則套用至整個組織。 使用此選項，任何雲端應用程式偵測到登入風險時需要多重要素驗證的原則。 存取原則套用至所有雲端應用程式適用於所有網站與服務。 此設定不會出現在選取的應用程式清單中的雲端應用程式的限制。
* **選取應用程式**，以依據原則將特定服務當作目標。 例如，您可以要求使用者必須符合規範的裝置來存取 SharePoint Online。 當使用者存取 SharePoint 內容時，此原則也會套用至其他服務。 Microsoft Teams 即為一例。

> [!NOTE]
> 您可以將特定應用程式從原則中排除。 不過，這些應用程式仍須遵循它們存取的服務所套用的原則。

**使用者動作**是可以由使用者執行的工作。 只有目前支援的動作**註冊安全性資訊 （預覽）** ，以便針對結合的註冊啟用的使用者嘗試註冊其安全性時強制執行條件式存取原則資訊。 可以在本文中，找到更多資訊[啟用結合安全性資訊註冊 （預覽）](../authentication/howto-registration-mfa-sspr-combined.md)。

## <a name="sign-in-risk"></a>登入風險

登入風險是可能性 (高、中或低) 的指標，代表執行登入的使用者不是使用者帳戶合法擁有者的可能性。 Azure AD 會計算使用者登入期間的登入風險層級。 您可以使用導出的登入風險層級作為條件式存取原則中的條件。

![登入風險層級](./media/conditions/22.png)

若要使用此條件，您必須[啟用 Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable)。
 
此條件的常見使用案例，是採取下列保護措施的原則： 

- 封鎖具有高度登入風險的使用者。 此保護措施可防止可能的非合法使用者存取您的雲端應用程式。 
- 針對具有中度登入風險的使用者，要求執行多重要素驗證。 藉由強制執行多重要素驗證，您可以更加確信執行登入的使用者是帳戶的合法擁有者。

如需詳細資訊，請參閱[偵測到工作階段風險時封鎖存取](app-sign-in-risk.md)。  

## <a name="device-platforms"></a>裝置平台

裝置平台是以您裝置上執行的作業系統為特徵。 Azure AD 會使用裝置 (例如使用者代理程式) 所提供的資訊來識別平台。 這項資訊未經驗證。 我們建議，所有平台均應套用原則。 原則應封鎖存取、要求須符合 Microsoft Intune 原則，或要求裝置必須已加入網域。 依預設會將原則套用至所有裝置平台。 


![設定裝置平台](./media/conditions/24.png)

如需支援的裝置平台清單，請參閱[裝置平台條件](technical-reference.md#device-platform-condition)。


此條件的其中一個常見使用案例，就是限制只有[受控的裝置](require-managed-devices.md)才能存取您雲端應用程式的原則。 更多的案例，包括裝置平台條件，請參閱 < [Azure Active Directory 應用程式型條件式存取](app-based-conditional-access.md)。



## <a name="device-state"></a>裝置狀態

裝置狀態條件排除加入混合式 Azure AD 的裝置和標記為符合規範的條件式存取原則的裝置。 


![設定裝置狀態](./media/conditions/112.png)

當原則只應套用至未受控裝置，以提供多一層工作階段安全性時，此條件將有其效用。 例如裝置未受控時，可以只強制執行 Microsoft Cloud App Security 工作階段控制。 

## <a name="locations"></a>位置

使用位置，可讓您根據嘗試連線的位置定義相關條件。 

![設定位置](./media/conditions/25.png)

此條件的常見使用案例，是採取下列保護措施的原則：

- 針對在公司網路以外存取服務的使用者，要求執行多重要素驗證。  

- 針對從特定國家或地區存取服務的使用者，封鎖存取權。 

如需詳細資訊，請參閱 <<c0> [ 什麼是 Azure Active Directory 條件式存取的位置條件？](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)。


## <a name="client-apps"></a>用戶端應用程式

根據預設，條件式存取原則適用於下列應用程式：

- **[瀏覽器應用程式](technical-reference.md#supported-browsers)** - 瀏覽器應用程式包括了使用 SAML、WS-同盟或 OpenID Connect Web SSO 通訊協定的網站。 也適用於已註冊為 OAuth 機密用戶端的任何網站或 Web 服務， 例如 Office 365 SharePoint 網站。 

- **[使用新式驗證的行動和傳統型應用程式](technical-reference.md#supported-mobile-applications-and-desktop-clients)** - Office 傳統型應用程式和手機應用程式均屬於此類應用程式。 


此外，您可以將原則鎖定至未使用新式驗證的特定用戶端應用程式，例如：

- **[Exchange ActiveSync 用戶端](conditions.md#exchange-activesync-clients)** - 當某項原則封鎖 Exchange ActiveSync 的使用時，受影響的使用者會收到一封隔離電子郵件，內有鎖定原因的相關資訊。 必要時，電子郵件會提供向 Intune 註冊裝置的指示。

- **[其他用戶端](block-legacy-authentication.md)** - 這類應用程式包括了使用基本驗證和郵件通訊協定 (如 IMAP、MAPI、POP、SMTP) 的用戶端，以及不是使用新式驗證的舊版 Office 應用程式。 如需詳細資訊，請參閱 [Office 2013 和 Office 2016 用戶端應用程式的新式驗證運作方式](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) (機器翻譯)。

![用戶端應用程式](./media/conditions/41.png)

此條件的常見使用案例，是具有下列需求的原則：

- 針對會將資料下載到裝置的行動應用程式和傳統型應用程式， **[要求使用受控裝置](require-managed-devices.md)** 。 在此同時，仍允許從任何裝置以瀏覽器進行存取。 這種情況下，可避免將文件儲存和同步至非受控的裝置。 透過此種方法，如果裝置遺失或遭竊，可以減少資料遺失的機率。

- 針對會使用 ActiveSync 存取 Exchange Online 的應用程式， **[要求使用受控裝置](require-managed-devices.md)** 。

- 封鎖對 Azure AD (其他用戶端) 的 **[舊版驗證](block-legacy-authentication.md)**

- 封鎖來自任何 Web 應用程式的存取，但允許來自行動應用程式和桌面應用程式的存取。



### <a name="exchange-activesync-clients"></a>Exchange ActiveSync 用戶端

在下列情況中，您只能選取 [Exchange ActiveSync 用戶端]  ：


- Microsoft Office 365 Exchange Online 是您選取的唯一雲端應用程式。

    ![雲端應用程式](./media/conditions/32.png)

- 您並未在原則中設定其他條件。 不過，您可以將此條件的範圍縮小到僅套用至[支援的平台](technical-reference.md#device-platform-condition)。
 
    ![僅將原則套用至支援的平台](./media/conditions/33.png)


當因為需要[受控裝置](require-managed-devices.md)而封鎖存取時，受影響的使用者會收到一封電子郵件，會在信中引導使用者使用 Intune。 

如果需要經過核准的應用程式，會提供說明指示，帶領受影響的使用者安裝和使用 Outlook 行動用戶端。

在其他情況下 (例如需要 MFA)，受影響的使用者會遭到封鎖，因為使用基本驗證的用戶端並不支援 MFA。

您只能將此設定用在使用者和群組上。 它不支援來賓或角色。 如果已使用 guest 帳戶或角色的條件，則會封鎖所有使用者，因為無法判斷條件式存取，或不若原則應該套用到使用者。


 如需詳細資訊，請參閱

- [SharePoint Online 和 Exchange Online 設定 Azure Active Directory 條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)。
 
- [Azure Active Directory 應用程式型條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)。 



## <a name="next-steps"></a>後續步驟

- 若要了解如何設定條件式存取原則，請參閱[快速入門：使用 Azure Active Directory 條件式存取所需的特定應用程式 MFA](app-based-mfa.md)。

- 若要設定條件式存取原則，為您的環境，請參閱[Azure Active Directory 中的條件式存取的最佳作法](best-practices.md)。 

