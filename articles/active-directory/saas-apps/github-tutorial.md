---
title: 教學課程：Azure Active Directory 與 GitHub 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 GitHub 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: dbd4634c575fd4f1886d3e7714ef9ddabbde0f8a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341152"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>教學課程：Azure Active Directory 與 GitHub 整合

在本教學課程中，您將了解如何整合 GitHub 與 Azure Active Directory (Azure AD)。

GitHub 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 GitHub 的人員。
- 您可以讓使用者使用其 Azure AD 帳戶來自動登入 GitHub (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 GitHub 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 GitHub 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 GitHub
2. 設定並測試 Azure AD 單一登入

## <a name="adding-github-from-the-gallery"></a>從資源庫新增 GitHub
若要設定將 GitHub 整合到 Azure AD 中，您需要從資源庫將 GitHub 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 GitHub，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![映像](./media/github-tutorial/selectazuread.png)

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![映像](./media/github-tutorial/a_select_app.png)
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![映像](./media/github-tutorial/a_new_app.png)

4. 在搜尋方塊中，輸入 **GitHub**，從結果面板中選取 [GitHub]，然後按一下 [新增] 按鈕以新增應用程式。

     ![映像](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 GitHub 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 GitHub 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 GitHub 中相關使用者之間的連結關聯性。

若要設定及測試與 GitHub 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 GitHub 測試使用者](#create-a-github-test-user)** - 讓 GitHub 中的 Britta Simon 對應項目得以連結至 Azure AD 中代表該使用者的項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，並在 GitHub 應用程式中設定單一登入。

**若要設定與 GitHub 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [GitHub] 應用程式整合頁面上，選取 [單一登入]。

    ![映像](./media/github-tutorial/b1_b2_select_sso.png)

2. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![映像](./media/github-tutorial/b1_b2_saml_sso.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [基本 SAML 組態] 對話方塊。

    ![映像](./media/github-tutorial/b1-domains_and_urlsedit.png)

4. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    ![映像](./media/github-tutorial/tutorial_github_url.png) 

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://github.com/orgs/<entity-id>/sso`

    b. 在 [識別碼 (實體識別碼)] 文字方塊中，使用下列模式輸入 URL：`https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > 請注意這些不是真正的值。 您必須使用實際的登入 URL 及識別碼來更新這些值。 在此建議您在 [識別碼] 中使用唯一的字串值。 移至 [GitHub 管理] 區段來擷取這些值。

5. GitHub 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性] 區段中，管理這些屬性的值。 按一下 [編輯] 按鈕以開啟 [使用者使屬性] 對話方塊。

    ![映像](./media/github-tutorial/i3-attribute.png)

6. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：
    
    a. 按一下 [編輯] 按鈕以開啟 [管理使用者宣告] 對話方塊。

    ![映像](./media/github-tutorial/i2-attribute.png)

    ![映像](./media/github-tutorial/i4-attribute.png)

    b. 從 [來源屬性] 清單中，選取屬性值。

    c. 按一下 [檔案] 。
 
7. 在 [SAML 簽署憑證] 區段中，按一下 [下載] 來下載 [憑證 (Base64)]，然後將它儲存在您的電腦上。

    ![映像](./media/github-tutorial/tutorial_github_certficate.png)

8. 在 [安裝 GitHub] 區段上，依據您的需求複製適當的 URL。

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

    ![映像](./media/github-tutorial/d1_samlsonfigure.png) 

9. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 GitHub 組織網站。

10. 瀏覽至 [設定]，然後按一下 [安全性]

    ![設定](./media/github-tutorial/tutorial_github_config_github_03.png)

11. 勾選 [啟用 SAML 驗證]方塊，以顯示單一登入設定欄位。 然後，使用單一登入 URL 值來更新 Azure AD 組態上的單一登入 URL。

    ![設定](./media/github-tutorial/tutorial_github_config_github_13.png)

12. 設定下列欄位：

    ![設定](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. 在 [登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的**登入 URL** 值。

    b. 在 [簽發者] 文字方塊中，貼上您從 Azure 入口網站複製的 **Azure AD 識別碼**值。

    c. 在記事本中開啟從 Azure 入口網站下載的憑證，將內容貼至 [公開憑證] 文字方塊。

    d. 按一下 [編輯] 圖示以編輯 [簽章方法] 和 [摘要方法]，將 **RSA-SHA1** 和 **SHA1** 改為 **RSA-SHA256** 和 **SHA256**，如下所示。

    ![映像](./media/github-tutorial/tutorial_github_sha.png) 
    
13. 按一下 [測試 SAML 組態]，確認 SSO 期間沒有驗證失敗或錯誤。

    ![設定](./media/github-tutorial/tutorial_github_config_github_06.png)

14. 按一下 [儲存] 

> [!NOTE]
> GitHub 中的單一登入會向 GitHub 中的特定組織驗證，而不會取代 GitHub 本身的驗證。 因此，如果使用者的 GitHub.com 工作階段已過期，系統可能會在單一登入程序進行期間，要求您使用 GitHub 的識別碼/密碼進行驗證。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![映像](./media/github-tutorial/d_users_and_groups.png)

2. 在畫面頂端選取 [新增使用者]。

    ![映像](./media/github-tutorial/d_adduser.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![映像](./media/github-tutorial/d_userproperties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性]、[顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。
 
### <a name="create-a-github-test-user"></a>建立 GitHub 測試使用者

本節的目標是在 GitHub 中建立名為 Britta Simon 的使用者。 GitHub 支援自動使用者佈建，該功能預設為啟用。 您可以在[這裡](github-provisioning-tutorial.md)找到關於如何設定自動使用者佈建的更多詳細資料。

**如果您需要手動建立使用者，請執行下列步驟：**

1. 以系統管理員身分登入您的 GitHub 公司網站。

2. 按一下 [人員] 。

    ![People](./media/github-tutorial/tutorial_github_config_github_08.png "People")

3. 按一下 [邀請成員]。

    ![邀請使用者](./media/github-tutorial/tutorial_github_config_github_09.png "邀請使用者")

4. 在 [邀請成員] 對話方塊頁面上，執行下列步驟：

    a. 在 [電子郵件] 文字方塊中，輸入 Britta Simon 帳戶的電子郵件地址。

    ![邀請人員](./media/github-tutorial/tutorial_github_config_github_10.png "邀請人員")

    b. 按一下 [傳送邀請]。

    ![邀請人員](./media/github-tutorial/tutorial_github_config_github_11.png "邀請人員")

    > [!NOTE]
    > Azure Active Directory 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 GitHub 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![映像](./media/github-tutorial/d_all_applications.png)

2. 在應用程式清單中，選取 [GitHub]。

    ![映像](./media/github-tutorial/tutorial_github_app.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![映像](./media/github-tutorial/d_leftpaneusers.png)

4. 選取 [新增] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![映像](./media/github-tutorial/d_assign_user.png)

4. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

5. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [GitHub] 圖格時，應該就會自動登入 GitHub 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)


