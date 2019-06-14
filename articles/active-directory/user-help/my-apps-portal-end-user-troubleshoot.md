---
title: 取得我的應用程式入口網站-Azure Active Directory 的協助 |Microsoft Docs
description: 取得與登入，然後在 My Apps 入口網站中執行一般工作的說明。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e87ae0a8adf28176d9a97cbf1b78943179884a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60475022"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>針對使用 My Apps 入口網站的問題進行疑難排解
如果您遇到問題，登入，或使用**My Apps**入口網站中，請嘗試下列疑難排解秘訣之前您可以連絡服務台或系統管理員尋求協助。

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>我無法安裝 My Apps 安全登入延伸模組
如果您遇到安裝 My Apps 安全登入延伸模組的問題：

- 請確定您使用支援的瀏覽器，包括：

    - **Microsoft Edge.** 執行 Windows 10 Anniversary Edition 或更新版本。
    - **Google Chrome。** 執行 Windows 7 或更新版本，和在 Mac OS X 或更新版本上。
    - **Mozilla Firefox 26.0 或更新版本。** 在 Windows XP SP2 或更新版本，和在 Mac OS X 10.6 或更新版本執行。
    - **Internet Explorer 11。** 在 Windows 7 或更新版本 （支援有限） 上執行。

- 請確定您的瀏覽器延伸模組設定已開啟。

- 請嘗試重新啟動您的瀏覽器，並登入**My Apps**入口網站一次。

- 請嘗試清除瀏覽器的 cookie，然後重新啟動並登入**My Apps**入口網站一次。

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>我無法登入**My Apps**入口網站
如果您有登入的問題**My Apps**入口網站中，您可以嘗試下列：

- 請確定您使用正確的 URL。 它應該是 https://myapps.microsoft.com 或自訂的頁面，為您的組織，例如 https://myapps.microsoft.com/contoso.com 。

- 請確定您的密碼正確，且尚未過期。 如需詳細資訊，請參閱 <<c0> [ 重設工作或學校密碼](active-directory-passwords-update-your-own-password.md)。

- 請確定您的驗證資訊是最新且正確。 如需詳細資訊，請參閱 < [Azure Multi-factor Authentication 什麼適合我？](multi-factor-authentication-end-user.md)或是[變更您的安全性資訊的方法和資訊](security-info-add-update-methods-overview.md)。

- 新增**我的應用程式**入口網站 URL**網際網路內容 > 安全性 > 信任的網站**設定。

- 清除您的瀏覽器快取，然後再試一次登入。

## <a name="my-password-isnt-working"></a>我的密碼無法運作
如果您忘記密碼，永遠不會收到您的組織、 系統鎖定了您的帳戶，或想要變更您的密碼，請參閱[忘記 Azure AD 密碼的說明，請](active-directory-passwords-update-your-own-password.md)。

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>我想要能夠重設自己的密碼
若要重設自己的密碼，您的系統管理員必須先開啟功能為您的組織，然後您必須更新，並確認您所需的驗證方法。 如需如何更新您的驗證方法的詳細資訊，請參閱[註冊自助式密碼重設](active-directory-passwords-reset-register.md)。

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>我啟動應用程式時收到拒絕存取訊息
如果您收到**拒絕存取**訊息之後，啟動應用程式**My App**入口網站中，您可以嘗試下列：

- 請確定您已安裝[My Apps 安全登入延伸模組](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)和您使用[支援的瀏覽器](my-apps-portal-end-user-access.md#supported-browsers)。

- 請確定您應用程式中，使用正確的 URL 和 URL 是在您**網際網路內容 > 安全性 > 信任的網站**清單。

- 請確定您的密碼正確，且尚未過期。 如需詳細資訊，請參閱 <<c0> [ 重設工作或學校密碼](active-directory-passwords-update-your-own-password.md)。

- 請確定您的驗證資訊是最新且正確。 如需詳細資訊，請參閱 < [Azure Multi-factor Authentication 什麼適合我？](multi-factor-authentication-end-user.md)或是[變更您的安全性資訊的方法和資訊](security-info-add-update-methods-overview.md)。

- 清除您的瀏覽器快取，然後再試一次登入。

如果在嘗試這些項目後您仍無法存取您的應用程式，您必須連絡貴組織的技術支援人員尋求協助。

## <a name="next-steps"></a>後續步驟
登入之後**My Apps**入口網站中，您也可以更新您的設定檔和帳戶資訊、 群組的詳細資訊及存取權檢閱的資訊 （如果您有權限）。

- [存取和使用 My Apps 入口網站上的應用程式](my-apps-portal-end-user-access.md)。

- [變更您的設定檔資訊](my-apps-portal-end-user-update-profile.md)。

- [檢視和更新您群組相關的資訊](my-apps-portal-end-user-groups.md)。

- [執行您自己的存取權檢閱](my-apps-portal-end-user-access-reviews.md)。