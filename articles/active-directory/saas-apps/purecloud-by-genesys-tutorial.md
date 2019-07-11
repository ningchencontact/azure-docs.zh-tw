---
title: 教學課程：Azure Active Directory 與 PureCloud by Genesys 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 PureCloud by Genesys 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: ac476e2a66f7fd1d315adb37258917b3ff47373f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093454"
---
# <a name="tutorial-azure-active-directory-integration-with-purecloud-by-genesys"></a>教學課程：Azure Active Directory 與 PureCloud by Genesys 整合

在本教學課程中，您將了解如何整合 PureCloud by Genesys 與 Azure Active Directory (Azure AD)。
將 PureCloud by Genesys 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 PureCloud by Genesys 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 PureCloud by Genesys (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定與 PureCloud by Genesys 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 PureCloud by Genesys 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* PureCloud by Genesys 支援由 **SP 和 IDP** 起始的 SSO

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>從資源庫新增 PureCloud by Genesys

若要設定將 PureCloud by Genesys 整合到 Azure AD 中，您需要從資源庫將 PureCloud by Genesys 新增到受控 SaaS 應用程式清單中。

**若要從資源庫新增 PureCloud by Genesys，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **PureCloud by Genesys**，從結果面板中選取 [PureCloud by Genesys]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 PureCloud by Genesys](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 PureCloud by Genesys 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 PureCloud by Genesys 中相關使用者之間的連結關聯性。

若要設定及測試與 PureCloud by Genesys 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 PureCloud by Genesys 單一登入](#configure-purecloud-by-genesys-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 PureCloud by Genesys 測試使用者](#create-purecloud-by-genesys-test-user)** - 在 PureCloud by Genesys 中建立 Britta Simon 的對應項目，且該項目須與 Azure AD 中代表使用者的項目連結。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定透過 PureCloud by Genesys 使用 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [PureCloud by Genesys]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，若您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![PureCloud by Genesys 網域和 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼]  文字方塊中，依據您的區域輸入 URL：

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. 在 [回覆 URL]  文字方塊中，依據您的區域輸入 URL：

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml` |

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    ![PureCloud by Genesys 網域和 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL]  文字方塊中，依據您的區域輸入 URL：
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

6. PureCloud by Genesys 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性 組態中。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯] ****  圖示，以開啟 [使用者屬性] ****  對話方塊。

    ![image](common/edit-attribute.png)

7. 除了以上屬性外，PureCloud by Genesys 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | Name | 來源屬性|
    | ---------------| --------------- |
    | 電子郵件 | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

8. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

9. 在 [設定 PureCloud by Genesys]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-purecloud-by-genesys-single-sign-on"></a>設定 PureCloud by Genesys 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 PureCloud by Genesys。

2. 按一下頂端的 [系統管理員]  ，然後瀏覽至 [整合]  底下的 [單一登入]  。

    ![設定單一登入](./media/purecloud-by-genesys-tutorial/configure01.png)

3. 切換至 [ADFS/Azure AD(Premium)]  索引標籤，然後執行下列步驟：

    ![設定單一登入](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. 按一下 [瀏覽]  ，將您從 Azure 入口網站下載的 base-64 編碼憑證上傳至 [ADFS 憑證]  。

    b. 在 [ADFS 簽發者 URI]  文字方塊中，貼上您從 Azure 入口網站複製的 **Azure AD 識別碼**值。

    c. 在 [目標 URI]  文字方塊中，貼上您從 Azure 入口網站複製的**登入 URL** 值。

    d. 針對 [信賴憑證者識別碼]  的值，您需要移至 Azure 入口網站，並在 [PureCloud by Genesys]  應用程式整合頁面上按一下 [屬性]  索引標籤，然後複製**應用程式識別碼**的值。 將其貼在 [信賴憑證者識別碼]  文字方塊中。 

    ![設定單一登入](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. 按一下 [儲存]    

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 PureCloud by Genesys 的存取權授與 Britta Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [PureCloud by Genesys]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [PureCloud by Genesys]  。

    ![應用程式清單中的 PureCloud by Genesys 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-purecloud-by-genesys-test-user"></a>建立 PureCloud by Genesys 測試使用者

若要讓 Azure AD 使用者能夠登入 PureCloud by Genesys，必須將他們佈建到 PureCloud by Genesys 中。 在 PureCloud by Genesys 中，需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員的身分登入 PureCloud by Genesys。

2. 按一下頂端的 [系統管理員]  ，然後瀏覽至 [人員和權限]  底下的 [人員]  。

    ![設定單一登入](./media/purecloud-by-genesys-tutorial/configure03.png)

3. 在 [人員] 頁面中，按一下 [新增人員]  。

    ![設定單一登入](./media/purecloud-by-genesys-tutorial/configure04.png)

4. 在 [將人員新增至組織]  快顯視窗中，執行下列步驟：

    ![設定單一登入](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. 在 [全名]  文字方塊中，輸入使用者的姓名，例如 **Brittasimon**。

    b. 在 [Email] \(電子郵件\)  文字方塊中，輸入使用者的電子郵件地址，例如 **brittasimon\@contoso.com**。
    
    c. 按一下頁面底部的 [新增]  。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [PureCloud by Genesys] 圖格時，應該會自動登入您已設定 SSO 的 PureCloud by Genesys。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

