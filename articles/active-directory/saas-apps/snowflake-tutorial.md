---
title: 教學課程：Azure Active Directory 與 Snowflake 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Snowflake 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3488ac27-0417-4ad9-b9a3-08325fe8ea0d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 6e1f76548e2433f9bc4b0b26b0894a92b7ec0aa0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092554"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>教學課程：Azure Active Directory 與 Snowflake 整合

在本教學課程中，您將了解如何整合 Snowflake 與 Azure Active Directory (Azure AD)。

Snowflake 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Snowflake 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Snowflake (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Snowflake 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用單一登入的 Snowflake 訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Snowflake
2. 設定並測試 Azure AD 單一登入

## <a name="adding-snowflake-from-the-gallery"></a>從資源庫新增 Snowflake

若要設定將 Snowflake 整合到 Azure AD 中，您需要從資源庫將 Snowflake 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Snowflake，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Snowflake**，從結果面板中選取 [Snowflake]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Snowflake](./media/snowflake-tutorial/tutorial_snowflake_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Snowflake 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Snowflake 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Snowflake 中的相關使用者之間建立連結關聯性。

若要使用 Snowflake 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Snowflake 測試使用者](#creating-snowflake-test-user)** - 讓 Snowflake 中對應的 Britta Simon 連結到使用者在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Snowflake 應用程式中設定單一登入。

**若要使用 Snowflake 設定 Azure AD 單一登入功能，請執行下列步驟：**

1. 在 Azure 入口網站的 [Snowflake] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

2. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![設定單一登入](common/tutorial_general_301.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![設定單一登入](common/editconfigure.png)

4. 若您想要以 **IDP** 起始模式設定應用程式，請在 [基本 SAML 組態] 區段執行下列步驟：

    ![Snowflake 網域與 URL 單一登入資訊](./media/snowflake-tutorial/tutorial_snowflake_url1.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：`https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. 在 **[回覆 URL]** 文字方塊中，以下列模式輸入 URL：`https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![Snowflake 網域與 URL 單一登入資訊](./media/snowflake-tutorial/tutorial_snowflake_url2.png)

    在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來更新這些值。

6. 在 [SAML 簽署憑證] 頁面上的 [SAML 簽署憑證] 區段中，按一下 [下載] 以下載**憑證 (Base64)**，然後將憑證檔案儲存在電腦上。

    ![憑證下載連結](./media/snowflake-tutorial/tutorial_snowflake_certificate.png) 

7. 在 [設定 Snowflake] 區段上，依據您的需求複製適當的 URL。

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

    ![Snowflake 組態](common/configuresection.png)

8. 在不同的網頁瀏覽器視窗中，以安全性系統管理員身分登入 Snowflake。

9. 按一下頁面右上方的 [設定檔]，**切換角色**為 **ACCOUNTADMIN**。

    > [!NOTE]
    > 這與您在右上角您使用者名稱底下選取的內容不同
    
    ![Snowflake admin ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

10. 在記事本中開啟**所下載的 Base 64 憑證**。 複製介於 “-----BEGIN CERTIFICATE-----” 與 “-----END CERTIFICATE-----" 之間的值，並將此值貼到下面 [憑證] 旁邊的引號中。 在 [ssoUrl] 中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。 選取 [所有查詢]，然後按一下 [執行]。

    ![Snowflake sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

    ```
    use role accountadmin;
    alter account set saml_identity_provider = '{
    "certificate": "<Paste the content of downloaded certificate from Azure portal>",
    "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
    "type":"custom",
    "label":"AzureAD"
    }';
    alter account set sso_login_page = TRUE;
    ```

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

### <a name="creating-snowflake-test-user"></a>建立 Snowflake 測試使用者

若要讓 Azure AD 使用者能夠登入 Snowflake，必須將他們佈建到 Snowflake。 在 Snowflake 中，需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以安全性系統管理員身分登入 Snowflake。

2. 按一下頁面右上方的 [設定檔]，**切換角色**為 **ACCOUNTADMIN**。  

    ![Snowflake admin ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. 執行下列 SQL 查詢來建立使用者，確定「登入名稱」是設定為工作表上的 Azure AD 使用者名稱，如下所示。

    ![Snowflake adminsql ](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 Snowflake 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![指派使用者][201]

2. 在應用程式清單中，選取 [Snowflake]。

    ![設定單一登入](./media/snowflake-tutorial/tutorial_snowflake_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Snowflake] 圖格時，應該會自動登入您的 Snowflake 應用程式。
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
