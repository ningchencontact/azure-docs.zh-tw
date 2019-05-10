---
title: 教學課程：Azure Active Directory 與 OpenAthens 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 OpenAthens 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cbb1bc64d923add0b1fd38749299714a9093633
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408026"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>教學課程：Azure Active Directory 與 OpenAthens 整合

在本教學課程中，您會了解如何將 OpenAthens 與 Azure Active Directory (Azure AD) 進行整合。
OpenAthens 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中管控可存取 OpenAthens 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 OpenAthens (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 OpenAthens 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 OpenAthens 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* OpenAthens 支援由 **IDP** 起始的 SSO

* OpenAthens 支援 **Just In Time** 使用者佈建

## <a name="adding-openathens-from-the-gallery"></a>從資源庫新增 OpenAthens

若要設定將 OpenAthens 整合到 Azure AD 中，您需要從資源庫將 OpenAthens 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 OpenAthens，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **OpenAthens**，從結果面板中選取 [OpenAthens]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 OpenAthens](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 OpenAthens 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 OpenAthens 中相關使用者之間的連結關聯性。

若要使用 OpenAthens 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 OpenAthens 單一登入](#configure-openathens-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 OpenAthens 測試使用者](#create-openathens-test-user)** - 在 OpenAthens 中建立 Britta Simon 的對應項目，且該項目須與 Azure AD 中代表使用者的項目連結。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 OpenAthens 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [OpenAthens] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

5. 在 [基本 SAML 組態] 區段上，上傳**服務提供者中繼資料檔案**，並執行稍後在本教學課程中提及的步驟。

    a. 按一下 [上傳中繼資料檔案]。

    ![openathens 上傳中繼資料](common/upload-metadata.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]。

    ![Openathens 瀏覽上傳中繼資料](common/browse-upload-metadata.png)

    c. 成功上傳中繼資料檔案後，就會在 [基本 SAML 組態] 區段文字方塊中自動填入 [識別碼] 值：

    ![OpenAthens 網域及 URL 單一登入資訊](common/idp-identifier.png)

6. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>設定 OpenAthens 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 OpenAthens 公司網站。

2. 在 [管理] 索引標籤下方的清單選取 [連線]。 

    ![設定單一登入](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. 選取 [SAML 1.1/2.0]，然後選取 [設定] 按鈕。

    ![設定單一登入](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. 若要新增設定，請選取 [瀏覽] 按鈕以上傳您從 Azure 入口網站下載的中繼資料 .xml 檔案，然後選取 [新增]。

    ![設定單一登入](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. 在 [詳細資料] 索引標籤下方執行以下步驟。

    ![設定單一登入](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. 在 [顯示名稱對應] 中，選取 [使用屬性]。

    b. 在 [顯示名稱屬性] 文字方塊中，輸入 `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 值。
    
    c. 在 [唯一使用者對應] 中，選取 [使用屬性]。

    d. 在 [唯一使用者屬性] 文字方塊中，輸入 `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 值。

    e. 在 [狀態] 中，勾選三個核取方塊。

    f. 在 [建立本機帳戶] 中，選取 [自動]。

    g. 選取 [儲存變更]。

    h. 從 [</> 信賴憑證者] 索引標籤，複製 [中繼資料 URL] ，並在瀏覽器中開啟此 URL 以下載 **SP 中繼資料 XML** 檔案。 在 Azure AD 中的 [基本 SAML 組態] 區段上，上傳此 SP 中繼資料檔案。

    ![設定單一登入](./media/openathens-tutorial/tutorial_openathens_application5.png)

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

在本節中，您會將 OpenAthens 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [OpenAthens]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入 **OpenAthens** 並加以選取。

    ![應用程式清單中的 OpenAthens 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-openathens-test-user"></a>建立 OpenAthens 測試使用者

本節會在 OpenAthens 中建立名為 Britta Simon 的使用者。 OpenAthens 支援依預設啟用的 **Just-In-Time 使用者佈建**。 在這一節沒有您需要進行的動作項目。 如果 OpenAthens 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 OpenAthens 圖格時，應該會自動登入您已設定 SSO 的 OpenAthens。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

