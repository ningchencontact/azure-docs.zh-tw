---
title: 教學課程：Azure Active Directory 與 Workspot Control 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Workspot Control 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8716d018756d1e6eadcd6ebeeaf4f67ad0bc4741
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211150"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>教學課程：Azure Active Directory 與 Workspot Control 整合

在本教學課程中，您將了解如何整合 Workspot Control 與 Azure Active Directory (Azure AD)。

Workspot Control 與 Azure AD 整合可提供下列優點：

- 您可以在 Azure AD 中控制可存取 Workspot Control 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Workspot Control (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先決條件

若要設定 Azure AD 與 Workspot Control 的整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Workspot Control 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Workspot Control
2. 設定並測試 Azure AD 單一登入

## <a name="adding-workspot-control-from-the-gallery"></a>從資源庫新增 Workspot Control
若要進行將 Workspot Control 整合至 Azure AD 的設定，您需要從資源庫將 Workspot Control 新增至受控 SaaS 應用程式的清單中。

**若要從資源庫新增 Workspot Control，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![映像](./media/workspotcontrol-tutorial/selectazuread.png)

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![映像](./media/workspotcontrol-tutorial/a_select_app.png)
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![映像](./media/workspotcontrol-tutorial/a_new_app.png)

4. 在搜尋方塊中輸入 **Workspot Control**，從結果面板中選取 [Workspot Control]，然後按一下 [新增] 按鈕以新增應用程式。

     ![映像](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，來設定及測試 Workspot Control 的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Workspot Control 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Workspot Control 中的相關使用者之間建立連結關聯性。

若要設定並測試 Workspot Control 的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Workspot Control 測試使用者](#create-a-workspot-control-test-user)** - 在 Workspot Control 中建立 Britta Simon 的對應項目，且該項目須與 Azure AD 中代表使用者的項目連結。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Workspot Control 應用程式中設定單一登入。

**若要設定 Workspot Control 的 Azure AD 單一登入，請執行下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Workspot Control] 應用程式整合頁面上，選取 [單一登入]。

    ![映像](./media/workspotcontrol-tutorial/B1_B2_Select_SSO.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML] 模式以啟用單一登入。

    ![映像](./media/workspotcontrol-tutorial/b1_b2_saml_sso.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [基本 SAML 組態] 對話方塊。

    ![映像](./media/workspotcontrol-tutorial/b1-domains_and_urlsedit.png)

4. 若您想要在 [IDP] 起始模式中設定應用程式，請在 [基本 SAML 設定] 區段上執行下列步驟：

    ![映像](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<INSTANCENAME>-saml.workspot.com/saml/metadata`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<INSTANCENAME>-saml.workspot.com/saml/assertion`

    c. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

     ![映像](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url1.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [Workspot Control 客戶支援小組](mailto:support@workspot.com)以取得這些值。 

5. 在 [以 SAML 設定單一登入] 頁面上的 [SAML 簽署憑證] 區段中，按一下 [下載] 以下載**憑證 (Base64)** 並將其儲存在電腦上。

    ![映像](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_certficate.png) 

6. 在 [安裝 Workspot Control] 區段上，依據您的需求複製適當的 URL。

    請注意，URL 可能會顯示下列項目：

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

    ![映像](./media/workspotcontrol-tutorial/d1_samlsonfigure.png) 

7. 在不同的網頁瀏覽器視窗中，以安全性系統管理員身分登入 Workspot Control。

8. 在頁面頂端的工具列中按一下 [Setup] \(設定\) ****，然後瀏覽至 [SAML] ****。

    ![映像](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

9. 在 [安全性聲明標記語言組態] 頁面上，執行下列步驟：
 
    ![映像](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    a. 在 [實體識別碼] 文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼] 值。   

    b. 在 [登入服務 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    c. 在 [登出服務 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。 

    d. 按一下 [更新檔案] 按鈕，將您從 Azure 入口網站下載的 base-64 編碼憑證上傳至 X.509 憑證中。

    e. 按一下 [檔案] 。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![映像](./media/workspotcontrol-tutorial/d_users_and_groups.png)

2. 在畫面頂端選取 [新增使用者]。

    ![映像](./media/workspotcontrol-tutorial/d_adduser.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![映像](./media/workspotcontrol-tutorial/d_userproperties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性] [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。
 
### <a name="create-a-workspot-control-test-user"></a>建立 Workspot Control 測試使用者

若要讓 Azure AD 使用者能夠登入 Workspot Control，必須將他們佈建到 Workspot Control 中。 在 Workspot Control 中，需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以安全性系統管理員身分登入 Workspot Control。

2. 在頁面頂端的工具列中按一下 [Users] \(使用者\) ****，然後瀏覽至 [Add User] \(新增使用者\) ****。

    ![映像](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. 在 [新增使用者] 頁面上，執行下列步驟：

    ![映像](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    a. 在 [名字] 文字方塊中，輸入使用者的名字，例如 **Britta**。

    b. 在 [姓氏] 文字方塊中，輸入使用者的姓氏，例如 **Simon**。

    c. 在 [電子郵件] 文字方塊中，輸入使用者的電子郵件，例如 **Brittasimon@contoso.com**。

    d. 從 [角色] 下拉式清單中選取適當的使用者角色。

    e. 從 [群組] 下拉式清單中選取適當的使用者群組。

    f. 按一下 [新增使用者] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Workspot Control 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![映像](./media/workspotcontrol-tutorial/d_all_applications.png)

2. 在應用程式清單中，選取 [Workspot Control]。

    ![映像](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_app.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![映像](./media/workspotcontrol-tutorial/d_leftpaneusers.png)

4. 選取 [新增] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![映像](./media/workspotcontrol-tutorial/d_assign_user.png)

4. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

5. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Workspot Control] 圖格時，應該會自動登入您的 Workspot Control 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
