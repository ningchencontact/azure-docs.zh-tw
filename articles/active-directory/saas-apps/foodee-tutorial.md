---
title: 教學課程：Azure Active Directory 與 Foodee 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Foodee 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7e28bd4a-5624-4c67-aebb-0856d97e82c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c09752c1aa9840ab8dcdc1faa69222de4529644d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102149"
---
# <a name="tutorial-integrate-foodee-with-azure-active-directory"></a>教學課程：整合 Foodee 與 Azure Active Directory

在本教學課程中，您會了解如何將 Foodee 與 Azure Active Directory (Azure AD) 進行整合。 在整合 Foodee 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Foodee 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Foodee。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 啟用 Foodee 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 Foodee 支援 **SP 和 IDP** 起始的 SSO，並且支援 **Just In Time** 使用者佈建。

## <a name="adding-foodee-from-the-gallery"></a>從資源庫新增 Foodee

若要設定將 Foodee 整合到 Azure AD 中，您需要從資源庫將 Foodee 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Foodee**。
1. 從結果面板選取 [Foodee]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B. Simon** 的測試使用者，設定及測試與 Foodee 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Foodee 中相關使用者之間的連結關聯性。

若要設定及測試與 Foodee 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
1. **[設定 Foodee](#configure-foodee)** 以在應用程式端設定 SSO 設定。
1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B. Simon 測試 Azure AD 單一登入。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B. Simon 能夠使用 Azure AD 單一登入。
1. **[建立 Foodee 測試使用者](#create-foodee-test-user)** ，使 Foodee 中對應的 B. Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Foodee]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://concierge.food.ee/sso/saml/<INSTANCENAME>/consume`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://concierge.food.ee/sso/saml/<INSTANCENAME>`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的回覆 URL 與登入 URL 更新這些值。 請連絡 [Foodee 用戶端支援小組](mailto:dev@food.ee)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  來下載憑證，並將其儲存在電腦上。

   ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Foodee]  區段上，根據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-foodee"></a>設定 Foodee

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 Foodee。

1. 按一下頁面右上角的 [設定檔標誌]  ，然後瀏覽至 [單一登入]  並執行下列步驟：

   ![Foodee 設定](./media/foodee-tutorial/config01.png)

   1. 在 [IDP 名稱]  文字方塊中輸入名稱，例如：Azure。
   1. 在記事本中開啟同盟中繼資料 XML，將其內容複製並貼入 [IDP METADATA XML]  文字方塊。
   1. 按一下 [檔案]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 B. Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B. Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `BrittaSimon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Foodee 的存取權授與 B. Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Foodee]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B. Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-foodee-test-user"></a>建立 Foodee 測試使用者

本節會在 Foodee 中建立名為 B. Simon 的使用者。 Foodee 支援依預設啟用的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 如果 Foodee 中還沒有使用者存在，在您嘗試存取 Foodee 時就會建立新的使用者。

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [Foodee] 圖格時，應該會自動登入您已設定 SSO 的 Foodee。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
