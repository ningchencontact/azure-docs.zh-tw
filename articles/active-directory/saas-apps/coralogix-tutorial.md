---
title: 教學課程：Azure Active Directory 與 Coralogix 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Coralogix 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c25c57dec14a81e5bcfcfa044cf5d5302e1c88
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578810"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>教學課程：Azure Active Directory 與 Coralogix 整合

在本教學課程中，您會了解如何整合 Coralogix 與 Azure Active Directory (Azure AD)。
Coralogix 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Coralogix 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Coralogix (單一登入)。
* 您可以集中管理您的帳戶：Azure 入口網站。

如需 SaaS 應用程式與 Azure AD 整合的詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定與 Coralogix 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以取得[一個月的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。
- 已啟用 Coralogix 單一登入的訂用帳戶。 

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Coralogix 支援郵 SP 起始的 SSO。

## <a name="add-coralogix-from-the-gallery"></a>從資源庫新增 Coralogix

若要設定將 Coralogix 整合到 Azure AD 中，先從資源庫將 Coralogix 新增到受控 SaaS 應用程式清單。

若要從資源庫新增 Coralogix，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Coralogix**。 從結果面板中選取 [Coralogix]，然後選取 [新增] 按鈕以新增應用程式。

     ![結果清單中的 Coralogix](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 Britta Simon 的測試使用者身分，使用 Coralogix 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，您必須建立 Azure AD 使用者與 Coralogix 中相關使用者之間的連結。

若要設定及測試與 Coralogix 搭配運作的 Azure AD 單一登入，請先完成下列建構元素：

1. [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
2. [設定 Coralogix 單一登入](#configure-coralogix-single-sign-on)，以在應用程式端設定單一登入設定。
3. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 Britta Simon 測試 Azure AD 單一登入。
4. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. [建立 Coralogix 測試使用者](#create-a-coralogix-test-user)，使 Coralogix 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. [測試單一登入](#test-single-sign-on)，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Coralogix 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Coralogix] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上選取 [編輯] 圖示，以開啟 [基本 SAML 組態] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 對話方塊中，執行下列步驟：

    ![Coralogix 網域與 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL] 方塊中，以下列模式輸入 URL︰`https://<SUBDOMAIN>.coralogix.com`

    b. 在 [識別碼 (實體識別碼)] 文字方塊中，輸入 URL，例如：
    
    `https://api.coralogix.com/saml/metadata.xml`

    或

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > 登入 URL 值不是真正的值。 請使用實際的登入 URL 來更新此值。 請連絡 [Coralogix 客戶支援小組](mailto:info@coralogix.com)以取得此值。 您也可以參考 Azure 入口網站中 [基本 SAML 組態] 區段中的模式。

5. Coralogix 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性] 區段中管理這些屬性的值。 在 [以 SAML 設定單一登入] 頁面上選取 [編輯] 按鈕，以開啟 [使用者屬性] 對話方塊。

    ![映像](common/edit-attribute.png)

6. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，使用 [編輯] 圖示來編輯宣告。 您也可使用 [新增宣告] 來新增宣告，以設定 SAML 權杖屬性，如上圖所示。 然後採取下列步驟：
    
    a. 選取 [編輯] 圖示以開啟 [管理使用者宣告] 對話方塊。

    ![映像](./media/coralogix-tutorial/tutorial_usermail.png) ![映像](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. 從 [選擇名稱識別碼格式] 清單中選取 [電子郵件地址]。

    c. 從 [來源屬性] 清單中，選取 [user.mail]。

    d. 選取 [ **儲存**]。

7. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，選取 [下載]，以依據您的需求從指定的選項下載 [同盟中繼資料 XML]。 然後將它儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

8. 在 [設定 Coralogix] 區段中，複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-coralogix-single-sign-on"></a>設定 Coralogix 單一登入

若要在 **Coralogix** 端設定單一登入，請將從 Azure 入口網站下載的 [同盟中繼資料 XML] 和複製的 URL 傳送給 [Coralogix 支援小組](mailto:info@coralogix.com)。 他們會確保兩端的 SAML SSO 連線已正確設定。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 對話方塊中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 "brittasimon@yourcompanydomain.extension"。 例如，在此情況下，您可以輸入 "brittasimon@contoso.com"。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Coralogix 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Coralogix]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Coralogix]。

    ![應用程式清單中的 Coralogix 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 選取 [新增使用者] 按鈕。 然後，在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊中，選取 [使用者] 清單中的 [Britta Simon]。 然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的角色值，請在 [選取角色] 對話方塊的清單中選取適當的使用者角色。 然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="create-a-coralogix-test-user"></a>建立 Coralogix 測試使用者

在本節中，您要在 Coralogix 中建立名為 Britta Simon 的使用者。 請與  [Coralogix 支援小組](mailto:info@coralogix.com)合作，以在 Coralogix 平台中新增使用者。 您必須先建立和啟動使用者，才能使用單一登入。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用 MyApps 入口網站來測試您的 Azure AD 單一登入組態。

當您在 MyApps 入口網站中選取 Coralogix 圖格時，應該會自動登入 Coralogix。 如需 MyApps 入口網站的詳細資訊，請參閱[什麼是 MyApps 入口網站？](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

