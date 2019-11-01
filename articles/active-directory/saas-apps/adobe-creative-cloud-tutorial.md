---
title: 教學課程：Azure Active Directory 與 Adobe Creative Cloud 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Adobe Creative Cloud 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f3fdd1aa4fa655cfffb9e9a2f29d551133613a8
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154006"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>教學課程：Azure Active Directory 與 Adobe Creative Cloud 整合

在本教學課程中，您會了解如何整合 Adobe Creative Cloud 與 Azure Active Directory (Azure AD)。
Adobe Creative Cloud 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Adobe Creative Cloud 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Adobe Creative Cloud (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Adobe Creative Cloud 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Adobe Creative Cloud 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Adobe Creative Cloud 支援由 **SP** 初始化的 SSO

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>從資源庫新增 Adobe Creative Cloud

若要設定將 Adobe Creative Cloud 整合至 Azure AD 中，您需要從資源庫將 Adobe Creative Cloud 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 Adobe Creative Cloud，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Adobe Creative Cloud**，從結果面板選取 [Adobe Creative Cloud]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 Adobe Creative Cloud](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Adobe Creative Cloud 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Adobe Creative Cloud 中相關使用者之間的連結關聯性。

若要設定及測試與 Adobe Creative Cloud 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Adobe Creative Cloud 單一登入](#configure-adobe-creative-cloud-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Adobe Creative Cloud 測試使用者](#create-adobe-creative-cloud-test-user)** - 在 Adobe Creative Cloud 中，建立一個與 Azure AD 中代表使用者的項目連結的 Britta Simon 對應項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Adobe Creative Cloud 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Adobe Creative Cloud]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Adobe Creative Cloud 網域和 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL]  文字方塊中，將值輸入為：`https://adobe.com`。

    b. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > 識別碼值不是實際值。 請使用實際的「識別碼」來更新此值。 請連絡 [Adobe Creative Cloud 用戶端支援小組](https://www.adobe.com/au/creativecloud/business/teams/plans.html)以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. Adobe Creative Cloud 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性]  區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [使用者屬性]  對話方塊。

    ![映像](common/edit-attribute.png)

6. 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    | 名稱 | 來源屬性|
    |----- | --------- |
    | 名字 | user.givenname |
    | 姓氏 | user.surname |
    | 電子郵件 | user.mail

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

    > [!NOTE]
    > 使用者需要具備有效的 Office 365 ExO 授權，電子郵件宣告值才會填入 SAML 回應中。

7. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

8. 在 [安裝 Adobe Creative Cloud]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>設定 Adobe Creative Cloud 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 [Adobe Admin Console](https://adminconsole.adobe.com)。

2. 移至上方導覽列中的 [設定]  ，然後選擇 [身分識別]  。 網域清單隨即開啟。 按一下您網域的 [設定]  連結。 接著執行 [需要單一登入組態]  區段中的下列步驟。 如需詳細資訊，請參閱[設定網域](https://helpx.adobe.com/enterprise/using/set-up-domain.html)。

    ![設定](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "設定")

    a. 按一下 [瀏覽]  ，將已從 Azure AD 下載的憑證上傳至 **IDP 憑證**。

    b. 在 [IDP 簽發者]  文字方塊中，放入 [SAML 實體 ID]  的值，該值是您從 Azure 入口網站的 [設定登入]  區段複製而來。

    c. 在 [IDP 登入 URL]  文字方塊中，放入 [SAML SSO 服務 URL]  的值，該值是您從 Azure 入口網站的 [設定登入]  區段複製而來。

    d. 選取 [HTTP - 重新導向]  作為 **IDP 繫結**。

    e. 選取 [電子郵件地址]  作為**使用者登入設定**。

    f. 按一下 [儲存]  按鈕。

3. 儀表板現在會顯示 XML「下載中繼資料」  檔案。 它包含 Adobe 的 EntityDescriptor URL 和 AssertionConsumerService URL。 請開啟檔案，然後在 Azure AD 應用程式中加以設定。

    ![在應用程式端設定單一登入](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. 在 [設定應用程式設定]  對話方塊上，使用 Adobe 提供給您的 EntityDescriptor 值作為**識別碼**。

    b. 在 [設定應用程式設定]  對話方塊上，使用 Adobe 提供給您的 AssertionConsumerService 值作為**回覆 URL**。

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

在本節中，您會將 Adobe Creative Cloud 的存取權授與 Britta Simon，讓他能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Adobe Creative Cloud]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Adobe Creative Cloud]  。

    ![應用程式清單中的 Adobe Creative Cloud 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-adobe-creative-cloud-test-user"></a>建立 Adobe Creative Cloud 測試使用者

若要讓 Azure AD 使用者能夠登入 Adobe Creative Cloud，必須將他們佈建到 Adobe Creative Cloud。 Adobe Creative Cloud 需以手動方式佈建。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要佈建使用者帳戶，請執行下列步驟：

1. 以系統管理員身分登入 [Adobe Admin Console](https://adminconsole.adobe.com)。

2. 在 Adobe 主控台內將使用者新增為同盟識別碼，並將它們指派給產品設定檔。 如需新增使用者的詳細資訊，請參閱[在 Adobe Admin Console 中新增使用者](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. 此時，在 Adobe 登入表單中輸入您的電子郵件地址/upn，按下 tab 鍵，隨後應備份至 Azure AD 同盟：
   * Web 存取：www\.adobe.com > 登入
   * 在桌面應用程式公用程式內 > 登入
   * 在應用程式內 > 說明 > 登入

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Adobe Creative Cloud] 圖格時，應該會自動登入您設定 SSO 的 Adobe Creative Cloud。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [設定網域 (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [設定 Azure 以便搭配 Adobe SSO (adobe.com) 使用](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)
