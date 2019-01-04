---
title: 教學課程：Azure Active Directory 與 Expensify 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Expensify 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: jeedes
ms.openlocfilehash: 5d61ac27eb5a6e4f546d8c6db66b84f2c204b507
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840464"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>教學課程：Azure Active Directory 與 Expensify 整合

在本教學課程中，您會了解如何將 Expensify 與 Azure Active Directory (Azure AD) 整合。

將 Expensify 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Expensify 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Expensify (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Expensify 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Expensify 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Expensify
1. 設定並測試 Azure AD 單一登入

## <a name="adding-expensify-from-the-gallery"></a>從資源庫新增 Expensify

若要設定將 Expensify 整合到 Azure AD 中，您需要從資源庫將 Expensify 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Expensify，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![映像](./media/expensify-tutorial/selectazuread.png)

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![映像](./media/expensify-tutorial/a_select_app.png)
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![映像](./media/expensify-tutorial/a_new_app.png)

4. 在搜尋方塊中，輸入 **Expensify**，從結果面板中選取 [Expensify]，然後按一下 [新增] 按鈕以新增應用程式。

     ![映像](./media/expensify-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Expensify 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Expensify 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 Expensify 中的相關使用者之間建立連結關聯性。

在 Expensify 中，將 Azure AD 中**使用者名稱**的值，指派為 **Username** 的值，以建立連結關聯性。

若要設定及測試與 Expensify 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 Expensify 測試使用者](#create-an-expensify-test-user)** - 在 Expensify 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表該使用者的項目連結。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Expensify 中設定單一登入。

**若要設定與 Expensify 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Expensify] 應用程式整合頁面上，選取 [單一登入]。

    ![映像](./media/expensify-tutorial/b1_b2_select_sso.png)

2. 按一下畫面頂端的 [變更單一登入模式]，選取 [SAML] 模式。

      ![映像](./media/expensify-tutorial/b1_b2_saml_ssso.png)

3. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![映像](./media/expensify-tutorial/b1_b2_saml_sso.png)

4. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [基本 SAML 組態] 對話方塊。

    ![映像](./media/expensify-tutorial/b1-domains_and_urlsedit.png)

5. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    a. 在 [登入 URL] 文字方塊中，將 URL 輸入為：`https://www.expensify.com/authentication/saml/login`

    b. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://www.<companyname>.expensify.com`

    ![映像](./media/expensify-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > 以您的公司網域取代 [識別碼 URL] 的 <companyname> 部分。 請參閱上方的 `https://contoso.expensify.com` 範例。 在 Expensify 中，這是您網域的名稱，會從 [Settings] \(設定\) > [Domain Control] \(網域控制\) 顯示。

    ![Expensify 網域資訊](./media/expensify-tutorial/tutorial_expensify_domain.png)

6. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載] 以依據您的需求下載適當的憑證，並儲存在電腦上。

    ![映像](./media/expensify-tutorial/certificatebase64.png)

7. 若要在 Expensify 中啟用 SSO，您必須先在應用程式中啟用 [網域控制]。 您可以透過[這裡](https://help.expensify.com/domain-control)所列的步驟，在應用程式中啟用 [網域控制]。 如需其他支援，請與 [Expensify 用戶端支援小組](mailto:help@expensify.com)合作。 一旦啟用了 [網域控制]，請遵循下列步驟：
   
    ![設定單一登入](./media/expensify-tutorial/tutorial_expensify_51.png)
    
    a. 登入 Expensify 應用程式。
    
    b. 在左側面板中，按一下 [Settings] \(設定\)，然後瀏覽至 [SAML]。
    
    c. 將 [SAML Login] \(SAML 登入\) 選項切換成 [Enabled] \(已啟用\)。
    
    d. 從記事本中的 Azure AD 開啟下載的同盟中繼資料，複製其內容並貼到 [識別提供者中繼資料] 文字方塊中。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![映像](./media/expensify-tutorial/d_users_and_groups.png)

2. 在畫面頂端選取 [新增使用者]。

    ![映像](./media/expensify-tutorial/d_adduser.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![映像](./media/expensify-tutorial/d_userproperties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性] [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。
 
### <a name="create-an-expensify-test-user"></a>建立 Expensify 測試使用者

在本節中，您會在 Expensify 中建立名為 Britta Simon 的使用者。 請與 [Expensify 用戶端支援小組](mailto:help@expensify.com)合作，在 Expensify 平台中新增使用者。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會授與 Britta Simon 對 Expensify 的存取權，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![映像](./media/expensify-tutorial/d_all_applications.png)

2. 在應用程式清單中，選取 [Expensify] 。

    ![映像](./media/expensify-tutorial/d_all_proapplications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![映像](./media/expensify-tutorial/d_leftpaneusers.png)

4. 選取 [新增] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![映像](./media/expensify-tutorial/d_assign_user.png)

4. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

5. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [Expensify] 圖格時，應該會自動登入您的 Expensify 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)




