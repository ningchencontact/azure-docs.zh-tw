---
title: 如何封鎖舊版驗證至 Azure Active Directory (Azure AD)，使用條件式存取 |Microsoft Docs
description: 了解如何藉由封鎖舊有的驗證使用 Azure AD 條件式存取來改善安全性狀態。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60fc168a6c15e7cb8d6e4271154d99fb4136226e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509029"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>作法：區塊的舊版驗證至 Azure AD 條件式存取   

為了讓您的使用者能夠輕鬆存取雲端應用程式，Azure Active Directory (Azure AD) 支援多種驗證通訊協定，包括舊式驗證。 不過，舊式通訊協定並不支援多重要素驗證 (MFA)。 在許多環境中，MFA 都是防止身分識別遭竊的常用工具。 

如果您的環境是否準備好區塊來改善您的租用戶保護的舊式驗證，您可以使用條件式存取來達成此目標。 這篇文章說明如何設定條件式存取原則來封鎖舊版驗證您的租用戶。

## <a name="prerequisites"></a>必要條件

本文假設您已熟悉以下各項： 

- [基本概念](overview.md)的 Azure AD 條件式存取 
- [最佳做法](best-practices.md)在 Azure 入口網站中設定條件式存取原則

## <a name="scenario-description"></a>案例描述

Azure AD 支援數個最常用的驗證和授權通訊協定，包括舊式驗證。 舊式驗證是指使用基本驗證的通訊協定。 一般而言，這些通訊協定無法強制執行任何類型的第二因素驗證。 舉例來說，以舊式驗證為基礎的應用程式包括：

- 舊版的 Microsoft Office 應用程式
- 使用 POP、IMAP、SMTP 等郵件通訊協定的應用程式

在現今的環境中，單一要素驗證 (例如，使用者名稱和密碼) 已不敷使用。 密碼的缺點在於容易被猜到，且一般人不太懂得如何選擇理想的密碼。 密碼也很容易遭受各種攻擊，例如網路釣魚和密碼噴濺。 要防範密碼威脅，最簡單的方式就是實作 MFA。 透過 MFA，即便攻擊者取得使用者的密碼，單靠密碼本身仍不足以成功進行驗證並存取資料。

如何防止使用舊式驗證的應用程式存取您租用戶的資源？ 建議是只與條件式存取原則會封鎖它們。 如有必要，您可以僅允許特定使用者和特定網路位置使用以舊式驗證為基礎的應用程式。

第一個雙因素驗證完成之後，會強制執行條件式存取原則。 因此，條件式存取不是作為第一個的列防禦阻絕服務 (DoS) 攻擊，例如的情況下，但是可以利用從這些事件 （例如，登入風險層級，位置的要求，依此類推） 的訊號，來決定存取權限。

## <a name="implementation"></a>實作

本節說明如何設定條件式存取原則來封鎖舊版驗證。 

### <a name="identify-legacy-authentication-use"></a>識別舊有的驗證，請使用

您可以封鎖您目錄中的舊有的驗證之前，您需要先了解您的使用者是否具有使用舊有的驗證以及分割如何影響您整體的目錄的應用程式。 Azure AD 登入記錄檔可用來了解是否您使用舊有的驗證。

1. 瀏覽至**Azure 入口網站** > **Azure Active Directory** > **登入**。
1. 如果它不會顯示上的 新增用戶端應用程式的資料行**資料行** > **用戶端應用程式**。
1. 篩選依據**用戶端應用程式** > **其他用戶端**，按一下 **套用**。

篩選只顯示您登入嘗試，所進行的舊版驗證通訊協定。 按一下每個個別的登入嘗試，將會顯示其他詳細資料。 **用戶端應用程式**欄位下**基本資訊** 索引標籤會指出已使用的舊版驗證通訊協定。

這些記錄檔會指出哪些使用者仍然需要用到舊有的驗證，以及哪些應用程式使用舊版的通訊協定來進行驗證要求。 使用者不會出現在這些記錄檔，並確認不會使用舊有的驗證，實作條件式存取原則僅適用於這些使用者。

### <a name="block-legacy-authentication"></a>封鎖舊式驗證 

在條件式存取原則中，您可以設定會繫結至用來存取您的資源的用戶端應用程式的條件。 用戶端應用程式條件可讓您針對 [行動裝置應用程式和桌面用戶端]  選取 [其他用戶端]  ，讓使用舊式驗證的應用程式能夠縮減其範圍。

![其他用戶端](./media/block-legacy-authentication/01.png)

若要封鎖這些應用程式的存取，您必須選取 [封鎖存取]  。

![封鎖存取](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>選取使用者和雲端應用程式

如果您想要為組織封鎖舊式驗證，您可能會認為藉由以下選取即可達成此目的：

- 所有使用者
- 所有雲端應用程式
- 封鎖存取

![指派](./media/block-legacy-authentication/03.png)

Azure 有一項安全功能，導致您無法建立類似的原則，因為這項設定違反[最佳做法](best-practices.md)條件式存取原則。
 
![不支援原則設定](./media/block-legacy-authentication/04.png)

這項安全功能是必要的，因為*封鎖所有使用者和所有雲端應用程式*可能會使您的整個組織無法登入租用戶。 您至少須排除一個使用者，才能符合最低的最佳做法需求。 您也可以排除目錄角色。

![不支援原則設定](./media/block-legacy-authentication/05.png)

您可以從原則中排除一個使用者，以符合這項安全功能的需求。 在理想情況下，您應定義幾個 [Azure AD 中的緊急存取系統管理帳戶](../users-groups-roles/directory-emergency-access.md)，並將其從您的原則中排除。

## <a name="policy-deployment"></a>原則部署

在您將原則用於生產環境之前，請處理好：
 
- **服務帳戶** - 識別作為服務帳戶或由裝置 (例如會議室的電話) 使用的使用者帳戶。 請確定這些帳戶具有強式密碼，並將這些帳戶新增至排除的群組中。
- **登入報告** - 檢閱登入報告，並尋找**其他用戶端**流量。 識別最高使用量，並調查其使用原因。 流量通常由未使用新式驗證，或使用某些第三方郵件應用程式的舊版 Office 用戶端所產生。 請建立計劃以消除這些應用程式的使用，或者，如果影響不大，請通知使用者他們無法再使用這些應用程式。
 
如需詳細資訊，請參閱[如何部署新的原則？](best-practices.md#how-should-you-deploy-a-new-policy)。

## <a name="what-you-should-know"></a>您應該知道的事情

封鎖存取權來使用**其他用戶端**也會封鎖使用基本驗證的 Exchange Online PowerShell

為**其他用戶端**設定原則會讓整個組織封鎖特定用戶端，例如 SPConnect。 之所以執行此封鎖，是因為舊版用戶端以非預期的方式進行驗證。 主要的 Office 應用程式 (例如，較舊的 Office 用戶端) 不會有這個問題。

原則最慢可能需要 24 小時才會生效。

您可以選取所有可用的授與控制項**其他用戶端**條件; 不過，使用者體驗是一律相同-封鎖存取。

如果您封鎖使用舊有的驗證**其他用戶端**條件，您可以設定裝置平台及位置條件。 例如，如果您只想要封鎖行動裝置的舊版驗證，請選取下列項目來設定**裝置平台**條件：

- Android
- iOS
- Windows Phone

![不支援原則設定](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>後續步驟

- 如果您不熟悉尚未設定條件式存取原則，請參閱[需要針對特定應用程式使用 Azure Active Directory 條件式存取進行 MFA](app-based-mfa.md)的範例。
- 如需有關新式驗證支援的詳細資訊，請參閱[新式驗證運作方式的 Office 2013 和 Office 2016 用戶端應用程式](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
