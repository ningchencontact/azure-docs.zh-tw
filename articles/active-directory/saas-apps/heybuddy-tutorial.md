---
title: 教學課程：Azure Active Directory 與 HeyBuddy 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 HeyBuddy 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d51b5af6-018e-4678-9a3f-b70438394f67
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2018
ms.author: jeedes
ms.openlocfilehash: 3ef345d866de16b856d3c8b56fa086b83b74ae0f
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289545"
---
# <a name="tutorial-azure-active-directory-integration-with-heybuddy"></a>教學課程：Azure Active Directory 與 HeyBuddy 整合

在本教學課程中，您將了解如何整合 HeyBuddy 與 Azure Active Directory (Azure AD)。

將 HeyBuddy 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 HeyBuddy 的人員。
- 您可以讓使用者使用其 Azure AD 帳戶自動登入 HeyBuddy (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>必要條件

若要設定與 HeyBuddy 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 HeyBuddy 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 HeyBuddy
2. 設定並測試 Azure AD 單一登入

## <a name="adding-heybuddy-from-the-gallery"></a>從資源庫新增 HeyBuddy

若要設定將 HeyBuddy 整合到 Azure AD 中，您需要從資源庫將 HeyBuddy 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 HeyBuddy，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **HeyBuddy**，從結果面板中選取 [HeyBuddy]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 HeyBuddy](./media/heybuddy-tutorial/tutorial_heybuddy_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 HeyBuddy 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 HeyBuddy 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 HeyBuddy 中相關使用者之間的連結關聯性。

若要使用 HeyBuddy 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 HeyBuddy 測試使用者](#creating-a-heybuddy-test-user)** - 使 HeyBuddy 中 Britta Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在 HeyBuddy 應用程式中設定單一登入。

**若要使用 HeyBuddy 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [HeyBuddy] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

2. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![設定單一登入](common/tutorial_general_301.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![設定單一登入](common/editconfigure.png)

4. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    ![HeyBuddy 網域及 URL 單一登入資訊](./media/heybuddy-tutorial/tutorial_heybuddy_url.png)

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://api.heybuddy.com/auth/<ENTITY ID>`

    b. 在 [識別碼 (實體識別碼)] 文字方塊中，使用下列模式輸入 URL：`YourCompanyInstanceofHeyBuddy`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」(實體識別碼) 來更新這些值。 系統會為每個組織自動產生登入 URL 中的 `Entity ID`。 請連絡 [HeyBuddy 用戶端支援小組](mailto:support@heybuddy.com)以取得這些值。

5. HeyBuddy 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性與宣告] 區段中管理這些屬性的值。 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [使用者屬性與宣告] 對話方塊。

    ![映像](./media/heybuddy-tutorial/tutorial_heybuddy_attribute.png)

    >[!NOTE]
    >請參閱此[連結](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)，以了解如何設定及安裝應用程式的角色。

6. 在 [使用者屬性與宣告] 對話方塊的 [使用者宣告] 區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：
    
    | 名稱  | 來源屬性  |
    | --------------- | --------------- |
    | 角色               | user.assignedroles |

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![映像](./common/new_save_attribute.png)

    ![映像](./common/new_attribute_details.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 將 [命名空間] 值保留為空白。

    d. 選取 [來源] 作為 [屬性]。

    e. 在 [來源屬性] 清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [檔案] 。

7. 在 [SAML 簽署憑證] 頁面上的 [SAML 簽署憑證] 區段中，按一下複製**圖示**以複製 **應用程式同盟中繼資料 Url**  並貼到記事本。

    ![憑證下載連結](./media/heybuddy-tutorial/tutorial_heybuddy_certificate.png) 

8. 若要在 **HeyBuddy** 端設定單一登入，您必須將所複製的 [應用程式同盟中繼資料 URL] 傳送給 [HeyBuddy 支援小組](mailto:support@heybuddy.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![建立 Azure AD 使用者][100]

2. 在畫面頂端選取 [新增使用者]。

    ![建立 Azure AD 測試使用者](common/create_aaduser_01.png) 

3. 在 [使用者] 屬性中，執行下列步驟。

    ![建立 Azure AD 測試使用者](common/create_aaduser_02.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性] [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="creating-a-heybuddy-test-user"></a>建立 HeyBuddy 測試使用者

本節目標是在 HeyBuddy 中建立名為 Britta Simon 的使用者。 HeyBuddy 支援預設啟用的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 當您嘗試存取 HeyBuddy 時，如果 HeyBuddy 還沒有使用者，它將會建立新的使用者。
>[!Note]
>如果您需要手動建立使用者，請連絡  [HeyBuddy 支援小組](mailto:support@heybuddy.com)。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 HeyBuddy 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![指派使用者][201]

2. 在應用程式清單中，選取 [HeyBuddy]。

    ![設定單一登入](./media/heybuddy-tutorial/tutorial_heybuddy_app.png)

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 HeyBuddy 圖示時，應該會自動登入 HeyBuddy 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
