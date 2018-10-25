---
title: 教學課程：Azure Active Directory 與 Jamf Pro 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Jamf Pro 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: d28e28a2c4f8144da16c4838f07c9b8bb5ce67f0
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268152"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>教學課程：Azure Active Directory 與 Jamf Pro 整合

在此教學課程中，您將了解如何整合 Jamf Pro 與 Azure Active Directory (Azure AD)。

Jamf Pro 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Jamf Pro 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Jamf Pro (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先決條件

若要設定 Azure AD 與 Jamf Pro 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Jamf Pro 單一登入的訂用帳戶

> [!NOTE]
> 若要測試此教學課程中的步驟，我們不建議使用生產環境。

若要測試此教學課程中的步驟，您應該依照這些建議執行：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在此教學課程中，您會在測試環境中測試 Azure AD 單一登入。
此教學課程中說明的案例由二個主要的基本工作組成：

1. 從資源庫新增 Jamf Pro
2. 設定並測試 Azure AD 單一登入

## <a name="adding-jamf-pro-from-the-gallery"></a>從資源庫新增 Jamf Pro

若要設定將 Jamf Pro 整合到 Azure AD 中，您需要從資源庫將 Jamf Pro 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Jamf Pro，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![映像](./media/jamfprosamlconnector-tutorial/selectazuread.png)

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![映像](./media/jamfprosamlconnector-tutorial/a_select_app.png)
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![映像](./media/jamfprosamlconnector-tutorial/a_new_app.png)

4. 在搜尋方塊中，輸入 **Jamf Pro**，從結果面板中選取 [Jamf Pro]，然後按一下 [新增] 按鈕以新增應用程式。

     ![映像](./media/jamfprosamlconnector-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在此節中，您會以名為 "Britta Simon" 的測試使用者身分，搭配 Jamf Pro 設定及測試 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Jamf Pro 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 Jamf Pro 中的相關使用者之間建立連結關聯性。

若要設定及測試與 Jamf Pro 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Jamf Pro 測試使用者](#create-a-jamf-pro-test-user)** - 使 Jamf Pro 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在此節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在 Jamf Pro 應用程式中設定單一登入。

**若要搭配 Jamf Pro 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Jamf Pro] 應用程式整合頁面上，選取 [單一登入]。

    ![映像](./media/jamfprosamlconnector-tutorial/b1_b2_select_sso.png)

2. 按一下畫面頂端的 [變更單一登入模式] 以選取 [SAML] 模式。

      ![映像](./media/jamfprosamlconnector-tutorial/b1_b2_saml_ssso.png)

3. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取] 以啟用單一登入。

    ![映像](./media/jamfprosamlconnector-tutorial/b1_b2_saml_sso.png)

4. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [基本 SAML 設定] 對話方塊。

    ![映像](./media/jamfprosamlconnector-tutorial/b1-domains_and_urlsedit.png)

5. 在 [基本 SAML 設定] 區段上，執行下列步驟：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.jamfcloud.com/saml/metadata`。

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.jamfcloud.com/saml/SSO`。

    ![映像](./media/jamfprosamlconnector-tutorial//b2-domains_and_urls.png)

    c. 按一下 [設定額外的 URL]。

    d. 在 [登入 URL] 文字方塊中，使用下列模式來輸入 URL︰`https://<subdomain>.jamfcloud.com`。

    ![映像](./media/jamfprosamlconnector-tutorial//b4-domains_and_urls.png)

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來更新這些值。 您將會從 Jamf Pro 入口網站的 [單一登入] 區段取得實際的識別碼值，此教學課程稍後將說明此值。 您可以從識別碼值擷取實際的 **subdomain** 值，並在登入 URL 和回覆 URL 中使用該 **subdomain** 資訊。

6. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]，並將其儲存在您的電腦上。

    ![映像](./media/jamfprosamlconnector-tutorial/C2_certificate.png)

7. 若要自動執行 Jamf Pro 內的設定，您必須按一下 [安裝延伸模組] 來安裝「我的應用程式安全登入瀏覽器延伸模組」。

    ![映像](./media/jamfprosamlconnector-tutorial/install_extension.png)
 
8. 將延伸模組新增至瀏覽器之後，按一下 [安裝 Jamf Pro] 便會將您導向到 Jamf Pro 應用程式。 請從該處提供用以登入 Jamf Pro 的管理員認證。 瀏覽器延伸模組將會自動為您設定應用程式，並自動執行步驟 9 到 12。

    ![映像](./media/jamfprosamlconnector-tutorial/d1_saml.png)

9. 如果您想要手動設定 Jamf Pro，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Jamf Pro 公司網站，然後執行下列步驟：

10. 按一下頁面右上角的**設定圖示**。

    ![Jamf Pro 設定](./media/jamfprosamlconnector-tutorial/configure1.png)

11. 按一下 [單一登入]。

    ![Jamf Pro 設定](./media/jamfprosamlconnector-tutorial/configure2.png)

12. 在 [單一登入] 頁面上，執行下列步驟：

    ![Jamf Pro 單一登入](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. 選取 [Jamf Pro 伺服器] 來啟用單一登入存取。

    b. 選取 [允許所有使用者略過]，就不會將使用者重新導向至識別提供者登入頁面來進行驗證，但可改為直接登入 Jamf Pro。 當使用者嘗試透過識別提供者存取 Jamf Pro 時，就會進行 IdP 啟始的 SSO 驗證和授權。

    c. 從 [使用者對應：SAML] 中選取 [NameID] 選項。 根據預設，此設定會設定為 [NameID]，但您可以定義自訂屬性。

    d. 針對 [使用者對應：JAMF PRO] 選取 [電子郵件]。 Jamf Pro 會以下列方式對應由 IdP 所傳送的 SAML 屬性：依使用者或依群組。 當使用者嘗試存取 Jamf Pro，Jamf Pro 預設會從識別提供者中取得該使用者的相關資訊，並比對 Jamf Pro 使用者帳戶。 如果傳入的使用者帳戶不存在於 Jamf Pro 中，則會進行群組名稱比對。

    e. 將值 `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` 貼到 [群組屬性名稱] 文字方塊中。

13. 在同一個頁面上，向下捲動至 [單一登入] 區段下方的 [識別提供者]，然後執行下列步驟：

    ![Jamf Pro 設定](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. 從 [身分識別提供者] 下拉式功能表中，選取 [其他] 作為選項。

    b. 在 [其他提供者] 文字方塊中，輸入 **Azure AD**。

    c. 從 [身分識別提供者中繼資料來源] 下拉式清單中選取 [中繼資料 URL] 作為選項，然後在下列文字方塊中，貼上您從 Azure 入口網站複製的**應用程式同盟中繼資料 URL** 值。

    d. 複製**實體識別碼**值，並將它貼到 Azure 入口網站上 [Jamf Pro 網域及 URL] 區段中的 [識別碼 (實體識別碼)]  文字方塊。

    >[!NOTE]
    > 此處的模糊值為子網域部分。請使用此值來完成 Azure 入口網站上 [Jamf Pro 網域及 URL] 區段中的登入 URL 和回覆 URL。

    e. 按一下 [檔案] 。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

此節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者]，然後選取 [所有使用者]。

    ![映像](./media/jamfprosamlconnector-tutorial/d_users_and_groups.png)

2. 選取畫面頂端的 [新增使用者]。

    ![映像](./media/jamfprosamlconnector-tutorial/d_adduser.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![映像](./media/jamfprosamlconnector-tutorial/d_userproperties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性]、[顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="create-a-jamf-pro-test-user"></a>建立 Jamf Pro 測試使用者

若要讓 Azure AD 使用者能夠登入 Jamf Pro，必須將他們佈建到 Jamf Pro。 在 Jamf Pro 的案例中，佈建是一個手動工作。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Jamf Pro 公司網站。

2. 按一下頁面右上角的**設定圖示**。

    ![新增員工](./media/jamfprosamlconnector-tutorial/configure1.png)

3. 按一下 [Jamf Pro 使用者帳戶和群組]。

    ![新增員工](./media/jamfprosamlconnector-tutorial/user1.png)

4. 按一下 [新增] 。

    ![新增員工](./media/jamfprosamlconnector-tutorial/user2.png)

5. 選取 [建立標準帳戶]。

    ![新增員工](./media/jamfprosamlconnector-tutorial/user3.png)

6. 在 [新增帳戶] 對話方塊上，執行下列步驟：

    ![新增員工](./media/jamfprosamlconnector-tutorial/user4.png)

    a. 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的全名。

    b. 針對您的組織，選取 [存取層級]、[權限集合] 及 [存取狀態] 的適當選項。

    c. 在 [全名] 文字方塊中，輸入 Britta Simon 的全名。

    d. 在 [電子郵件地址] 文字方塊中，輸入 Britta Simon 帳戶的電子郵件地址。

    e. 在 [密碼] 文字方塊中，輸入使用者的密碼。

    f. 在 [確認密碼] 文字方塊中，輸入使用者的密碼。

    g. 按一下 [檔案] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此節中，您會將 Jamf Pro 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Jamf Pro]。

    ![映像](./media/jamfprosamlconnector-tutorial/d_all_applications.png)

2. 在應用程式清單中，選取 [Jamf Pro]。

    ![映像](./media/jamfprosamlconnector-tutorial/d_all_proapplications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![映像](./media/jamfprosamlconnector-tutorial/d_leftpaneusers.png)

4. 選取 [新增] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![映像](./media/jamfprosamlconnector-tutorial/d_assign_user.png)

4. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

5. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="test-single-sign-on"></a>測試單一登入

在此節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Jamf Pro] 圖格時，應該會自動登入您的 Jamf Pro 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
