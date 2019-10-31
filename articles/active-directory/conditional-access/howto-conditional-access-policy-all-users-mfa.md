---
title: 條件式存取-所有使用者都需要 MFA-Azure Active Directory
description: 建立自訂的條件式存取原則，以要求所有使用者執行多重要素驗證
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 892171f3e275ebc405bc7a228185ebfabc61acfb
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164831"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>條件式存取：所有使用者都需要 MFA

身為 Alex Weinert，Microsoft 身分識別安全性的目錄在他的文章中提及[您的 Pa $ $word 並不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)：

> 您的密碼並不重要，但 MFA 的確沒問題！ 根據我們的研究，當您使用 MFA 時，您的帳戶會比99.9% 更不可能遭到入侵。

本文中的指導方針可協助您的組織為您的環境建立平衡的 MFA 原則。

## <a name="user-exclusions"></a>使用者排除專案

條件式存取原則是功能強大的工具，建議您從原則中排除下列帳戶：

* **緊急存取**或**中斷玻璃**帳戶，以避免整個租使用者帳戶鎖定。 在不太可能發生的情況下，所有系統管理員都會被鎖定在您的租使用者中，您的緊急存取系統管理帳戶可以用來登入租使用者，採取復原存取的步驟。
   * 如需詳細資訊，請參閱[Azure AD 中的管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)一文。
* **服務帳戶**和**服務原則**，例如 Azure AD Connect 同步處理帳戶。 服務帳戶是不會與任何特定使用者系結的非互動式帳戶。 後端服務通常會使用它們，並允許以程式設計方式存取應用程式。 應該排除服務帳戶，因為無法以程式設計方式完成 MFA。
   * 如果您的組織在腳本或程式碼中使用這些帳戶，請考慮將它們取代為[受控](../managed-identities-azure-resources/overview.md)識別。 暫時的因應措施是，您可以從基準原則中排除這些特定帳戶。

## <a name="application-exclusions"></a>應用程式排除專案

組織可能會有許多使用中的雲端應用程式。 並非所有的應用程式都可能需要同等的安全性。 例如，薪資和出席應用程式可能需要 MFA，但該餐廳可能不會。 系統管理員可以選擇從其原則中排除特定的應用程式。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

下列步驟將協助建立條件式存取原則，要求這些指派的系統管理角色執行多重要素驗證。

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入**Azure 入口網站**。
1. 流覽至**Azure Active Directory** > **條件式存取**。
1. 選取 [新增原則]。
1. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**]
   1. 在 [**排除**] 底下，選取 [**使用者和群組**]，然後選擇貴組織的 [緊急存取] 或 [中斷玻璃帳戶]。 
   1. 選取 [完成]。
1. 在 [**雲端應用程式] 或 [動作** > **包括**] 底下，選取 [**所有雲端應用程式**]
   1. 在 [**排除**] 底下，選取不需要多重要素驗證的任何應用程式。
1. 在 **[** **存取控制** > 授與] 底下，選取 **[授與存取權**，**需要多重要素驗證**]，然後選取 [**選取**]。
1. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
1. 選取 [**建立**] 以建立以啟用您的原則。

## <a name="next-steps"></a>後續步驟

[條件式存取的一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
