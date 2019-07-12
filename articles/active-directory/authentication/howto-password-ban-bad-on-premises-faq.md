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
ms.openlocfilehash: 3b4879093ed80a554219b053cc5a2bc895126725
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702899"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Azure AD 密碼保護內部部署 - 常見問題集

## <a name="general-questions"></a>一般問題

**問：使用者可以使用哪些指引來了解如何選取安全密碼？**

下列連結是目前 Microsoft 中有關本主題的指引：

[Microsoft 密碼指引](https://www.microsoft.com/research/publication/password-guidance)

**問：非公用雲端是否支援內部部署 Azure AD 密碼保護？**

否 - 僅公用雲端支援內部部署 Azure AD 密碼保護。 尚未宣告非公用雲端可支援的日期。

**問：如何讓內部部署使用者的子集受益於 Azure AD 密碼保護？**

不支援。 部署並啟用之後，Azure AD 密碼保護會一視同仁，所有使用者都會受惠於相同的安全性優點。

**問：密碼變更和已設定密碼 （或重設） 之間的差異為何？**

密碼變更時，使用者選擇新密碼之後證明他們已經知道舊密碼。 比方說，這是在使用者登入 Windows，而且系統會接著提示選擇新密碼時，會發生什麼事。

（有時稱為 「 密碼重設 」） 已設定密碼時，系統管理員帳戶的密碼以取代新的密碼，例如使用 Active Directory 使用者和電腦 管理工具。 這項作業需要高層級的權限 （通常是網域系統管理員），而且通常執行作業的人員沒有舊密碼的知識。 技術服務人員案例通常這樣做，例如當協助忘記密碼的使用者。 您也會看到 設定事件時正在第一次使用密碼建立全新的使用者帳戶的密碼。

密碼驗證原則的行為相同，不論是否已完成密碼變更或設定。 Azure AD 密碼保護 DC 代理程式服務會記錄不同的事件，通知您是否變更密碼，或設定作業已完成。  請參閱[監視和記錄的 Azure AD 密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor)。

**問：嘗試設定弱式密碼時所記錄的重複的密碼拒絕事件為何要使用 [Active Directory 使用者和電腦管理] 嵌入式管理單元？**

Active Directory 使用者和電腦管理 嵌入式管理單元會先嘗試設定新的密碼使用 Kerberos 通訊協定。 在失敗時嵌入式管理單元會讓第二個嘗試設定使用舊版 (SAM RPC) 通訊協定 （使用特定的通訊協定不重要） 的密碼。 如果新的密碼被視為弱式 Azure AD 密碼保護，這會導致兩組記錄的密碼重設拒絕事件。

**問：是否支援同時安裝 Azure AD 密碼保護與其他密碼篩選產品？**

是的。 核心 Windows 功能之一就是支援多個已註冊的密碼篩選 dll，因此並不限於 Azure AD 密碼保護。 所有註冊的密碼篩選 dll 必須先同意才能接受密碼。

**問：如果不使用 Azure，我要如何在 Active Directory 環境中部署和設定 Azure AD 密碼保護？**

不支援。 Azure AD 密碼保護是 Azure 功能，可延伸至內部部署 Active Directory 環境。

**問：如何修改 Active Directory 層級上的原則內容？**

不支援。 您只可以使用 Azure AD 管理入口網站來管理原則。 另請參閱上一個問題。

**問：為什麼 sysvol 複寫需要 DFSR？**

FRS (DFSR 之前的技術) 有許多已知問題，而且在更新版本的 Windows Server Active Directory 中完全不支援。 在 FRS 設定的網域上，完全不會執行任何 Azure AD 密碼保護測試。

如需詳細資訊，請參閱下列文章：

[將 sysvol 複寫移轉至 DFSR 的案例](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr) \(英文\)

[FRS 終章將至](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs) \(英文\)

**問：此功能在網域 Sysvol 共用上需要多少磁碟空間？**

精確的空間使用量會因為一些因素而有所不同，例如 Microsoft 全域禁用清單和每個租用戶自訂清單中禁用的權杖數量和長度，以及加密的額外負荷。 這些清單的內容很可能在未來繼續增加。 有鑑於此，我們合理預期此功能在網域 sysvol 共用上至少需要五 (5) MB 的空間。

**問：為什麼需要重新開機才能安裝或升級 DC 代理程式軟體？**

這項需求是核心 Windows 行為所造成。

**問：是否有任何方法可將 DC 代理程式設定為使用特定 Proxy 伺服器？**

資料分割 由於 Proxy 伺服器是無狀態的，因此使用哪一個特定 Proxy 伺服器並不重要。

**問：Azure AD 密碼保護 Proxy 服務是否可以與其他服務 (例如 Azure AD Connect) 一起部署？**

是的。 Azure AD 密碼保護 Proxy 服務與 Azure AD Connect 之間永遠不會產生直接衝突。

**問：以何種順序應該 DC 代理程式和 proxy 安裝並註冊嗎？**

支援的 Proxy 代理程式安裝、 DC 代理程式安裝、 樹系註冊和 Proxy 註冊的任何排序。

**問：是否應該顧慮到部署此功能時，對網域控制站造成的效能衝擊？**

在狀況良好的現有 Active Directory 部署中，Azure AD 密碼保護 DC 代理程式服務應不會大幅影響網域控制站效能。

對大部分的 Active Directory 部署而言，任何指定網域控制站上的密碼變更作業都是整體工作負載的一小部分。 例如，假設某個 Active Directory 網域有 10000 個使用者帳戶，而 MaxPasswordAge 原則設為 30 天。 平均而言，此網域每天會看到 10000/30=~333 個密碼變更作業，甚至對單一網域控制站而言，這都是很小的作業量。 考慮到可能最糟糕的情況：假設單一 DC 上的這 ~333 個密碼變更作業需要在一小時左右完成。 例如，許多員工都在星期一早上進行工作時，就可能會發生此情況。 即使在該情況下，我們仍然可看到每分鐘有 ~333/60 分鐘 = 6 個密碼變更作業，這依然不是龐大的負載。

不過，如果您目前的網域控制站已在效能受限的層級上執行 (例如 CPU、磁碟空間和磁碟 I/O 等已達到極限)，建議您先新增其他網域控制站或擴展可用磁碟空間，然後再部署這項功能。 另請參閱上面有關 Sysvol 磁碟空間使用量的問題。

**問：我只想在網域中的幾個 DC 上測試 Azure AD 密碼保護。有可能強制使用這些特定 DC 來進行使用者密碼變更作業嗎？**

資料分割 使用者變更其密碼時，Windows 用戶端 OS 會控制要使用哪一個網域控制站。 網域控制站的選取會取決於 Active Directory 站台和子網路指派，以及環境特有網路設定等因素。Azure AD 密碼保護不會控制這些因素，也無法影響要選取哪個網域控制站來變更使用者密碼。

可部分達成此目標的方法之一是，在指定 Active Directory 站台中的所有網域控制站上部署 Azure AD 密碼保護。 此方法合理地將指派至該站台的 Windows 用戶端涵蓋在一個範圍內，因此也涵蓋登入這些用戶端並變更其密碼的使用者。

**問：如果我只在主要網域控制站 (PDC) 上安裝 Azure AD 密碼保護 DC 代理程式服務，網域中的所有其他網域控制站也可受到保護嗎？**

資料分割 當非 PDC 網域控制站上的使用者密碼變更時，純文字密碼永遠不會傳送到 PDC (這是常見的錯誤觀念)。 當指定 DC 接受新密碼後，此 DC 會使用該密碼來建立該密碼的各種驗證通訊協定特有雜湊，然後在目錄中保存這些雜湊。 純文字密碼不會保存。 已更新的雜湊接著會複寫到 PDC。 在某些情況下，使用者密碼可能會直接在 PDC 上變更，這也是取決於各種因素，例如網路拓樸和 Active Directory 站台的設計。 (請參閱上一個問題。)

總之，在 PDC 上部署 Azure AD 密碼保護 DC 代理程式服務時，就必須達到此功能在網域間的 100% 安全性涵蓋範圍。 只在 PDC 上部署此功能並不會使網域中其他 DC 享有 Azure AD 密碼保護安全性的優勢。

**問：System Center Operations Manager 管理組件可用於 Azure AD 密碼保護嗎？**

資料分割

**問：為什麼 Azure 仍拒絕弱式密碼即使我已設定的原則處於稽核模式？**

稽核模式只有內部部署 Active Directory 環境中執行。 Azure 會隱含地一律是在 「 強制執行 」 模式時，它會評估密碼。

## <a name="additional-content"></a>其他內容

下列連結並不屬於核心的 Azure AD 密碼保護文件，但可能是適用於此功能的其他資訊來源。

[Azure AD 密碼保護現已正式推出 ！](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[電子郵件網路釣魚防護指南 – 第 15 節：實作 Microsoft Azure AD 密碼保護服務 (也適用於內部部署環境！)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD 密碼保護和智慧鎖定現在已開放公開預覽！](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>可用的 Microsoft 頂級\統一支援訓練

如果您想要深入了解 Azure AD 密碼保護，並將其部署在您的環境中，可利用提供給頂級或統一支援合約客戶的 Microsoft 主動式服務。 此服務稱為 Azure Active Directory：密碼保護。 如需詳細資訊，請連絡技術支援專案經理。

## <a name="next-steps"></a>後續步驟

如果在這裡找不到您內部部署 Azure AD 密碼保護問題的解答，請在下面的意見項目提交，感謝您！

[部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
