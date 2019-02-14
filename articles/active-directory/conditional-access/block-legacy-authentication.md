---
title: 如何使用條件式存取封鎖對 Azure Active Directory (Azure AD) 的舊式驗證 |Microsoft Docs
description: 了解如何藉由使用 Azure AD 條件式存取封鎖舊式驗證來改善安全性狀態。
services: active-directory
keywords: 應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.subservice: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/01/2019
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5ff4861c288b82d6ce90d85c38ef3f92eb299ea4
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562975"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>作法：使用條件式存取封鎖對 Azure AD 的舊式驗證   

為了讓您的使用者能夠輕鬆存取雲端應用程式，Azure Active Directory (Azure AD) 支援多種驗證通訊協定，包括舊式驗證。 不過，舊式通訊協定並不支援多重要素驗證 (MFA)。 在許多環境中，MFA 都是防止身分識別遭竊的常用工具。 


如果您的環境已準備就緒，可封鎖舊式驗證以改善您租用戶的防護能力，您可以使用條件式存取來達成此目標。 本文說明如何設定為您的租用戶封鎖舊式驗證的條件式存取原則。



## <a name="prerequisites"></a>必要條件

本文假設您已熟悉以下各項： 

- Azure AD 條件式存取的[基本概念](overview.md) 
- 在 Azure 入口網站中設定條件式存取原則的[最佳做法](best-practices.md)



## <a name="scenario-description"></a>案例描述

Azure AD 支援數個最常用的驗證和授權通訊協定，包括舊式驗證。 舊式驗證是指使用基本驗證的通訊協定。 一般而言，這些通訊協定無法強制執行任何類型的第二因素驗證。 舉例來說，以舊式驗證為基礎的應用程式包括：

- 舊版的 Microsoft Office 應用程式

- 使用 POP、IMAP、SMTP 等郵件通訊協定的應用程式

在現今的環境中，單一要素驗證 (例如，使用者名稱和密碼) 已不敷使用。 密碼的缺點在於容易被猜到，且一般人不太懂得如何選擇理想的密碼。 密碼也很容易遭受各種攻擊，例如網路釣魚和密碼噴濺。 要防範密碼威脅，最簡單的方式就是實作 MFA。 透過 MFA，即便攻擊者取得使用者的密碼，單靠密碼本身仍不足以成功進行驗證並存取資料。

如何防止使用舊式驗證的應用程式存取您租用戶的資源？ 建議您使用條件式存取原則直接加以封鎖。 如有必要，您可以僅允許特定使用者和特定網路位置使用以舊式驗證為基礎的應用程式。




## <a name="implementation"></a>實作

本節說明如何使用條件式存取原則來封鎖舊式驗證。 

### <a name="block-legacy-authentication"></a>封鎖舊式驗證 

在條件式存取原則中，您可以設定與用來存取資源的用戶端應用程式繫結的條件。 用戶端應用程式條件可讓您針對 [行動裝置應用程式和桌面用戶端] 選取 [其他用戶端]，讓使用舊式驗證的應用程式能夠縮減其範圍。

![其他用戶端](./media/block-legacy-authentication/01.png)

若要封鎖這些應用程式的存取，您必須選取 [封鎖存取]。

![封鎖存取](./media/block-legacy-authentication/02.png)


### <a name="select-users-and-cloud-apps"></a>選取使用者和雲端應用程式

如果您想要為組織封鎖舊式驗證，您可能會認為藉由以下選取即可達成此目的：

- 所有使用者

- 所有雲端應用程式

- 封鎖存取
 

![指派](./media/block-legacy-authentication/03.png)



Azure 有一項安全功能會防止您建立此類原則，因為這種設定違反條件式存取原則的[最佳做法](best-practices.md)。
 
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

為**其他用戶端**設定原則會讓整個組織封鎖特定用戶端，例如 SPConnect。 之所以執行此封鎖，是因為舊版用戶端以非預期的方式進行驗證。 主要的 Office 應用程式 (例如，較舊的 Office 用戶端) 不會有這個問題。

原則最慢可能需要 24 小時才會生效。

您可以選取所有可用的授與控制作為其他用戶端條件；但使用者體驗都是相同的 - 存取遭到封鎖。

如果您使用其他用戶端條件來封鎖舊式驗證，您也可以設定裝置平台和位置條件。 例如，如果您只想要封鎖行動裝置的舊版驗證，請選取下列項目來設定**裝置平台**條件：

- Android

- iOS

- Windows Phone

![不支援原則設定](./media/block-legacy-authentication/06.png)




## <a name="next-steps"></a>後續步驟

如果您還不太了解如何設定條件式存取原則，請參閱[利用 Azure Active Directory 條件式存取來取得特定應用程式的 MFA](app-based-mfa.md)，以取得範例。
