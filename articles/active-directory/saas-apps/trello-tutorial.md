---
title: 教學課程：Azure Active Directory 與 Trello 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Trello 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453827b42d12333fd4f27761e7f73484fb749532
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "65865782"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>教學課程：Azure Active Directory 與 Trello 整合

在本教學課程中，您將了解如何整合 Trello 與 Azure Active Directory (Azure AD)。
Trello 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Trello 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Trello (單一登入)。
* 您可以集中管理您的帳戶：Azure 入口網站。

如需 SaaS 應用程式與 Azure AD 整合的詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Trello 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以取得[一個月的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。
* 已啟用 Trello 單一登入的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Trello 支援郵 SP 和 IDP 起始的 SSO

* Trello 支援 Just In Time 使用者佈建

## <a name="add-trello-from-the-gallery"></a>從資源庫新增 Trello

如要設定將 Trello 整合到 Azure AD 中，請先從資源庫把 Trello 新增到受控 SaaS 應用程式清單。

若要從資源庫新增 Trello，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Trello**，然後從結果窗格中選取 **Trello**。

5. 選取 [新增] 按鈕以新增應用程式。

     ![結果清單中的 Trello](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Trello 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，您必須建立 Azure AD 使用者與 Trello 中相關使用者之間的連結。

若要設定及測試與 Trello 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
2. [設定 Trello 單一登入](#configure-trello-single-sign-on)，以在應用程式端設定單一登入設定。
3. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 Britta Simon 測試 Azure AD 單一登入。
4. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. [建立 Trello 測試使用者](#create-a-trello-test-user)，讓 Trello 中有對應 Britta Simon 的使用者，並連結到該使用者在 Azure AD 中的代表項目。
6. [測試單一登入](#test-single-sign-on)，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

> [!NOTE]
> 您應該會從 Trello 取得 **\<enterprise\>** slug。 如果您沒有 Slug 值，請連絡 [Trello 支援小組](mailto:support@trello.com)以取得貴企業的 Slug。

若要設定與 Trello 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Trello] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上選取 [編輯] 圖示，以開啟 [基本 SAML 組態] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 區段中，若您想要以 IDP 起始模式設定應用程式，請執行下列步驟：

    ![Trello 網域與 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼] 方塊中，以下列模式輸入 URL：`https://trello.com/auth/saml/metadata`

    b. 在 [回覆 URL] 方塊中，以下列模式輸入 URL：`https://trello.com/auth/saml/consume/<enterprise>`

5. 如果您想要以 SP 起始模式設定應用程式，請選取 [設定其他 URL]，然後執行下列步驟：

    ![Trello 網域與 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL] 方塊中，以下列模式輸入 URL：`https://trello.com/auth/saml/login/<enterprise>`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼、回覆 URL 及登入 URL 來更新這些值。 請連絡 [Trello 用戶端支援小組](mailto:support@trello.com)以取得這些值。 您也可以參考 Azure 入口網站中 [基本 SAML 組態] 區段中的模式。

6. Trello 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性] 區段中管理這些屬性的值。 在 [以 SAML 設定單一登入] 頁面上選取 [編輯] 按鈕，以開啟 [使用者屬性] 對話方塊。

    ![[使用者屬性] 對話方塊](common/edit-attribute.png)

7. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，設定 SAML 權杖屬性，如上圖所示。 然後採取下列步驟：

    | Name |  來源屬性|
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. 選取 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![使用者宣告對話方塊](common/new-save-attribute.png)

    ![管理使用者宣告](common/new-attribute-details.png)

    b. 在 [名稱] 方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間] 保持空白。

    d. 針對 [來源]，選取 [屬性]。

    e. 在 [來源屬性] 清單中，輸入該資料列所顯示的屬性值。

    f. 選取 [確定]。

    g. 選取 [ **儲存**]。

8. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中選取 [下載]，以依據您的需求從指定的選項下載 [憑證 (Base64)]。 然後將它儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

9. 在 [設定 Trello] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-trello-single-sign-on"></a>設定 Trello 單一登入

若要設定 Trello 端的單一登入，請先將從 Azure 入口網站下載的 [憑證 (Base64)] 和複製的 URL 傳送給 [Trello 支援小組](mailto:support@trello.com)。 他們會確保兩端的 SAML SSO 連線已正確設定。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 對話方塊中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 "brittasimon@yourcompanydomain.extension"。 例如，在此情況下，您可以輸入 "BrittaSimon@contoso.com"。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Trello 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Trello]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Trello] 。

    ![應用程式清單中的 Trello 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 選取 [新增使用者] 按鈕。 然後，在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊中，選取 [使用者] 清單中的 [Britta Simon]。 然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當的使用者角色。 然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="create-a-trello-test-user"></a>建立 Trello 測試使用者

在本節中，您會在 Trello 中建立名為 Britta Simon 的使用者。 Trello 支援依預設啟用的 Just In Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Trello 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

> [!NOTE]
> 如果您需要手動建立使用者，請連絡 [Trello 支援小組](mailto:support@trello.com)。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用 MyApps 入口網站來測試您的 Azure AD 單一登入組態。

當您在 MyApps 入口網站中選取 Trello 圖格時，應該會自動登入 Trello。 如需 MyApps 入口網站的詳細資訊，請參閱[什麼是 MyApps 入口網站？](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

