---
title: 從 Azure AD 應用程式庫設定應用程式的 One Click SSO | Microsoft Docs
description: 從 Azure AD 應用程式庫設定應用程式的 One Click SSO 的步驟。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 358240823da469551e254356fc0613bea20d78c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064812"
---
# <a name="one-click-sso-feature-for-azure-ad-gallery-applications"></a>Azure AD 資源庫應用程式的 One Click SSO 功能

 在本教學課程中，您將了解如何針對可提供 SSO 設定 UI 的所有 SAML 應用程式執行 One Click SSO。

## <a name="introduction-to-one-click-sso"></a>One Click SSO 簡介

我們引進了 One Click SSO 功能，以針對可支援 SAML 通訊協定的 Azure AD 資源庫應用程式設定單一登入。 在 Azure AD SSO 設定頁面上，我們提供了這個選項，讓客戶能自動在應用程式端設定 Azure AD 中繼資料。 目標是要協助客戶以最低限度的手動工作快速設定 SSO。 

## <a name="advantages-of-the-one-click-sso"></a>One Click SSO 的優點

- 快速設定資源庫應用程式的 SSO (客戶必須先在應用程式端上進行手動設定)。
- 更有效且精確的設定方式。
- 設定時不需要合作夥伴通訊或支援，因為應用程式會提供用於設定 SAML 的 UI。

## <a name="prerequisites"></a>必要條件

- 應用程式的有效訂用帳戶，以及您想要透過 OneClick SSO 設定的管理員認證。
- 在瀏覽器中安裝 Microsoft 所提供的**我的應用程式安全登入瀏覽器擴充功能**。 如果您想要深入了解此擴充功能，請參考這個[連結](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)。

## <a name="one-click-sso-feature-step-by-step-details"></a>One Click SSO 功能逐步詳細資料

1. 從 Azure AD 應用程式庫新增應用程式。

2. 按一下 [單一登入]。

3. 按一下 [啟用單一登入]。

4. 在 [基本 SAML 組態] 區段中填入必要的組態值。

    > [!NOTE] 
    > 如果應用程式需要設定自訂宣告，請在執行 OneClick SSO 之前先進行設定。

5. 如有任何資源庫應用程式實作 One Click SSO 功能，您會看到以下畫面。 如果尚未安裝**我的應用程式安全登入瀏覽器擴充功能**，您需要按一下 [安裝擴充功能]  選項。

    ![安裝我的應用程式安全登入瀏覽器擴充功能](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. 將擴充功能新增至瀏覽器之後，按一下 [設定應用程式名稱]  ，這會將您重新導向至應用程式管理員入口網站。 您必須以系統管理員身分登入，才能進入應用程式。

    ![設定應用程式名稱](./media/one-click-sso-tutorial/setup-sso.png)

7. 瀏覽器擴充功能現在會自動為您設定應用程式。 如果您想要繼續執行，它會先要求您確認。 按一下 [是]  。

    ![儲存自動填入的資料](./media/one-click-sso-tutorial/save-autopopulate.png)

    > [!NOTE]
    > 如果任何應用程式需要額外的瀏覽或步驟，您應會看到要求您執行這些步驟的適當訊息。 

8. 完成設定後，按一下 [確定]  以儲存變更。

    ![儲存自動填入的資料](./media/one-click-sso-tutorial/save-data.png)

9. 隨即顯示成功的確認快顯訊息，您的 SSO 設定便已設定成功。 您可以接著測試應用程式。

    ![已設定 SSO](./media/one-click-sso-tutorial/sso-configured.png)

10. 順利完成設定後，應用程式會登出，而您會回到 Azure 入口網站。

11. 您可以按一下 [測試] 按鈕來測試單一登入。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [什麼是我的應用程式安全登入瀏覽器擴充功能](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 