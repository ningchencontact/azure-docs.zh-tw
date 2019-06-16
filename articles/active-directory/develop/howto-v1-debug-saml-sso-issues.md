---
title: 針對 SAML 型單一登入進行偵錯 - Azure Active Directory | Microsoft Docs
description: 針對 Azure Active Directory 中應用程式的 SAML 型單一登入進行偵錯。
services: active-directory
author: rwike77
documentationcenter: na
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, smalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0273a0d35d2b4d69f74b1acd8bc2b1d7174810cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111475"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>針對 Azure Active Directory 中應用程式的 SAML 型單一登入進行偵錯

了解如何在 Azure Active Directory (Azure AD) 中支援[安全性聲明標記語言 (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language) 的應用程式內尋找[單一登入](../manage-apps/what-is-single-sign-on.md)問題，並加以修正。 

## <a name="before-you-begin"></a>開始之前

建議您安裝 [My Apps 安全登入擴充功能](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension)。 此瀏覽器延伸模組可讓您輕鬆採集 SAML 要求和您所需要的 SAML 回應資訊來解決問題的單一登入。 如果您無法安裝此擴充功能，本文會說明如何在已安裝/未安裝此擴充功能的情況下解決問題。

若要下載並安裝 My Apps 安全登入擴充功能，請使用下列其中一個連結。

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>測試 SAML 型單一登入

若要測試 SAML 型單一登入 Azure ad 和目標應用程式：

1. 以全域系統管理員或其他已獲得授權可管理應用程式的系統管理員身分，登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左刀鋒視窗中，選取**Azure Active Directory**，然後選取**企業應用程式**。 
1. 企業應用程式清單中，選取您要測試單一登入，然後從左邊選取的選項的應用程式**單一登入**。
1. 若要開啟 SAML 型單一登入測試經驗，請前往**測試單一登入**（步驟 5）。 如果**測試** 按鈕會呈現灰色，您必須第一次填入出，並將必要的屬性儲存**基本 SAML 組態**一節。
1. 在 [測試單一登入]  刀鋒視窗中，使用公司的認證來登入目標應用程式。 您可以使用目前使用者或不同使用者的身分來登入。 如果您使用不同使用者的身分登入，會有提示要求您進行驗證。

    ![測試 SAML 頁面](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)


如果您成功登入，就表示您已通過測試。 在此情況下，Azure AD 會向應用程式發出 SAML 回應權杖。 應用程式使用 SAML 權杖成功地將您登入。

如果您在公司的登入頁面或應用程式的頁面上遇到錯誤，請使用下列其中一節來解決錯誤。


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>解決公司登入頁面上的登入錯誤

當您嘗試登入時，您可能會看到類似下列的範例您公司登入頁面上的錯誤。

![登入錯誤](./media/howto-v1-debug-saml-sso-issues/error.png)

若要對這個錯誤進行偵錯，您需要錯誤訊息和 SAML 要求。 My Apps 安全登入擴充功能會自動收集此資訊，並在 Azure AD 上顯示解決指導方針。 

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>若要解決此登入錯誤，使用 My Apps 安全登入延伸模組安裝

1. 發生錯誤時，延伸模組會將您導向回 Azure AD**測試單一登入**刀鋒視窗。 
1. 在 **測試單一登入**刀鋒視窗中，選取**下載 SAML 要求**。 
1. 根據錯誤和 SAML 要求中的值，您應該會看到特定的解決指導方針。
1. 您會看到**修正此問題**按鈕，以自動更新以解決此問題的 Azure AD 中設定。 如果您沒有看到此按鈕，然後登入問題不因為在 Azure AD 上設定不正確。

如果沒有解決方式提供登入錯誤，我們建議您使用 [意見反應] 文字方塊中，以通知我們。

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>若要解決此錯誤，而不需要安裝 My Apps 安全登入延伸模組

1. 複製頁面右下角的錯誤訊息。 錯誤訊息包括：
    - 相互關聯識別碼和時間戳記。 在向 Microsoft 建立支援案例時務必要有這些值，原因是這些值可協助工程師識別問題並提供正確的問題解決方式。
    - 可識別問題根本原因的陳述。
1. 返回 Azure AD，並尋找 [測試單一登入]  刀鋒視窗。
1. 在 [取得解決指導方針]  上方的文字方塊中，貼上錯誤訊息。
1. 按一下 [取得解決指導方針]  以顯示問題的解決步驟。 此指導方針可能需要來自 SAML 要求或 SAML 回應的資訊。 如果您不使用 My Apps 安全登入延伸模組，您需要一種工具這類[Fiddler](https://www.telerik.com/fiddler)擷取 SAML 要求和回應。
1. 請確認 SAML 要求中的目的地會對應至 SAML 單一登入服務 URL 從 Azure AD 取得。
1. 請確認 SAML 要求中的簽發者是您已設定 Azure AD 中的應用程式相同的識別項。 Azure AD 會使用簽發者尋找您目錄中的應用程式。
1. 請確認 AssertionConsumerServiceURL 是應用程式預期接收來自 Azure AD 的 SAML 權杖。 您可以在 Azure AD 中設定此值，但它不是強制性如果它是 SAML 要求的一部分。


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>解決應用程式頁面上的登入錯誤

您可能會先成功登入，之後才在應用程式的頁面上看到錯誤。 當 Azure AD 核發權杖給應用程式，但應用程式不接受該回應時，就會發生這種情況。   

若要解決此錯誤，請依照下列步驟執行︰

1. 如果應用程式在 Azure AD 資源庫中，請確認您已遵循與 Azure AD 整合應用程式的所有步驟。 若要尋找應用程式的整合指示，請參閱 [SaaS 應用程式整合教學課程清單](../saas-apps/tutorial-list.md)。
1. 擷取 SAML 回應。
    - 如果您已安裝 My Apps 安全登入擴充功能，請從 [測試單一登入]  刀鋒視窗中，按一下 [下載 SAML 回應]  。
    - 如果未安裝此擴充功能，則請使用 [Fiddler](https://www.telerik.com/fiddler) 之類的工具來擷取 SAML 回應。 
1. 請注意 SAML 回應權杖中的這些元素：
   - NameID 值和格式的使用者唯一識別碼
   - 在權杖中所發出的宣告
   - 用來簽署權杖的憑證。 

     如需 SAML 回應的詳細資訊，請參閱[單一登入 SAML 通訊協定](single-sign-on-saml-protocol.md)。

1. 既然您已檢閱 SAML 回應，請參閱[登入後的應用程式的頁面上的錯誤](../manage-apps/application-sign-in-problem-application-error.md)如需如何解決此問題的指引。 
1. 如果您仍然無法成功登入，您可以要求應用程式廠商遺漏的項目從 SAML 回應。


## <a name="next-steps"></a>後續步驟

既然單一登入您的應用程式運作，您可以[自動化使用者佈建和取消佈建到 SaaS 應用程式](../manage-apps/user-provisioning.md)或是[開始使用條件式存取](../conditional-access/app-based-conditional-access.md)。
