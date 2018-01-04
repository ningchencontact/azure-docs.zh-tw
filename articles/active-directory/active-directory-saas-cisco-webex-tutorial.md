---
title: "教學課程： Azure Active Directory 與 Cisco Webex 的整合 |Microsoft 文件"
description: "了解如何設定單一登入 Azure Active Directory 與 Cisco Webex 之間。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory7
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 42632dcf8997ec5e987ac8a6615aae24e903399a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>教學課程： Azure Active Directory 與 Cisco Webex 的整合

在本教學課程中，您可以了解如何與 Azure Active Directory (Azure AD) 整合 Cisco Webex。

Cisco Webex 整合與 Azure AD 可以提供下列優點：

- 您可以控制可以存取 Cisco Webex 的 Azure AD 中。
- 您可以啟用自動取得登入 Cisco Webex 其 Azure AD 帳戶與您的使用者。
- 您可以在 Azure 入口網站集中管理您的帳戶。

如需有關 SaaS 應用程式與 Azure AD 整合的詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 整合與 Cisco Webex，您需要下列項目：

- Azure AD 訂用帳戶
- Cisco Webex 單一登入-啟用的訂閱

> [!NOTE]
> 我們不建議使用生產環境來測試本教學課程中的步驟。

若要測試本教學課程中的步驟，請遵循下列建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，可以[取得一個月的免費試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫加入 Cisco Webex
2. 設定並測試 Azure AD 單一登入

## <a name="add-cisco-webex-from-the-gallery"></a>從資源庫加入 Cisco Webex
若要設定 Cisco Webex 整合 Azure AD，您需要加入 Cisco Webex 從資源庫，您的受管理的 SaaS 應用程式清單。

**若要從資源庫加入 Cisco Webex，採取下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 移至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請選取對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在 [搜尋] 方塊中，輸入**Cisco Webex**。 

5. 選取**Cisco Webex**從 [結果] 窗格。 然後選取**新增**按鈕以加入應用程式。

    ![在 [結果] 清單中的 cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，設定並測試 Azure AD 單一登入與 Cisco Webex 根據測試使用者稱為 「 許 Simon。 」

單一登入工作，如 Azure AD 需要知道誰在 Cisco Webex 的對等項目的使用者是使用者在 Azure AD 中。 換句話說，您需要為 Cisco Webex 中建立 Azure AD 使用者和相關的使用者之間的連結。

在 Cisco Webex 價值**Username**相同的值做**使用者名稱**在 Azure AD 中。 現在您已經建立兩名使用者之間的連結。 

若要設定和測試 Azure AD 單一登入與 Cisco Webex，請完成下列的建置組塊：

1. [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
2. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 Britta Simon 測試 Azure AD 單一登入。
3. [建立測試使用者 Cisco Webex](#create-a-cisco-webex-test-user)能夠許 Simon 對應項目連結至使用者的 Azure AD 表示的 Cisco Webex。
4. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. [測試單一登入](#test-single-sign-on)，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您可以啟用 Azure AD 單一登入 Azure 入口網站中，並設定單一登入 Cisco Webex 的應用程式中。

**若要設定 Azure AD 單一登入與 Cisco Webex，採取下列步驟：**

1. 在 Azure 入口網站上**Cisco Webex**應用程式整合頁面上，選取**單一登入**。

    ![設定單一登入連結][4]

2. 若要啟用單一登入，在**單一登入**對話方塊中，於**模式**下拉式清單中，選取**SAML 型登入**。
 
    ![單一登入對話方塊](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. 在**Cisco Webex 網域和 Url**區段中，執行下列步驟：

    ![Cisco Webex 網域和 Url 的單一登入資訊](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. 在 [登入 URL] 方塊中，以下列模式輸入 URL：`https://<subdomain>.webex.com`

    b. 在**識別碼**方塊中，輸入 URL `http://www.webex.com`。

    c. 在 [回覆 URL] 方塊中，使用下列模式輸入 URL：`https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > 這些都不是真正的值。 更新這些值與實際的回覆 URL 並登入 URL。 請連絡[Cisco Webex 用戶端支援小組](https://www.webex.co.in/support/support-overview.html)取得這些值。 

5. 在**SAML 簽章憑證**區段中，選取**中繼資料 XML**，然後儲存您的電腦上的中繼資料檔案。

    ![憑證下載連結](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. 選取 [ **儲存**]。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. 在**Cisco Webex 設定**區段中，選取**設定 Cisco Webex**開啟**設定登入**視窗。 從 [快速參考] 區段中複製 [登出 URL]、[SAML 實體識別碼]，以及 [SAML 單一登入服務 URL]。

    ![設定單一登入](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. 在不同的網頁瀏覽器視窗中，您的 Cisco Webex 公司網站管理員身分登入。

8. 在頂端功能表中，從選取**網站管理**。

    ![網站管理](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "網站管理")

9. 在**管理站台**區段中，選取**SSO 組態**。
   
    ![SSO 組態](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "SSO 組態")

10. 在**同盟網頁 SSO 組態**區段中，執行下列步驟：
   
    ![同盟 SSO 組態](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "同盟 SSO 組態")  

    a. 在**同盟通訊協定**清單中，選取**SAML 2.0**。

    b. 如**SSO 設定檔**，選取**SP 起始**。

    c. 在 [記事本] 中開啟下載的憑證，然後複製其內容。

    d. 選取**匯入 SAML 中繼資料**，然後貼上複製的內容的憑證。

    e. 在**SAML (IdP ID) 的簽發者**方塊中，貼上的值**SAML 實體識別碼**您從 Azure 入口網站複製。

    f. 在**客戶 SSO 服務登入 URL**方塊中，貼上**SAML 單一登入服務 URL**，您從 Azure 入口網站複製的。

    g. 從 [NameID 格式] 清單中選取 [電子郵件地址]。

    h. 在**AuthnContextClassRef**方塊中，輸入**urn: oasis： 名稱： tc: SAML:2.0:ac:classes:Password**。

    i. 在**客戶 SSO 服務登出 URL**方塊中，貼上**登出 URL**，您從 Azure 入口網站複製的。
   
    j. 選取**更新**。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本。 您將從這個應用程式之後**Active Directory** > **企業應用程式**區段中，選取**單一登入**索引標籤，然後再存取 內嵌透過文件**組態**底部區段。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件](https://go.microsoft.com/fwlink/?linkid=845985)。

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

若要讓 Azure AD 使用者能夠登入 Cisco Webex，他們必須提供在 Cisco Webex。 Cisco Webex 需以手動方式佈建。

**若要佈建使用者帳戶，請採取下列步驟：**

1. 登入您**Cisco Webex**租用戶。

2. 移至**管理使用者** > **將使用者加入**。
   
    ![新增使用者](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "新增使用者")

3. 在**新增使用者**區段中，執行下列步驟：
   
    ![新增使用者](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "新增使用者")   

    a. 如**帳戶類型**，選取**主機**。

    b. 在**名字**方塊中，輸入使用者的名字 (在此情況下，**許**)。

    c. 在**姓氏**方塊中，輸入使用者的姓氏 (在此情況下， **Simon**)。

    d. 在**Username**方塊中，輸入使用者的電子郵件 (在此情況下，  **Brittasimon@contoso.com** )。

    e. 在**電子郵件**方塊中，輸入使用者的電子郵件地址 (在此情況下，  **Brittasimon@contoso.com** )。

    f. 在 [密碼] 方塊中，輸入使用者的密碼。

    g. 在**確認**密碼方塊中，重新輸入使用者的密碼。

    h. 選取 [新增] 。

>[!NOTE]
>您可以使用任何其他 Cisco Webex 使用者帳戶建立工具或 Api 來佈建 Azure AD 使用者帳戶的 Cisco Webex 所提供。 

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您可以讓使用者使用 Azure 單一登入授與他們存取給 Cisco Webex 許 Simon。

![指派使用者角色][200] 

**若要指派給 Cisco Webex 的許 Simon，採取下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視。 下一步，移至 目錄 檢視中，再**企業應用程式**。  

2. 選取 [所有應用程式]。

    ![指派使用者][201] 

3. 在應用程式清單中，選取**Cisco Webex**。

    ![應用程式清單中的 [Cisco Webex] 連結](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 選取 [新增] 按鈕。 然後選取**使用者和群組**中**將作業加入** 對話方塊。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊中，選取 [使用者] 清單中的 [Britta Simon]。

6. 在 [使用者和群組] 對話方塊中，按一下 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您選取 Cisco Webex 磚存取面板中時，您會自動取得登入 Cisco Webex 的應用程式。

如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

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

