---
title: 教學課程：Azure Active Directory 與 Pega Systems 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Pega Systems 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 59dc9f82251e7a406e6fe1339fdb55b4880cd74d
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649181"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>教學課程：Azure Active Directory 與 Pega Systems 整合

在本教學課程中，您會了解如何整合 Pega Systems 與 Azure Active Directory (Azure AD)。
Pega Systems 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中管控可存取 Pega Systems 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Pega Systems (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定與 Pega Systems 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Pega Systems 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Pega Systems 支援 **SP** 和 **IDP** 起始的 SSO

## <a name="adding-pega-systems-from-the-gallery"></a>從資源庫新增 Pega Systems

若要設定將 Pega Systems 整合到 Azure AD 中，您需要從資源庫將 Pega Systems 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Pega Systems，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Pega Systems**，從結果面板中選取 [Pega Systems]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 Pega Systems](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Pega Systems 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Pega Systems 中相關使用者之間的連結關聯性。

若要設定及測試與 Pega Systems 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Pega Systems 單一登入](#configure-pega-systems-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Pega Systems 測試使用者](#create-pega-systems-test-user)** - 使 Pega Systems 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Pega Systems 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Pega Systems] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![Pega Systems 網域及 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![Pega Systems 網域及 URL 單一登入資訊](common/both-advanced-urls.png)

    a. 在 [登入 URL] 文字方塊中，輸入登入 URL 值。

    b. 在 [轉送狀態] 文字方塊中，使用下列模式輸入 URL：`https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「識別碼」、「回覆 URL」、「登入 URL」及「轉送狀態 URL」來更新這些值。 您可以從本教學課程稍後所述的 Pega 應用程式找到「識別碼」和「回覆 URL」的值。 針對「回覆狀態」，請連絡 [Pega Systems 用戶端支援小組](https://www.pega.com/contact-us)以取得值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

6. Pega Systems 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性 設定中。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯] **** 圖示，以開啟 [使用者屬性] **** 對話方塊。

    ![映像](common/edit-attribute.png)

7. 除了以上屬性外，Pega Systems 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | Name | 來源屬性|
    | ------------------- | -------------------- |
    | UID | *********** |
    | cn  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | 組織 | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | 電話 | *********** |

    > [!NOTE]
    > 這些是客戶特定的值。 請提供適當的值。

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間] 保持空白。

    d. 選取 [來源] 作為 [屬性]。

    e. 在 [來源屬性] 清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]。

    g. 按一下 [檔案] 。

8. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載] 以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

9. 在 [設定 Pega Systems] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-pega-systems-single-sign-on"></a>設定 Pega Systems 單一登入

1. 若要設定 **Pega Systems** 端的單一登入，請在其他瀏覽器視窗中以管理帳戶開啟 **Pega 入口網站**。

2. 選取 [建立]  ->  [SysAdmin]  ->  [驗證服務]。

    ![設定單一登入儲存按鈕](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. 在 [建立驗證服務] 畫面上執行下列動作：

    ![設定單一登入儲存按鈕](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. 從 [類型] 選取 [SAML 2.0]

    b. 在 [名稱] 文字方塊中輸入任何名稱，例如 Azure AD SSO

    c. 在 [簡短描述] 文字方塊中，輸入任何描述  

    d. 按一下 [建立並開啟] 
    
4. 在 [識別提供者 (IdP) 資訊] 區段中，按一下 [匯入 IdP 中繼資料]，並瀏覽您從 Azure 入口網站下載的中繼資料檔案。 按一下 [提交] 以載入中繼資料。

    ![設定單一登入儲存按鈕](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
5. 這樣會填入 IdP 資料，如下所示。

    ![設定單一登入儲存按鈕](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. 在 [服務提供者 (SP) 設定] 區段上執行下列動作：

    ![設定單一登入儲存按鈕](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. 複製 [實體識別碼] 值，並將其貼至 Azure 入口網站中 [基本 SAML 設定] 中的 [識別碼] 文字方塊內。

    b. 複製 [判斷提示取用者服務 (ACS) 位置] 值，並將其貼至 Azure 入口網站中 [基本 SAML 設定] 中的 [回覆 URL] 文字方塊內。

    c. 選取 [停用要求簽署]。

7. 按一下 [儲存] 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 brittasimon@yourcompanydomain.extension。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Pega Systems 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Pega Systems]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取[Pega Systems]。

    ![應用程式清單中的 Pega Systems 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-pega-systems-test-user"></a>建立 Pega Systems 測試使用者

本節目標是在 Pega Systems 中建立名為 Britta Simon 的使用者。 請與 [Pega Systems 用戶端支援小組](https://www.pega.com/contact-us)配合，以在 Pega Sysyems 中建立使用者。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Pega Systems] 圖格時，應該會自動登入您已設定 SSO 的 Pega Systems。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)