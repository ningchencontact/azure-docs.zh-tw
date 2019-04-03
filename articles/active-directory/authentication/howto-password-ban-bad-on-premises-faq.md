---
title: 內部部署上的 Azure AD 密碼保護常見問題集-Azure Active Directory
description: 內部部署 Azure AD 密碼保護常見問題集
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8c3395345093ae9a3d35deb27a08f12d331c9f3
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861899"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Azure AD 密碼保護內部部署 - 常見問題集

## <a name="general-questions"></a>一般問題

**問：哪些指引應該會提供使用者如何選取安全的密碼？**

下列連結是目前 Microsoft 中有關本主題的指引：

[Microsoft 密碼指導方針](https://www.microsoft.com/en-us/research/publication/password-guidance)

**問：支援非公用雲端中的 Azure AD 密碼保護是在內部部署環境？**

否 - 僅公用雲端支援內部部署 Azure AD 密碼保護。 尚未宣告非公用雲端可支援的日期。

**問：如何套用至我的內部部署使用者的子集的 Azure AD 密碼保護優點？**

不支援。 部署並啟用之後，Azure AD 密碼保護會一視同仁，所有使用者都會受惠於相同的安全性優點。

**問：密碼變更和已設定密碼 （或重設） 之間的差異為何？**

密碼變更時，使用者選擇新密碼之後證明他們已經知道舊密碼。 比方說，這是在使用者登入 Windows，而且系統會接著提示選擇新密碼時，會發生什麼事。

（有時稱為 「 密碼重設 」） 已設定密碼時，系統管理員帳戶的密碼以取代新的密碼，例如使用 Active Directory 使用者和電腦 管理工具。 這項作業需要高層級的權限 （通常是網域系統管理員），而且通常執行作業的人員沒有舊密碼的知識。 技術服務人員案例通常這樣做，例如當協助忘記密碼的使用者。 您也會看到 設定事件時正在第一次使用密碼建立全新的使用者帳戶的密碼。

密碼驗證原則的行為相同，不論是否已完成密碼變更或設定。 Azure AD 密碼保護 DC 代理程式服務會記錄不同的事件，通知您是否變更密碼，或設定作業已完成。  請參閱[監視和記錄的 Azure AD 密碼保護](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor)。

**問：它支援安裝其他密碼篩選為基礎的產品與 Azure AD 密碼保護嗎？**

是。 核心 Windows 功能之一就是支援多個已註冊的密碼篩選 dll，因此並不限於 Azure AD 密碼保護。 所有註冊的密碼篩選 dll 必須先同意才能接受密碼。

**問：如何部署和設定 Azure AD 密碼保護我的 Active Directory 環境中，而不需使用 Azure？**

不支援。 Azure AD 密碼保護是 Azure 功能，可延伸至內部部署 Active Directory 環境。

**問：如何修改 Active Directory 層級原則的內容？**

不支援。 您只可以使用 Azure AD 管理入口網站來管理原則。 另請參閱上一個問題。

**問：DFSR 為什麼需要進行 sysvol 複寫？**

FRS (DFSR 之前的技術) 有許多已知問題，而且在更新版本的 Windows Server Active Directory 中完全不支援。 在 FRS 設定的網域上，完全不會執行任何 Azure AD 密碼保護測試。

如需詳細資訊，請參閱下列文章：

[DFSR 移轉 sysvol 複寫案例](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[即將結束，Nigh frs](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**問：在 網域 sysvol 共用將此功能需要在多少磁碟空間？**

精確的空間使用量會因為一些因素而有所不同，例如 Microsoft 全域禁用清單和每個租用戶自訂清單中禁用的權杖數量和長度，以及加密的額外負荷。 這些清單的內容很可能在未來繼續增加。 有鑑於此，我們合理預期此功能在網域 sysvol 共用上至少需要五 (5) MB 的空間。

**問：若要安裝或升級 DC 代理程式軟體為何需要重新開機？**

這項需求是核心 Windows 行為所造成。

**問：是否有任何 DC 代理程式設定為使用特定 proxy 伺服器的方法？**

沒有。 由於 Proxy 伺服器是無狀態的，因此使用哪一個特定 Proxy 伺服器並不重要。

**問：我是否可以部署其他服務，例如 Azure AD Connect 與 Azure AD 密碼保護 Proxy 服務？**

是。 Azure AD 密碼保護 Proxy 服務與 Azure AD Connect 之間永遠不會產生直接衝突。

**問：以何種順序應該 DC 代理程式和 proxy 安裝並註冊嗎？**

支援的 Proxy 代理程式安裝、 DC 代理程式安裝、 樹系註冊和 Proxy 註冊的任何排序。

**問：我是否應該擔心效能的衝擊我的網域控制站，從部署這項功能？**

在狀況良好的現有 Active Directory 部署中，Azure AD 密碼保護 DC 代理程式服務應不會大幅影響網域控制站效能。

對大部分的 Active Directory 部署而言，任何指定網域控制站上的密碼變更作業都是整體工作負載的一小部分。 例如，假設某個 Active Directory 網域有 10000 個使用者帳戶，而 MaxPasswordAge 原則設為 30 天。 平均而言，此網域每天會看到 10000/30=~333 個密碼變更作業，甚至對單一網域控制站而言，這都是很小的作業量。 考慮到可能最糟糕的情況：假設單一 DC 上的這 ~333 個密碼變更作業需要在一小時左右完成。 例如，許多員工都在星期一早上進行工作時，就可能會發生此情況。 即使在該情況下，我們仍然可看到每分鐘有 ~333/60 分鐘 = 6 個密碼變更作業，這依然不是龐大的負載。

不過，如果您目前的網域控制站已在效能受限的層級上執行 (例如 CPU、磁碟空間和磁碟 I/O 等已達到極限)，建議您先新增其他網域控制站或擴展可用磁碟空間，然後再部署這項功能。 另請參閱上面有關 Sysvol 磁碟空間使用量的問題。

**問：我只想在網域中的幾個 DC 上測試 Azure AD 密碼保護。 它可能會強制執行使用者密碼變更為使用這些特定的網域控制站嗎？**

沒有。 使用者變更其密碼時，Windows 用戶端 OS 會控制要使用哪一個網域控制站。 網域控制站的選取會取決於 Active Directory 站台和子網路指派，以及環境特有網路設定等因素。Azure AD 密碼保護不會控制這些因素，也無法影響要選取哪個網域控制站來變更使用者密碼。

可部分達成此目標的方法之一是，在指定 Active Directory 站台中的所有網域控制站上部署 Azure AD 密碼保護。 此方法合理地將指派至該站台的 Windows 用戶端涵蓋在一個範圍內，因此也涵蓋登入這些用戶端並變更其密碼的使用者。

**問：如果我安裝的 Azure AD 密碼保護 DC 代理程式服務上只是網域控制站 (PDC)，將網域中的所有其他網域控制站也受到保護嗎？**

沒有。 當非 PDC 網域控制站上的使用者密碼變更時，純文字密碼永遠不會傳送到 PDC (這是常見的錯誤觀念)。 當指定 DC 接受新密碼後，此 DC 會使用該密碼來建立該密碼的各種驗證通訊協定特有雜湊，然後在目錄中保存這些雜湊。 純文字密碼不會保存。 已更新的雜湊接著會複寫到 PDC。 在某些情況下，使用者密碼可能會直接在 PDC 上變更，這也是取決於各種因素，例如網路拓樸和 Active Directory 站台的設計。 (請參閱上一個問題。)

總之，在 PDC 上部署 Azure AD 密碼保護 DC 代理程式服務時，就必須達到此功能在網域間的 100% 安全性涵蓋範圍。 只在 PDC 上部署此功能並不會使網域中其他 DC 享有 Azure AD 密碼保護安全性的優勢。

**問：Azure AD 密碼保護的是 System Center Operations Manager 管理組件？**

沒有。

**問：為什麼 Azure 仍拒絕弱式密碼即使我已設定的原則處於稽核模式？**

稽核模式只有內部部署 Active Directory 環境中執行。 Azure 會隱含地一律是在 「 強制執行 」 模式時，它會評估密碼。

## <a name="additional-content"></a>其他內容

下列連結並不屬於核心的 Azure AD 密碼保護文件，但可能是適用於此功能的其他資訊來源。

[Azure AD 密碼保護現已正式推出 ！](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[電子郵件網路釣魚的防護指南 – 第 15 節：實作 Microsoft Azure AD 密碼保護服務 (如內部部署過 ！)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD 密碼保護與智慧鎖定正處於公開預覽狀態 ！](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>可用的 Microsoft 頂級\統一支援訓練

如果您想要深入了解 Azure AD 密碼保護，並將其部署在您的環境中，可利用提供給頂級或統一支援合約客戶的 Microsoft 主動式服務。 此服務稱為 Azure Active Directory：密碼保護。 如需詳細資訊，請連絡技術支援專案經理。

## <a name="next-steps"></a>後續步驟

如果在這裡找不到您內部部署 Azure AD 密碼保護問題的解答，請在下面的意見項目提交，感謝您！

[部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
