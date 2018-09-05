---
title: 在 PIM 中設定 Azure AD 目錄角色設定 | Microsoft Docs
description: 如何在 Azure AD Privileged Identity Management (PIM) 中設定 Azure AD 目錄角色設定。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 20a704a0d5b61134a61b5cbf02a1c71dbc7039e1
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189332"
---
# <a name="configure-azure-ad-directory-role-settings-in-pim"></a>在 PIM 中設定 Azure AD 目錄角色設定

特殊權限角色管理員可以自訂其組織中的 Azure AD Privileged Identity Management (PIM)，包括變更啟用合格角色指派之使用者的體驗。

## <a name="open-role-settings"></a>開啟角色設定

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 目錄角色]。

1. 按一下 [設定] 。

    ![Azure AD 目錄角色 - 設定](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. 按一下 [角色]。

1. 按一下您想要設定其設定的角色。

    ![Azure AD 目錄角色 - 設定角色](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    在每個角色的設定頁面上，有許多您可以設定的設定。 這些設定只會影響身為**合格**指派 (而不是**永久**指派) 的使用者。

## <a name="activations"></a>啟用

**啟用**滑桿是角色在到期前維持作用中狀態的最長時間 (以小時為單位)。 此值可介於 1 到 72 小時。

## <a name="notifications"></a>通知

**通知**的切換可讓您選擇是否要讓系統傳送電子郵件給系統管理員來確認他們已啟用角色。 這對偵測未經授權或不合法的啟用而言相當有用。

## <a name="incidentrequest-ticket"></a>事件/要求票證

**事件/要求票證**的切換可讓您選擇是否要要求合格系統管理員在啟用其角色時包含票證號碼。 當您執行角色存取稽核時，這會相當有用。

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

**Multi-Factor Authentication** 的切換可讓您選擇是否要要求使用者在啟用其角色之前，先以 MFA 驗證其身分識別。 他們只需在每一工作階段進行一次驗證，而不需在每次啟用角色時都進行驗證。 啟用 MFA 時，需要記住兩個秘訣：

* 使用 Microsoft 帳戶作為電子郵件地址 (通常是 @outlook.com，但不一定) 的使用者無法註冊 Azure MFA。 如果您想要將角色指派給使用 Microsoft 帳戶的使用者，您應該將他們設為永久系統管理員，或是停用該角色的 MFA。
* 您無法將 Azure AD 和 Office365 中高特殊權限角色的 MFA 停用。 這是一項安全功能，因為這些角色應該嚴密地受到保護：  
  
  * 應用程式管理員
  * 應用程式 Proxy 伺服器管理員
  * 計費管理員  
  * 規範管理員  
  * CRM 服務管理員
  * 客戶 LockBox 存取核准者
  * 目錄寫入器  
  * Exchange 系統管理員  
  * 全域管理員
  * Intune 服務管理員
  * 信箱管理員  
  * 合作夥伴第 1 層支援  
  * 合作夥伴第 2 層支援  
  * 特殊權限角色管理員
  * 安全性系統管理員  
  * SharePoint 管理員  
  * 商務用 Skype 的管理員  
  * 使用者帳戶管理員  

如需搭配 PIM 使用 MFA 的詳細資訊，請參閱[在 PIM 中對 Azure AD 目錄角色要求多重要素驗證](pim-how-to-require-mfa.md)。

## <a name="require-approval"></a>需要核准

**需要核准**的切換可讓您選擇是否需要核准才可啟用此角色。

1. 當您將切換設定為 [已啟用] 時，窗格會隨即展開，其中包含可選取核准者的選項。

    ![Azure AD 目錄角色 - 設定 - 需要核准](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    如果您**尚未**指定任何核准者，特殊權限角色管理員就會變成預設核准者。 需要有特殊權限角色管理員，才能核准此角色的**所有**啟用要求。

1. 若要指定核准者，請按一下 [選取核准者]。

    ![Azure AD 目錄角色 - 設定 - 需要核准](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 選取一個或多個核准者，然後按一下 [選取]。 您可以選取使用者或群組。 建議至少選取 2 個核准者。 不允許自我核准。

    您的選項將出現在所選取的核准者清單中。

1. 指定所有角色設定後，按一下 [儲存] 以儲存變更。


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>後續步驟

- [在 PIM 中對 Azure AD 目錄角色要求多重要素驗證](pim-how-to-require-mfa.md)
- [在 PIM 中設定 Azure AD 目錄角色的安全性警示](pim-how-to-configure-security-alerts.md)
