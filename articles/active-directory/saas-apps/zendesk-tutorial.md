---
title: 教學課程：Azure Active Directory 與 Zendesk 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Zendesk 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 37d20eabfc8fb883cda346dc8b55a17b8b959218
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268169"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>教學課程：Azure Active Directory 與 Zendesk 整合

在此教學課程中，您會了解如何整合 Zendesk 與 Azure Active Directory (Azure AD)。

Zendesk 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Zendesk 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Zendesk (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先決條件

若要設定 Azure AD 與 Zendesk 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Zendesk 單一登入的訂用帳戶

> [!NOTE]
> 若要測試此教學課程中的步驟，我們不建議使用生產環境。

若要測試此教學課程中的步驟，您應該依照這些建議執行：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在此教學課程中，您會在測試環境中測試 Azure AD 單一登入。
此教學課程中說明的案例由二個主要的基本工作組成：

1. 從資源庫新增 Zendesk
2. 設定並測試 Azure AD 單一登入

## <a name="adding-zendesk-from-the-gallery"></a>從資源庫新增 Zendesk

若要設定將 Zendesk 整合到 Azure AD 中，您需要從資源庫將 Zendesk 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Zendesk，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![映像](./media/zendesk-tutorial/selectazuread.png)

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![映像](./media/zendesk-tutorial/a_select_app.png)
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![映像](./media/zendesk-tutorial/a_new_app.png)

4. 在搜尋方塊中，輸入 **Zendesk**，從結果面板中選取 [Zendesk]，然後按一下 [新增] 按鈕以新增應用程式。

     ![映像](./media/zendesk-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在此節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Zendesk 設定及測試 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Zendesk 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 Zendesk 中的相關使用者之間建立連結關聯性。

在 Zendesk 中，將 Azure AD 中的 [使用者名稱] 值指派為 [使用者名稱] 的值，以建立連結關聯性。

若要設定及測試與 Zendesk 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Zendesk 測試使用者](#create-a-zendesk-test-user)** - 讓 Zendesk 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在此節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Zendesk 應用程式中設定單一登入。

**若要設定與 Zendesk 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com/) 的 [Zendesk] 應用程式整合頁面上，選取 [單一登入]。

    ![映像](./media/zendesk-tutorial/b1_b2_select_sso.png)

2. 按一下畫面頂端的 [變更單一登入模式] 以選取 [SAML] 模式。

      ![映像](./media/zendesk-tutorial/b1_b2_saml_ssso.png)

3. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取] 以啟用單一登入。

    ![映像](./media/zendesk-tutorial/b1_b2_saml_sso.png)

4. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [基本 SAML 設定] 對話方塊。

    ![映像](./media/zendesk-tutorial/b1-domains_and_urlsedit.png)

5. 在 [基本 SAML 設定] 區段上，執行下列步驟：

    a. 在 [登入 URL] 文字方塊中，使用下列模式來輸入 URL︰`https://<subdomain>.zendesk.com`。

    b. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`<subdomain>.zendesk.com`。

    ![映像](./media/zendesk-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Zendesk 用戶端支援小組](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise)以取得這些值。

6. Zendesk 需要特定格式的 SAML 判斷提示。 沒有任何必要 SAML 屬性，但您可以視需要從應用程式整合頁面的 [使用者屬性] 區段新增屬性。 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [使用者屬性] 對話方塊。

    ![映像](./media/zendesk-tutorial/i4-attribute.png)

7. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![映像](./media/zendesk-tutorial/i2-attribute.png)

    ![映像](./media/zendesk-tutorial/i3-attribute.png)
    
    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間] 保持空白。

    d. 選取 [來源] 作為 [屬性]。
    
    e. 在 [來源屬性] 清單中，輸入針對該資料列顯示的屬性值。
    
    f. 按一下 [確定]。

    g. 按一下 [檔案] 。

    > [!NOTE]
    > 您可以使用擴充屬性來新增預設不在 Azure AD 中的屬性。 按一下[可以在 SAML 中設定的使用者屬性](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-)，以取得 **Zendesk**接受的完整 SAML 屬性清單。

8. 在 [SAML 簽署憑證] 區段中，複製 [指紋] 並將其儲存在您的電腦上。

    ![映像](./media/zendesk-tutorial/C3_certificate.png)

    a. 視需要為 [簽署選項] 選取適當的選項。

    b. 視需要為 [簽署演算法] 選取適當的選項。

    c. 按一下 [儲存] 

9. 在 [安裝 Zendesk] 區段上，按一下 [檢視逐步指示] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中，複製以下 URL。

    請注意，URL 可能會顯示下列項目：

    a. SAML 單一登入服務 URL

    b. 實體識別碼

    c. 登出 URL

    ![映像](./media/zendesk-tutorial/d1_saml.png) 

10. 有兩種方式可設定 Zendesk - 自動和手動。
  
11. 若要自動執行 Zendesk 內的設定，您必須按一下 [安裝延伸模組] 來安裝「我的應用程式安全登入瀏覽器延伸模組」。

    ![映像](./media/zendesk-tutorial/install_extension.png)

12. 將延伸模組新增至瀏覽器之後，按一下 [安裝 Zendesk] 便會將您導向到 Zendesk 應用程式。 請從該處提供用以登入 Zendesk 的管理員認證。 瀏覽器延伸模組將會自動為您設定應用程式，並自動執行步驟 13。

     ![映像](./media/zendesk-tutorial/d2_saml.png) 

13. 如果您想要手動設定 Zendesk，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Zendesk 公司網站，然後執行下列步驟：

    * 按一下 Admin 。

    * 在左側瀏覽窗格中，按一下 [設定]，然後按一下 [安全性]。

    * 在 [安全性]  頁面上執行下列步驟：

      ![安全性](././media/zendesk-tutorial/ic773089.png "安全性")

      ![單一登入](././media/zendesk-tutorial/ic773090.png "單一登入")

      a. 按一下 [系統管理員和代理程式] 索引標籤。

      b. 選取 [單一登入 (SSO) 和 SAML]，然後選取 [SAML]。

      c. 在 [SAML SSO URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 單一登入服務 URL] 值。

      d. 在 [遠端登出 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。

      e. 在 [憑證指紋] 文字方塊中，貼上您從 Azure 入口網站複製的憑證 [指紋] 值。

      f. 按一下 [檔案] 。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

此節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者]，然後選取 [所有使用者]。

    ![映像](./media/zendesk-tutorial/d_users_and_groups.png)

2. 選取畫面頂端的 [新增使用者]。

    ![映像](./media/zendesk-tutorial/d_adduser.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![映像](./media/zendesk-tutorial/d_userproperties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性]、[顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="create-a-zendesk-test-user"></a>建立 Zendesk 測試使用者

此節的目標是要在 Zendesk 中建立名為 Britta Simon 的使用者。 Zendesk 支援自動使用者佈建，該功能預設為啟用。 在[這裡](Zendesk-provisioning-tutorial.md)可以找到更多關於如何設定自動使用者佈建的詳細資料。

**如果您需要手動建立使用者，請執行下列步驟：**

> [!NOTE]
> [使用者] 帳戶會在登入時自動佈建。 登入之前，必須在 **Zendesk** 中手動佈建 [代理程式] 和 [系統管理員] 帳戶。

1. 登入您的 **Zendesk** 租用戶。

2. 選取 [客戶清單]  索引標籤。

3. 選取 [使用者] 索引標籤，然後按一下 [新增]。

    ![新增使用者](././media/zendesk-tutorial/ic773632.png "新增使用者")
4. 輸入您要佈建之現有 Azure AD 帳戶的**名稱**和**電子郵件**，然後按一下 [儲存]。

    ![新增使用者](././media/zendesk-tutorial/ic773633.png "新增使用者")

> [!NOTE]
> 您可以使用任何其他的 Zendesk 使用者帳戶建立工具或 Zendesk 提供的 API，佈建 AAD 使用者帳戶。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此節中，您會將 Zendesk 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式]。

    ![映像](./media/zendesk-tutorial/d_all_applications.png)

2. 在應用程式清單中，選取 [Zendesk]。

    ![映像](./media/zendesk-tutorial/d_all_zendeskapplications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![映像](./media/zendesk-tutorial/d_leftpaneusers.png)

4. 選取 [新增] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![映像](./media/zendesk-tutorial/d_assign_user.png)

4. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

5. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="test-single-sign-on"></a>測試單一登入

在此節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Zendesk 圖格時，應該會自動登入您的 Zendesk 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
* [設定使用者佈建](zendesk-provisioning-tutorial.md)
