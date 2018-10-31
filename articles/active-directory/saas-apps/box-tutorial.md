---
title: 教學課程：Azure Active Directory 與 Box 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Box 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: 535f59b1b0dc56b183c8a019d101b4fd4f1bfad6
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116120"
---
# <a name="tutorial-azure-active-directory-integration-with-box"></a>教學課程：Azure Active Directory 與 Box 整合

在本教學課程中，您將了解如何整合 Box 與 Azure Active Directory (Azure AD)。

Box 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Box 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Box (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Box 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Box 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Box
2. 設定並測試 Azure AD 單一登入

## <a name="adding-box-from-the-gallery"></a>從資源庫新增 Box
若要設定將 Box 整合到 Azure AD 中，您需要從資源庫將 Box 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Box，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![映像](./media/box-tutorial/selectazuread.png)

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![映像](./media/box-tutorial/a_select_app.png)
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![映像](./media/box-tutorial/a_new_app.png)

4. 在搜尋方塊中，輸入 **Box**，從結果面板中選取 [Box]，然後按一下 [新增] 按鈕以新增應用程式。

     ![映像](./media/box-tutorial/tutorial_Box_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 Box 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Box 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 Box 中相關使用者之間的連結關聯性。

若要設定及測試與 Box 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Box 測試使用者](#create-a-box-test-user)** - 讓 Britta Simon 在 Box 中有一個對應項目連結至使用者的 Azure AD 代表身分。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Box 應用程式中設定單一登入。

**若要使用 Box 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Box] 應用程式整合頁面上，選取 [單一登入]。

    ![映像](./media/box-tutorial/b1_b2_select_sso.png)

2. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![映像](./media/box-tutorial/b1_b2_saml_sso.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [基本 SAML 組態] 對話方塊。

    ![映像](./media/box-tutorial/b1-domains_and_urlsedit.png)

4. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<SUBDOMAIN>.account.box.com`

    b. 在 [識別碼] 文字方塊中，輸入 URL：`box.net`

    ![映像](./media/box-tutorial/tutorial_box_url.png)

    > [!NOTE] 
    > [登入 URL] 的值不是真正的值。 請使用實際的「登入 URL」來更新此值。 請連絡 [Box 支援小組](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) \(英文\) 以取得此值。
 
5. 在 [以 SAML 設定單一登入] 頁面上的 [SAML 簽署憑證] 區段中，按一下 [下載] 以下載**同盟中繼資料 XML** 並將其儲存在電腦上。

    ![映像](./media/box-tutorial/tutorial_Box_certificate.png)

6. 若要設定應用程式的 SSO，請遵循[自行設定 SSO](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown) 中的程序。 

>[!NOTE]
>如果您無法設定 Box 帳戶的 SSO 設定，必須將已下載的**同盟中繼資料 XML**傳送至 [Box 支援小組](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![映像](./media/box-tutorial/d_users_and_groups.png)

2. 在畫面頂端選取 [新增使用者]。

    ![映像](./media/box-tutorial/d_adduser.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![映像](./media/box-tutorial/d_userproperties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性]、[顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。
 
### <a name="create-a-box-test-user"></a>建立 Box 測試使用者

本節目標是在 Box 中建立名為 Britta Simon 的使用者。 Box 支援預設啟用的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 嘗試存取 Box 時若尚無使用者，則會建立新使用者。
>[!Note]
>如果您需要手動建立使用者，請連絡 [Box 支援小組](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire)。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Box 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![映像](./media/box-tutorial/d_all_applications.png)

2. 在應用程式清單中，選取 [Box]。

    ![映像](./media/box-tutorial/tutorial_Box_app.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![映像](./media/box-tutorial/d_leftpaneusers.png)

4. 選取 [新增] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![映像](./media/box-tutorial/d_assign_user.png)

4. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

5. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中選取 **Box** 圖格時，您會取得可讓您登入 Box 應用程式的登入頁面。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
* [設定使用者佈建](box-userprovisioning-tutorial.md)


