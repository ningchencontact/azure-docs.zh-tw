---
title: SAML 單一登入內部部署應用程式與 Azure Active Directory 應用程式 Proxy （預覽） |Microsoft Docs
description: 了解如何提供單一登入內部部署上透過使用 SAML 驗證保護的應用程式 Proxy 發佈應用程式。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e103604af7aba2a0ef2e3d0e02a721ae4740c40
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59493768"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>SAML 單一登入內部部署應用程式使用應用程式 Proxy （預覽）

您可以使用 SAML 驗證保護，並提供這些應用程式可以透過應用程式 Proxy 的遠端存取的內部部署應用程式提供單一登入 (SSO)。 使用 SAML 單一登入，Azure Active Directory (Azure AD) 應用程式使用驗證使用者的 Azure AD 帳戶。 Azure AD 與應用程式透過連線通訊協定，進行登入資訊通訊。 您也可以將使用者對應至特定的應用程式角色根據您在 SAML 宣告中定義的規則。 藉由啟用應用程式 Proxy，除了 SAML SSO 使用者會有外部存取應用程式和無縫式 SSO 的體驗。

應用程式必須能夠取用所簽發的 SAML 權杖**Azure Active Directory**。 此設定不適用於應用程式使用內部部署身分識別提供者。 針對這些案例，建議您檢閱[資源移轉至 Azure AD 的應用程式](migration-resources.md)。

使用應用程式 Proxy 的 SAML SSO 也適用於使用 SAML 權杖加密功能。 如需詳細資訊，請參閱 <<c0> [ 設定 Azure AD SAML 權杖加密](howto-saml-token-encryption.md)。

## <a name="publish-the-on-premises-application-with-application-proxy"></a>發行應用程式 Proxy 的內部部署應用程式

您可以在內部部署應用程式提供 SSO 之前，請確定您已啟用應用程式 Proxy 並已安裝連接器。 請參閱[在 Azure AD 中新增透過應用程式 Proxy 遠端存取內部部署應用程式](application-proxy-add-on-premises-application.md)若要了解如何。

請記住下列時您會透過本教學課程：

* 在本教學課程中發佈您的應用程式，根據指示進行。 請務必選取**Azure Active Directory**作為**預先驗證**應用程式的方法 (步驟 4[新增至 Azure AD 的內部部署應用程式](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad
))。
* 複製**外部 URL**應用程式。
* 最佳做法，使用自訂網域盡可能以最佳的使用者體驗。 深入了解[使用的 Azure AD 應用程式 Proxy 中的自訂網域](application-proxy-configure-custom-domain.md)。
* 將至少一個使用者新增至應用程式，請確定測試帳戶具有存取內部部署應用程式。 使用測試帳戶，如果您可以透過應用程式，請造訪**外部 URL**來驗證應用程式 Proxy 已正確設定。 如需疑難排解資訊，請參閱[疑難排解應用程式 Proxy 問題和錯誤訊息](application-proxy-troubleshoot.md)。

## <a name="set-up-saml-sso"></a>設定 SAML SSO

1. 在 Azure 入口網站中，選取**Azure Active Directory > 企業應用程式**從清單中選取的應用程式。
1. 從應用程式的**概觀**頁面上，選取**單一登入**。
1. 選取  **SAML**做為單一登入方法。
1. 在**設定單一登入使用 SAML 設定**頁面上，編輯**基本 SAML 組態**資料，並遵循[Enter 基本 SAML 組態](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on)若要設定 SAML 型應用程式的驗證。

   * 請確定**回覆 URL**比對，或者是在路徑**外部 URL**您透過應用程式 Proxy 發佈的內部部署應用程式。 如果您的應用程式需要不同**回覆 URL** SAML 設定時，將此做**第一次**清單中保留的 URL**外部 URL**做為額外的 URL，排序在第一個之後。
   * 請確認應用程式也已指定正確**回覆 URL**或判斷提示取用者服務 URL，用來接收驗證權杖。

     ![輸入基本的 SAML 組態資料](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)

    > [!NOTE]
    > 如果後端應用程式預期**回覆 URL**是內部的 URL，您必須在使用者的裝置上安裝 My Apps 安全登入延伸模組。 此延伸模組會自動重新導向至適當的應用程式 Proxy 服務。 若要安裝擴充功能，請參閱[My Apps 安全登入延伸模組](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)。

## <a name="test-your-app"></a>測試應用程式

當您完成所有這些步驟時，您的應用程式應該啟動並執行。 若要測試應用程式：

1. 開啟瀏覽器並瀏覽至您發佈應用程式時所建立的外部 URL。 
1. 使用您指派給應用程式的測試帳戶來登入。 您應該能夠載入應用程式，並擁有 SSO 應用程式。

## <a name="next-steps"></a>後續步驟

- [Azure AD 應用程式 Proxy 如何提供單一登入？](application-proxy-single-sign-on.md)
- [疑難排解應用程式 Proxy](application-proxy-troubleshoot.md)
