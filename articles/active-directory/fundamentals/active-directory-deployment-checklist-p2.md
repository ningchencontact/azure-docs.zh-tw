---
title: Premium P2 授權功能檢查清單 - Azure Active Directory | Microsoft Docs
description: 30 天、90 天及以上的 Azure Active Directory Premium P2 功能部署檢查清單。
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.custom: it-pro, seodec18
ms.openlocfilehash: 4fcb692d7189c84e32f55995538ffc692cb67dd6
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064707"
---
# <a name="azure-active-directory-premium-p2-licensing-feature-checklist"></a>Azure Active Directory Premium P2 授權功能檢查清單

為您的組織部署 Azure Active Directory (Azure AD) 並確保其安全，看起來似乎難以抗拒。 本文指出客戶發現很有幫助的一些常見工作。 客戶通常會在 30 天、90 天或更長時間內完成這些工作，以增強其安全狀態。 即使是已部署 Azure AD 的組織，也可以使用此檢查清單來確保從投資中獲得最大收益。

完善規劃及執行的身分識別基礎結構，可以讓已驗證使用者和裝置更加安全的存取生產力工作負載和資料。

## <a name="prerequisites"></a>必要條件

本指南假設您具有 Azure AD Premium P2 授權、Enterprise Mobility + Security E5、Microsoft 365 E5 或類似的授權套件。

[Azure AD 授權](https://azure.microsoft.com/pricing/details/active-directory/)

[Microsoft 365 企業版](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)

[Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)

## <a name="plan-and-deploy-day-1-30"></a>規劃和部署：第 1-30 天

- 指定超過一個全域管理員 (急用帳戶)
   - [在 Azure AD 中管理緊急存取系統管理帳戶](../users-groups-roles/directory-emergency-access.md)
- 開啟 Azure AD Privileged Identity Management (PIM) 以檢視報表
   - [開始使用 PIM](../privileged-identity-management/pim-getting-started.md)
- 可能的話，請使用非全域系統管理角色。
   - [在 Azure Active Directory 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md)
- 驗證
   - [推出自助式密碼重設](../authentication/howto-sspr-deployment.md)
   - 部署 Azure AD 密碼保護 (預覽)
      - [避免在組織中使用不當密碼](../authentication/concept-password-ban-bad.md)
      - [強制執行 Windows Server Active Directory 的 Azure AD 密碼保護](../authentication/concept-password-ban-bad-on-premises.md)
   - 設定帳戶鎖定原則
      - [Azure Active Directory 智慧鎖定](../authentication/howto-password-smart-lockout.md)
      - [AD FS 外部網路鎖定和外部網路智慧鎖定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)
   - [使用條件式存取原則部署 Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
   - [啟用自助密碼重設和 Azure AD Multi-Factor Authentication 的融合式註冊 (預覽)](../authentication/concept-registration-mfa-sspr-converged.md)
   - [啟用 Azure Active Directory Identity Protection](../identity-protection/enable.md)
      - [使用風險事件觸發 Multi-Factor Authentication 和密碼變更](../authentication/tutorial-risk-based-sspr-mfa.md)
   - [密碼指導方針](https://www.microsoft.com/research/publication/password-guidance/)
      - 維持八個字元的最低長度需求，愈長不一定愈好。
      - 移除字元組合的需求。
      - [移除使用者帳戶的強制定期密碼重設。](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire)
- 從內部部署 Active Directory 同步處理使用者
   - [安裝 Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md)
   - [實作密碼雜湊同步處理](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)
   - [實作密碼回寫](../authentication/howto-sspr-writeback.md)
   - [實作 Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)
- [依據 Azure Active Directory 中的群組成員資格將授權指派給使用者](../users-groups-roles/licensing-groups-assign.md)

## <a name="plan-and-deploy-day-31-90"></a>規劃和部署：第 31-90 天

- [規劃來賓使用者的存取權](../b2b/what-is-b2b.md)
   - [在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](../b2b/add-users-administrator.md)
   - [允許或封鎖對特定組織的 B2B 使用者邀請](../b2b/allow-deny-list.md)
   - [對 Azure AD 中的 B2B 使用者授與內部部署應用程式的存取權](../b2b/hybrid-cloud-to-on-premises.md)
- 決定使用者生命週期管理策略
- [決定裝置管理策略](../devices/overview.md)
   - [適用於 Azure AD Join 的使用案例和部署考量](../devices/azureadjoin-plan.md)
- [在組織中管理 Windows Hello 企業版](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

## <a name="plan-and-deploy-day-90-and-beyond"></a>規劃和部署：90 天以上

- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)
   - [在 PIM 中設定 Azure AD 目錄角色設定](../privileged-identity-management/pim-how-to-change-default-settings.md)
   - [在 PIM 中指派 Azure AD 目錄角色](../privileged-identity-management/pim-how-to-add-role-to-user.md)
- [在 PIM 中完成 Azure AD 目錄角色的存取權檢閱](../privileged-identity-management/pim-how-to-start-security-review.md)
- 全面管理使用者生命週期
   - Azure AD 具備管理身分識別生命週期的方法
   - 從您員工帳戶的生命週期移除手動步驟，以防止未經授權的存取：
      - 將真實來源 (HR系統) 中的身分識別同步至 Azure AD。
      - [使用動態群組，根據 HR (或您的真實來源) 的屬性自動將使用者指派至群組，例如部門、標題、區域和其他屬性。](../users-groups-roles/groups-dynamic-membership.md)
      - [使用群組型存取管理佈建，為 SaaS 應用程式自動佈建使用者。](../manage-apps/what-is-access-management.md)

## <a name="next-steps"></a>後續步驟

[身分識別與裝置存取設定](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[常見的建議身分識別和裝置存取原則](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
