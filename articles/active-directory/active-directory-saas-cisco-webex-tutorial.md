---
title: 教學課程：Azure Active Directory 與 Cisco Webex 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Cisco Webex 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 8655340f803ec5a976d482939e666f69cf900812
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234983"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>教學課程：Azure Active Directory 與 Cisco Webex 整合

在本教學課程中，您將了解如何整合 Cisco Webex 與 Azure Active Directory (Azure AD)。

將 Cisco Webex 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Cisco Webex 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Cisco Webex。
- 您可以在 Azure 入口網站集中管理您的帳戶。

如需有關 SaaS 應用程式與 Azure AD 整合的詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先決條件

若要設定 Azure AD 與 Cisco Webex 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Cisco Webex 單一登入的訂用帳戶

> [!NOTE]
> 我們不建議使用生產環境來測試本教學課程中的步驟。

若要測試本教學課程中的步驟，請遵循下列建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，可以[取得一個月的免費試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Cisco Webex
2. 設定並測試 Azure AD 單一登入

## <a name="add-cisco-webex-from-the-gallery"></a>從資源庫新增 Cisco Webex
若要設定將 Cisco Webex 整合到 Azure AD 中，您需要將 Cisco Webex 從資源庫新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Cisco Webex，請執行下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 移至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請選取對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Cisco Webex**。 

5. 從結果面板中選取 [Cisco Webex]。 然後選取 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Cisco Webex 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Cisco Webex 與 Azure AD 中互相對應的使用者。 換句話說，您必須在 Azure AD 使用者與 Cisco Webex 中的相關使用者之間建立連結。

在 Cisco Webex 中，為 [Username] \(使用者名稱\) 值提供與 Azure AD 中**使用者名稱**相同的值。 現在您已經建立兩名使用者之間的連結。 

若要設定及測試與 Cisco Webex 搭配運作的 Azure AD 單一登入，請完成下列構成要素：

1. [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
2. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 Britta Simon 測試 Azure AD 單一登入。
3. [建立 Cisco Webex 測試使用者](#create-a-cisco-webex-test-user)，以在 Cisco Webex 中建立一個與 Azure AD 中代表 Britta Simon 之項目連結的對應項目。
4. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. [測試單一登入](#test-single-sign-on)，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Cisco Webex 應用程式中設定單一登入。

**若要設定與 Cisco Webex 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Cisco Webex] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結][4]

2. 若要啟用單一登入，請在 [單一登入] 對話方塊的 [模式] 下拉式清單中，選取 [SAML 登入]。
 
    ![單一登入對話方塊](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. 在 [Cisco Webex 網域及 URL] 區段中，執行下列步驟：

    ![Cisco Webex 網域及 URL 單一登入資訊](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. 在 [登入 URL] 方塊中，以下列模式輸入 URL：`https://<subdomain>.webex.com`

    b. 在 [識別碼] 方塊中，輸入 URL `http://www.webex.com`。

    c. 在 [回覆 URL] 方塊中，使用下列模式輸入 URL：`https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > 這些都不是真正的值。 請使用實際的回覆 URL 和登入 URL 來更新這些值。 請連絡 [Cisco Webex 用戶端支援小組](https://www.webex.co.in/support/support-overview.html) \(英文\) 以取得這些值。 

5. 在 [SAML 簽署憑證] 區段上，選取 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. 選取 [ **儲存**]。

    ![設定單一登入 [儲存] 按鈕](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. 在 [Cisco Webex 設定] 區段中，選取 [設定 Cisco Webex] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [登出 URL]、[SAML 實體識別碼]，以及 [SAML 單一登入服務 URL]。

    ![設定單一登入](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Cisco Webex 公司網站。

8. 在頂端的功能表中，選取 [Site Administration] \(網站管理\)。

    ![網站管理](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "網站管理")

9. 在 [Manage Site] \(管理網站\) 區段中，選取 [SSO Configuration] \(SSO 設定\)。
   
    ![SSO 組態](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "SSO 組態")

10. 在 [Federated Web SSO Configuration] \(同盟 Web SSO 設定\) 區段中，執行下列步驟：
   
    ![同盟 SSO 組態](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "同盟 SSO 組態")  

    a. 從 [Federation Protocol] \(同盟通訊協定\) 清單中，選取 [SAML 2.0]。

    b. 針對 [SSO profile] \(SSO 設定檔\)，選取 [SP Initiated] \(SP 起始\)。

    c. 在「記事本」中開啟所下載的憑證，然後複製其內容。

    d. 選取 [Import SAML Metadata] \(匯入 SAML 中繼資料\)，然後貼上所複製的憑證內容。

    e. 在 [Issuer for SAML (IdP ID)] \(SAML 的簽發者 (IdP ID)\) 方塊中，貼上您從 Azure 入口網站複製的 [SAML 實體識別碼] 值。

    f. 在 [Customer SSO Service Login URL] \(客戶 SSO 服務登入 URL\) 方塊中，貼上您從 Azure 入口網站複製的 **SAML 單一登入服務 URL**。

    g. 從 [NameID 格式] 清單中選取 [電子郵件地址]。

    h. 在 [AuthnContextClassRef] 方塊中，輸入 **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**。

    i. 在 [Customer SSO Service Logout URL] \(客戶 SSO 服務登出 URL\) 方塊中，貼上您從 Azure 入口網站複製的**登出 URL**。
   
    j. 選取 [更新]。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本。 從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，請選取 [單一登入] 索引標籤，然後透過底部的 [設定] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件](https://go.microsoft.com/fwlink/?linkid=845985)。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左側窗格中，選取 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後選取 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，請選取 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，採取下列步驟：

    ![[使用者] 對話方塊](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。
 
### <a name="create-a-cisco-webex-test-user"></a>建立 Cisco Webex 測試使用者

若要讓 Azure AD 使用者能夠登入 Cisco Webex，必須在 Cisco Webex 中佈建這些使用者。 Cisco Webex 需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 登入您的 **Cisco Webex** 租用戶。

2. 移至 [Manage Users] \(管理使用者\) > [Add User] \(加入使用者\)。
   
    ![新增使用者](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "新增使用者")

3. 在 [Add User] \(新增使用者\) 區段中，執行下列步驟：
   
    ![新增使用者](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "新增使用者")   

    a. 針對 [Account Type] \(帳戶類型\)，選取 [Host] \(主機\)。

    b. 在 [First name] \(名字\) 方塊中，輸入使用者的名字 (在此案例中為 **Britta**)。

    c. 在 [Last name] \(姓氏\) 方塊中，輸入使用者的姓氏 (在此案例中為 **Simon**)。

    d. 在 [Username] \(使用者名稱\) 方塊中，輸入使用者的電子郵件 (在此案例中為 **Brittasimon@contoso.com**)。

    e. 在 [Email] \(電子郵件\) 方塊中，輸入使用者的電子郵件地址 (在此案例中為 **Brittasimon@contoso.com**)。

    f. 在 [密碼] 方塊中，輸入使用者的密碼。

    g. 在 [Confirm Password] \(確認密碼\) 方塊中，重新輸入使用者的密碼。

    h. 選取 [新增] 。

>[!NOTE]
>您可以使用任何其他 Cisco Webex 使用者帳戶建立工具或 Cisco Webex 所提供的 API 來佈建 Azure AD 使用者帳戶。 

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Cisco Webex 的存取權授與使用者 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者角色][200] 

**若要將 Britta Simon 指派給 Cisco Webex，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視。 接著，移至目錄檢視，然後移至 [企業應用程式]。  

2. 選取 [所有應用程式]。

    ![指派使用者][201] 

3. 在應用程式清單中，選取 [Cisco Webex]。

    ![應用程式清單中的 [Cisco Webex] 連結](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 選取 [新增] 按鈕。 然後，在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊中，選取 [使用者] 清單中的 [Britta Simon]。

6. 在 [使用者和群組] 對話方塊中，按一下 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 [Cisco Webex] 圖格時，會自動登入您的 Cisco Webex 應用程式。

如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_203.png

