---
title: 在 Privileged Identity Management Azure Active Directory 中設定 Azure AD 角色設定 |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中設定 Azure AD 角色設定。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cff298e24ac185767e6290e396818ccece7b9b55
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809162"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>在 Privileged Identity Management 中設定 Azure AD 角色設定

特殊權限角色管理員可以在其 Azure Active Directory （Azure AD）組織中自訂 Privileged Identity Management （PIM），包括變更啟用合格角色指派之使用者的體驗。

## <a name="open-role-settings"></a>開啟角色設定

請依照下列步驟開啟 Azure AD 角色的設定。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 角色]。

1. 按一下 [設定]。

    ![Azure AD 角色-設定](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. 按一下 [角色]。

1. 按一下您想要設定其設定的角色。

    ![Azure AD 角色-設定角色](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    在每個角色的設定頁面上，有許多您可以設定的設定。 這些設定只會影響身為**合格**指派 (而不是**永久**指派) 的使用者。

## <a name="activations"></a>啟用

使用 [啟用] 滑桿，設定角色在到期前維持作用中狀態的最長時間 (以小時為單位)。 此值可介於 1 到 72 小時。

## <a name="notifications"></a>通知

使用 [通知] 參數來指定啟動角色時，系統管理員是否會收到電子郵件通知。 此通知有助於偵測未經授權或不合法的啟用。

設定為 [已啟用] 時，就會傳送通知給：

- 特殊權限角色管理員
- 安全性系統管理員
- 全域管理員

如需詳細資訊，請參閱[Privileged Identity Management 中的電子郵件通知](pim-email-notifications.md)。

## <a name="incidentrequest-ticket"></a>事件/要求票證

使用「**事件/要求票證**」參數，要求合格系統管理員在啟用其角色時包含票證號碼。 這種作法可讓角色存取審核更有效率。

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

使用 [Multi-Factor Authentication] 的切換，指定是否要要求使用者在啟用其角色之前，先以 MFA 驗證其身分識別。 他們只需要在每個會話中驗證其身分識別一次，而不是每次啟動角色。 啟用 MFA 時，需要記住兩個秘訣：

- 具有 Microsoft 帳戶的電子郵件地址（通常 @outlook.com，但不一定）的使用者無法註冊 Azure 多重要素驗證。 如果您想要將角色指派給具有 Microsoft 帳戶的使用者，您應該將其設為永久系統管理員或停用該角色的多重要素驗證。
- 您無法針對 Azure AD 和 Office 365 的高特殊許可權角色停用 Azure 多重要素驗證。 此安全性功能可協助保護下列角色：  
  
  - Azure 資訊保護系統管理員
  - 計費管理員
  - 雲端應用程式系統管理員
  - 規範管理員
  - 條件式存取系統管理員
  - Dynamics 365 管理員
  - 客戶 LockBox 存取核准者
  - 目錄寫入器
  - Exchange 系統管理員
  - 全域管理員
  - Intune 管理員
  - Power BI 系統管理員
  - 特殊權限角色管理員
  - 安全性系統管理員
  - SharePoint 管理員
  - 商務用 Skype 的管理員
  - 使用者管理員

如需詳細資訊，請參閱[多重要素驗證和 Privileged Identity Management](pim-how-to-require-mfa.md)。

## <a name="require-approval"></a>需要核准

如果您想要委派所需的核准來啟用角色，請遵循下列步驟。

1. 將 [需要核准] 切換為 [啟用]。 展開的窗格會有選取核准者的選項。

    ![Azure AD 角色-設定-需要核准](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    如果您未指定任何核准者，特殊權限角色管理員會變成預設核准者，然後需要核准此角色的所有啟用要求。

1. 若要指定核准者，請按一下 [選取核准者]。

    ![Azure AD 角色-設定-需要核准](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 除了特殊權限角色管理員以外，請選取一或多個核准者，然後按一下 [**選取**]。 您可以選取使用者或群組。 我們建議至少有兩個核准者。 即使您將自己新增為核准者，也無法自行核准角色啟用。 您的選項將出現在所選取的核准者清單中。

1. 在您指定所有角色設定之後，請選取 [**儲存**] 以儲存變更。

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中設定 Azure AD 角色的安全性警示](pim-how-to-configure-security-alerts.md)
