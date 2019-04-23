---
title: 教學課程：Azure Active Directory 與 Grovo 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Grovo 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 399cecc3-aa62-4914-8b6c-5a35289820c1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/18/2019
ms.author: jeedes
ms.openlocfilehash: fc0ef38193bbd6e5044764a26a5793a4d115348d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278134"
---
# <a name="tutorial-azure-active-directory-integration-with-grovo"></a>教學課程：Azure Active Directory 與 Grovo 整合

在本教學課程中，您會了解如何整合 Grovo 與 Azure Active Directory (Azure AD)。
Grovo 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Grovo 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Grovo (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Grovo 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 啟用 Grovo 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Grovo 支援 **SP** 和 **IDP** 起始的 SSO

* Grovo 支援 **Just In Time** 使用者佈建

## <a name="adding-grovo-from-the-gallery"></a>從資源庫新增 Grovo

若要設定將 Grovo 整合到 Azure AD 中，您需要從資源庫將 Grovo 新增到受控 SaaS 應用程式清單。

**若要從資源庫加入 Grovo，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Grovo**，從結果面板中選取 [Grovo]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 Grovo](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 Grovo 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Grovo 中相關使用者之間的連結關聯性。

若要使用 Grovo 設定及測試 Azure AD 單一登入，您需要完成下列建構要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Grovo 單一登入](#configure-grovo-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Grovo 測試使用者](#create-grovo-test-user)** - 使 Grovo 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 Grovo 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Grovo] 應用程式整合分頁上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 區段上，若您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![Grovo 網域與 URL 單一登入資訊](common/idp-relay.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    c. 按一下 [設定額外的 URL]。

    d. 在 [轉送狀態] 文字方塊中，使用下列模式輸入 URL：`https://<subdomain>.grovo.com`

5. 如果您想要在 **SP** 起始模式中設定應用程式，請執行下列步驟：

    ![Grovo 網域與 URL 單一登入資訊](common/both-signonurl.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「識別碼」、「回覆 URL」、「登入 URL」及「轉送狀態」來更新這些值。 請連絡 [ 用戶端支援小組](https://www.grovo.com/contact-us)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

6. Grovo 應用程式需要特定格式的 SAML 判斷提示，需要您加入自訂屬性對應到您的 SAML token 屬性設定。 下列螢幕擷取畫面顯示預設屬性清單，其中的 **nameidentifier** 與 **user.userprincipalname** 相對應。 Grovo 應用程式要求 **nameidentifier** 需與 **user.mail** 相對應，因此您必須按一下 [編輯] 圖示以編輯屬性對應，並變更屬性對應。

    ![image](common/edit-attribute.png)

7. 除了以上屬性外，Grovo 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | Name | 來源屬性|
    | ------------------- | -------------------- |    
    | 名字          | user.givenname |
    | 姓氏           | user.surname |
    | 電子郵件地址       | user.mail    |
    | employeeID          | user.employeeid |

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間] 保持空白。

    d. 選取 [來源] 作為 [屬性]。

    e. 在 [來源屬性] 清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]。

    g. 按一下 [檔案] 。

8. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載 [憑證 (Base64)]，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

9. 在 [設定 Grovo] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-grovo-single-sign-on"></a>設定 Grovo 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 Grovo。

2. 移至 [管理員] > [整合]。
 
    ![Grovo 設定](./media/grovo-tutorial/tutorial_grovo_admin.png) 

3. 按一下 [SP Initiated SAML 2.0] 區段底下的 [設定]。

    ![Grovo 設定](./media/grovo-tutorial/tutorial_grovo_setup.png)

4. 在 [SP Initiated SAML 2.0] 快顯視窗中，執行下列步驟：

    ![Grovo 設定](./media/grovo-tutorial/tutorial_grovo_saml.png)

    a. 在 [實體識別碼] 文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼] 值。

    b. 在 [單一登入服務端點] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    c. 選取 [單一登入服務端點繫結] 作為 `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect`。
    
    d. 從記事本中的 Azure 入口網站開啟已下載的 [Base64 編碼憑證]，將它貼至 [公開金鑰] 文字方塊。

    e. 按 [下一步] 。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Grovo 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Grovo]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Grovo] 。

    ![應用程式清單中的 Grovo 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-grovo-test-user"></a>建立 Grovo 測試使用者

本節會在 Grovo 中建立名為 Britta Simon 的使用者。 Grovo 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Grovo 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

>[!Note]
>如果您需要手動建立使用者，請連絡 [Grovo 支援小組](https://www.grovo.com/contact-us)。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Grovo] 圖格時，應該會自動登入您已設定 SSO 的 Grovo。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

