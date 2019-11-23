---
title: On-premises password protection FAQ - Azure Active Directory
description: 內部部署 Azure AD 密碼保護常見問題集
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ee5d6328c6a3e4ea0b4a6359d4a21494e3ae62c
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381694"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Azure AD 密碼保護內部部署 - 常見問題集

This section provides answers to many commonly asked questions about Azure AD Password Protection.

## <a name="general-questions"></a>一般問題

**Q: What guidance should users be given on how to select a secure password?**

下列連結是目前 Microsoft 中有關本主題的指引：

[Microsoft 密碼指引](https://www.microsoft.com/research/publication/password-guidance)

**Q: Is on-premises Azure AD Password Protection supported in non-public clouds?**

否 - 僅公用雲端支援內部部署 Azure AD 密碼保護。 尚未宣告非公用雲端可支援的日期。

The Azure AD portal does allow modification of the on-premises-specific "Password protection for Windows Server Active Directory" configuration even in non-public clouds; such changes will be persisted but otherwise will never take effect. Registration of on-premises proxy agents or forests is unsupported when non-public cloud credentials are used, and any such registration attempts will always fail.

**Q: How can I apply Azure AD Password Protection benefits to a subset of my on-premises users?**

不支援。 部署並啟用之後，Azure AD 密碼保護會一視同仁，所有使用者都會受惠於相同的安全性優點。

**Q: What is the difference between a password change and a password set (or reset)?**

A password change is when a user chooses a new password after proving they have knowledge of the old password. For example, a password change is what happens when a user logs into Windows and is then prompted to choose a new password.

A password set (sometimes called a password reset) is when an administrator replaces the password on an account with a new password, for example by using the Active Directory Users and Computers management tool. This operation requires a high level of privilege (usually Domain Admin), and the person performing the operation usually does not have knowledge of the old password. Help-desk scenarios often perform password sets, for instance when assisting a user who has forgotten their password. You will also see password set events when a brand new user account is being created for the first time with a password.

The password validation policy behaves the same regardless of whether a password change or set is being done. The Azure AD Password Protection DC Agent service does log different events to inform you whether a password change or set operation was done.  See [Azure AD Password Protection monitoring and logging](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**Q: Why are duplicated password rejection events logged when attempting to set a weak password using the Active Directory Users and Computers management snap-in?**

The Active Directory Users and Computers management snap-in will first try to set the new password using the Kerberos protocol. Upon failure, the snap-in will make a second attempt to set the password using a legacy (SAM RPC) protocol (the specific protocols used are not important). If the new password is considered weak by Azure AD Password Protection, this snap-in behavior will result in two sets of password reset rejection events being logged.

**Q: Why are Azure AD Password Protection password validation events being logged with an empty user name?**

Active Directory supports the ability to test a password to see if it passes the domain's current password complexity requirements, for example using the [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api. When a password is validated in this way, the testing also includes validation by password-filter-dll based products such as Azure AD Password Protection - but the user names passed to a given password filter dll will be empty. In this scenario, Azure AD Password Protection will still validate the password using the currently in-effect password policy and will issue an event log message to capture the outcome, however the event log message will have empty user name fields.

**Q: Is it supported to install Azure AD Password Protection side by side with other password-filter-based products?**

可以。 核心 Windows 功能之一就是支援多個已註冊的密碼篩選 dll，因此並不限於 Azure AD 密碼保護。 所有註冊的密碼篩選 dll 必須先同意才能接受密碼。

**Q: How can I deploy and configure Azure AD Password Protection in my Active Directory environment without using Azure?**

不支援。 Azure AD 密碼保護是 Azure 功能，可延伸至內部部署 Active Directory 環境。

**Q: How can I modify the contents of the policy at the Active Directory level?**

不支援。 The policy can only be administered using the Azure AD portal. 另請參閱上一個問題。

**問：為什麼 sysvol 複寫需要 DFSR？**

FRS (DFSR 之前的技術) 有許多已知問題，而且在更新版本的 Windows Server Active Directory 中完全不支援。 在 FRS 設定的網域上，完全不會執行任何 Azure AD 密碼保護測試。

如需詳細資訊，請參閱下列文章：

[將 sysvol 複寫移轉至 DFSR 的案例](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr) \(英文\)

[FRS 終章將至](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs) \(英文\)

If your domain is not already using DFSR, you MUST migrate it to use DFSR before installing Azure AD Password Protection. For more information, see the following link:

[SYSVOL Replication Migration Guide: FRS to DFS Replication](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> The Azure AD Password Protection DC Agent software will currently install on domain controllers in domains that are still using FRS for sysvol replication, but the software will NOT work properly in this environment. Additional negative side-effects include individual files failing to replicate, and sysvol restore procedures appearing to succeed but silently failing to replicate all files. You should migrate your domain to use DFSR as soon as possible, both for DFSR's inherent benefits and also to unblock the deployment of Azure AD Password Protection. Future versions of the software will be automatically disabled when running in a domain that is still using FRS.

**Q: How much disk space does the feature require on the domain sysvol share?**

精確的空間使用量會因為一些因素而有所不同，例如 Microsoft 全域禁用清單和每個租用戶自訂清單中禁用的權杖數量和長度，以及加密的額外負荷。 這些清單的內容很可能在未來繼續增加。 有鑑於此，我們合理預期此功能在網域 sysvol 共用上至少需要五 (5) MB 的空間。

**問：為什麼需要重新開機才能安裝或升級 DC 代理程式軟體？**

這項需求是核心 Windows 行為所造成。

**問：是否有任何方法可將 DC 代理程式設定為使用特定 Proxy 伺服器？**

不會。 由於 Proxy 伺服器是無狀態的，因此使用哪一個特定 Proxy 伺服器並不重要。

**Q: Is it okay to deploy the Azure AD Password Protection Proxy service side by side with other services such as Azure AD Connect?**

可以。 Azure AD 密碼保護 Proxy 服務與 Azure AD Connect 之間永遠不會產生直接衝突。

Unfortunately, an incompatibility has been found between the version of the Microsoft Azure AD Connect Agent Updater service that is installed by the Azure AD Password Protection Proxy software and the version of the service that is installed by the [Azure Active Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) software. This incompatibility may result in the Agent Updater service being unable to contact Azure for software updates. It is not recommended to install Azure AD Password Protection Proxy and Azure Active Directory Application Proxy on the same machine.

**Q: In what order should the DC agents and proxies be installed and registered?**

Any ordering of Proxy agent installation, DC agent installation, forest registration, and Proxy registration  is supported.

**Q: Should I be concerned about the performance hit on my domain controllers from deploying this feature?**

在狀況良好的現有 Active Directory 部署中，Azure AD 密碼保護 DC 代理程式服務應不會大幅影響網域控制站效能。

對大部分的 Active Directory 部署而言，任何指定網域控制站上的密碼變更作業都是整體工作負載的一小部分。 例如，假設某個 Active Directory 網域有 10000 個使用者帳戶，而 MaxPasswordAge 原則設為 30 天。 平均而言，此網域每天會看到 10000/30=~333 個密碼變更作業，甚至對單一網域控制站而言，這都是很小的作業量。 考慮到可能最糟糕的情況：假設單一 DC 上的這 ~333 個密碼變更作業需要在一小時左右完成。 例如，許多員工都在星期一早上進行工作時，就可能會發生此情況。 即使在該情況下，我們仍然可看到每分鐘有 ~333/60 分鐘 = 6 個密碼變更作業，這依然不是龐大的負載。

不過，如果您目前的網域控制站已在效能受限的層級上執行 (例如 CPU、磁碟空間和磁碟 I/O 等已達到極限)，建議您先新增其他網域控制站或擴展可用磁碟空間，然後再部署這項功能。 另請參閱上面有關 Sysvol 磁碟空間使用量的問題。

**Q: I want to test Azure AD Password Protection on just a few DCs in my domain. Is it possible to force user password changes to use those specific DCs?**

不會。 使用者變更其密碼時，Windows 用戶端 OS 會控制要使用哪一個網域控制站。 The domain controller is selected based on factors such as Active Directory site and subnet assignments, environment-specific network configuration, etc. Azure AD Password Protection does not control these factors and cannot influence which domain controller is selected to change a user's password.

可部分達成此目標的方法之一是，在指定 Active Directory 站台中的所有網域控制站上部署 Azure AD 密碼保護。 此方法合理地將指派至該站台的 Windows 用戶端涵蓋在一個範圍內，因此也涵蓋登入這些用戶端並變更其密碼的使用者。

**Q: If I install the Azure AD Password Protection DC Agent service on just the Primary Domain Controller (PDC), will all other domain controllers in the domain also be protected?**

不會。 當非 PDC 網域控制站上的使用者密碼變更時，純文字密碼永遠不會傳送到 PDC (這是常見的錯誤觀念)。 當指定 DC 接受新密碼後，此 DC 會使用該密碼來建立該密碼的各種驗證通訊協定特有雜湊，然後在目錄中保存這些雜湊。 純文字密碼不會保存。 已更新的雜湊接著會複寫到 PDC。 在某些情況下，使用者密碼可能會直接在 PDC 上變更，這也是取決於各種因素，例如網路拓樸和 Active Directory 站台的設計。 (請參閱上一個問題。)

總之，在 PDC 上部署 Azure AD 密碼保護 DC 代理程式服務時，就必須達到此功能在網域間的 100% 安全性涵蓋範圍。 只在 PDC 上部署此功能並不會使網域中其他 DC 享有 Azure AD 密碼保護安全性的優勢。

**Q: Why is custom smart lockout not working even after the agents are installed in my on-premises Active Directory environment?**

Custom smart lockout is only supported in Azure AD. Changes to the custom smart lockout settings in the Azure AD portal have no effect on the on-premises Active Directory environment, even with the agents installed.

**Q: Is a System Center Operations Manager management pack available for Azure AD Password Protection?**

不會。

**Q: Why is Azure AD still rejecting weak passwords even though I've configured the policy to be in Audit mode?**

Audit mode is only supported in the on-premises Active Directory environment. Azure AD is implicitly always in "enforce" mode when it evaluates passwords.

**Q: My users see the traditional Windows error message when a password is rejected by Azure AD Password Protection. Is it possible to customize this error message so that users know what really happened?**

不會。 The error message seen by users when a password is rejected by a domain controller is controlled by the client machine, not by the domain controller. This behavior happens whether a password is rejected by the default Active Directory password policies or by a password-filter-based solution such as Azure AD Password Protection.

## <a name="additional-content"></a>其他內容

下列連結並不屬於核心的 Azure AD 密碼保護文件，但可能是適用於此功能的其他資訊來源。

[Azure AD Password Protection is now generally available!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Email Phishing Protection Guide – Part 15: Implement the Microsoft Azure AD Password Protection Service (for On-Premises too!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD 密碼保護和智慧鎖定現在已開放公開預覽！](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>可用的 Microsoft 頂級\統一支援訓練

如果您想要深入了解 Azure AD 密碼保護，並將其部署在您的環境中，可利用提供給頂級或統一支援合約客戶的 Microsoft 主動式服務。 The service is called Azure Active Directory: Password Protection. 如需詳細資訊，請連絡技術支援專案經理。

## <a name="next-steps"></a>後續步驟

如果在這裡找不到您內部部署 Azure AD 密碼保護問題的解答，請在下面的意見項目提交，感謝您！

[部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
