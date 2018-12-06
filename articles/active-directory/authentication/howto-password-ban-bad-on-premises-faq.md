---
title: 內部部署 Azure AD 密碼保護常見問題集
description: 內部部署 Azure AD 密碼保護常見問題集
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: d3d42a3c81153d54690f0825368eaa950dbac18e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314774"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>預覽：Azure AD 密碼保護內部部署 - 常見問題集

|     |
| --- |
| Azure AD 密碼保護是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>一般問題

**問：Azure AD 密碼保護何時公開上市 (GA)？**

我們尚未宣布公開上市日期。

**問：非公用雲端是否支援內部部署 Azure AD 密碼保護？**

否 - 僅公用雲端支援內部部署 Azure AD 密碼保護。

**問：如何讓內部部署使用者的子集受益於 Azure AD 密碼保護？**

不支援。 部署並啟用之後，Azure AD 密碼保護會一視同仁，所有使用者都會受惠於相同的安全性優點。

**問：是否支援同時安裝 Azure AD 密碼保護與其他密碼篩選產品？**

是。 核心 Windows 功能之一就是支援多個已註冊的密碼篩選 dll，因此並不限於 Azure AD 密碼保護。 所有註冊的密碼篩選 dll 必須先同意才能接受密碼。

**問：為什麼 sysvol 複寫需要 DFSR？**

FRS (DFSR 之前的技術) 有許多已知問題，而且在更新版本的 Windows Server Active Directory 中完全不支援。 在 FRS 設定的網域上，完全不會執行任何 Azure AD 密碼保護測試。

如需詳細資訊，請參閱下列文章：

[將 sysvol 複寫移轉至 DFSR 的案例](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr) \(英文\)

[FRS 終章將至](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs) \(英文\)

**問：為什麼需要重新開機才能安裝或升級 DC 代理程式軟體？**

這項需求是核心 Windows 行為所造成。

**問：是否有任何方法可將 DC 代理程式設定為使用特定 Proxy 伺服器？**

否。

## <a name="next-steps"></a>後續步驟

如果在這裡找不到您內部部署 Azure AD 密碼保護問題的解答，請在下面的意見項目提交，感謝您！

[部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
