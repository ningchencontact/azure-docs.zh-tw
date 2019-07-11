---
title: 教學課程：Azure Active Directory 與 Spotinst 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Spotinst 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5b3c9a0152144ac0d705c7d3f12c18cbc38398f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090140"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>教學課程：Azure Active Directory 與 Spotinst 整合

在本教學課程中，您將了解如何整合 Spotinst 與 Azure Active Directory (Azure AD)。
Spotinst 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Spotinst 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Spotinst (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定與 Spotinst 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 Spotinst 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Spotinst 支援由 **SP 和 IDP** 起始的 SSO

## <a name="adding-spotinst-from-the-gallery"></a>從資源庫新增 Spotinst

若要設定 Spotinst 與 Azure AD 整合，您需要從資源庫將 Spotinst 加入到受控 SaaS 應用程式清單。

**若要從資源庫新增 Spotinst，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Spotinst**，從結果面板中選取 [Spotinst]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 Spotinst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，使用 Spotinst 來設定和測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Spotinst 中相關使用者之間的連結關聯性。

若要使用 Spotinst 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Spotinst 單一登入](#configure-spotinst-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Spotinst 測試使用者](#create-spotinst-test-user)** - 在 Spotinst 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Spotinst 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Spotinst]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![Spotinst 網域與 URL 單一登入資訊](common/idp-preintegrated-relay.png)

    a. 選取 [設定額外的 URL]  。

    b. 在 [轉送狀態]  文字方塊中輸入下列值：`<ID>`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    ![Spotinst 網域與 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL]  文字方塊中，輸入 URL：`https://console.spotinst.com`

    > [!NOTE]
    > 轉送狀態的值不是真正的值。 您將會使用實際的「回覆狀態」值來更新 [回覆狀態] 值，本教學課程稍後會說明。

6. Spotinst 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性]  區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [使用者屬性]  對話方塊。

    ![image](common/edit-attribute.png)

7. 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，使用 [編輯]  圖示來編輯宣告或使用 [新增宣告]  來新增宣告，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟： 

    | Name | 來源屬性|
    | -----| --------------- |
    | 電子郵件 | user.mail |
    | 名字 | user.givenname |
    | 姓氏 | user.surname |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

8. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

9. 在 [設定 Spotinst]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-spotinst-single-sign-on"></a>設定 Spotinst 單一登入

1. 在不同的網頁瀏覽器視窗中，以安全性系統管理員身分登入 Spotinst。

2. 按一下畫面右上方的 [使用者圖示]  ，然後按一下 [設定]  。

    ![Spotinst 設定](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. 按一下頂端的 [安全性]  索引標籤，然後選取 [識別提供者]  並執行下列步驟：

    ![Spotinst 安全性](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. 複製執行個體的 [轉送狀態]  值，並將它貼到 Azure 入口網站 [基本 SAML 設定]  區段的 [轉送狀態]  文字方塊中。

    b. 按一下 [瀏覽]  ，上傳您從 Azure 入口網站下載的中繼資料 xml 檔案

    c. 按一下 [儲存]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Spotinst 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Spotinst]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Spotinst]  。

    ![應用程式清單中的 Spotinst 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-spotinst-test-user"></a>建立 Spotinst 測試使用者

本節目標是在 Spotinst 中建立名為 Britta Simon 的使用者。

1. 如果您已經以 **SP** 起始模式設定應用程式，請執行下列步驟：

   a. 在不同的網頁瀏覽器視窗中，以安全性系統管理員身分登入 Spotinst。

   b. 按一下畫面右上方的 [使用者圖示]  ，然後按一下 [設定]  。

    ![Spotinst 設定](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. 按一下 [使用者]  ，然後選取 [新增使用者]  。

    ![Spotinst 設定](./media/spotinst-tutorial/adduser1.png)

    d. 在 [新增使用者] 區段中，執行下列步驟：

    ![Spotinst 設定](./media/spotinst-tutorial/adduser2.png)

    * 在 [全名]  文字方塊中，輸入使用者的全名，例如 **BrittaSimon**。

    * 在 [電子郵件]  文字方塊中，輸入使用者的電子郵件地址，例如 `brittasimon\@contoso.com`。

    * 針對 [組織角色、帳戶角色和帳戶]  選取組織特定詳細資料。

2. 如果您已經以 **IDP** 起始模式設定應用程式，則這個區段沒有適用的動作項目。 Spotinst 支援預設啟用的 Just-In-Time 佈建。 當您嘗試存取 Spotinst 時，如果 Spotinst 還沒有使用者，它將會建立新的使用者。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Spotinst] 圖格時，應該會自動登入您已設定 SSO 的 Spotinst。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

