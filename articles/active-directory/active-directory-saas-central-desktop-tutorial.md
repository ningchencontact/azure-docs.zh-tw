---
title: "教學課程：Azure Active Directory 與 Central Desktop 整合 | Microsoft Docs"
description: "了解如何設定單一登入 Azure Active Directory 與 Central Desktop 之間。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 94c67bef7a0c6ba60fc9c7a60c79a23bf7984fb1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>教學課程：Azure Active Directory 與 Central Desktop 整合

在本教學課程中，您可以了解如何與 Azure Active Directory (Azure AD) 整合 Central Desktop。

整合 Azure AD 與 Central Desktop 可以提供下列優點：

- 您可以控制可以存取 Central Desktop 的 Azure AD 中。
- 您可以啟用自動取得登入 Central Desktop 其 Azure AD 帳戶與您的使用者。
- 您可以在 Azure 入口網站集中管理您的帳戶。

如需有關 SaaS 應用程式與 Azure AD 整合的詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 整合與 Central Desktop，您需要下列項目：

- Azure AD 訂用帳戶
- Central Desktop 單一登入-啟用的訂閱

> [!NOTE]
> 我們不建議使用生產環境來測試本教學課程中的步驟。

若要測試本教學課程中的步驟，請遵循下列建議：

- 除非必要，否則請勿使用生產環境。
- 如果您還沒有 Azure AD 試用的環境，[取得一個月的免費試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫加入 Central Desktop
2. 設定並測試 Azure AD 單一登入

## <a name="add-central-desktop-from-the-gallery"></a>從資源庫加入 Central Desktop
若要設定 Central Desktop 整合 Azure AD，您需要加入 Central Desktop 從資源庫，您的受管理的 SaaS 應用程式清單。

**若要從資源庫加入 Central Desktop，採取下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 移至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要加入新的應用程式，請選取**新的應用程式**對話方塊頂端的按鈕。

    ![新增應用程式按鈕][3]

4. 在 [搜尋] 方塊中，輸入**Central Desktop**。 選取**Central Desktop**結果面板，然後選取從**新增**按鈕以加入應用程式。

    ![在 [結果] 清單中的中央桌面](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，設定並測試 Azure AD 單一登入與 Central Desktop 根據測試使用者稱為 「 許 Simon。 」

單一登入工作，如 Azure AD 需要知道誰在 Central Desktop 的對等項目的使用者是使用者在 Azure AD 中。 換句話說，您需要在 Central Desktop 中建立 Azure AD 使用者和相關的使用者之間的連結。

在 Central Desktop，會產生**Username**相同的值做**使用者名**在 Azure AD 中。 現在您已經建立兩名使用者之間的連結。

若要設定和測試 Azure AD 單一登入與 Central Desktop，您需要完成下列的建置組塊：

1. [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
2. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 Britta Simon 測試 Azure AD 單一登入。
3. [建立測試使用者 Central Desktop](#create-a-central-desktop-test-user)連結至使用者的 Azure AD 表示的 Central Desktop 中有許 Simon 對應項目。
4. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. [測試單一登入](#test-single-sign-on)，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您可以啟用 Azure AD 單一登入 Azure 入口網站中，並設定單一登入 Central Desktop 應用程式中。

**若要設定 Azure AD 單一登入與 Central Desktop，採取下列步驟：**

1. 在 Azure 入口網站上**Central Desktop**應用程式整合頁面上，選取**單一登入**。

    ![設定單一登入連結][4]

2. 若要啟用單一登入，在**單一登入**對話方塊中，於**模式**下拉式清單中，選取**SAML 型登入**。
 
    ![單一登入對話方塊](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. 在**Central Desktop 網域和 Url**區段中，執行下列步驟：

    ![中央桌面網域和 Url 的單一登入資訊](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. 在 [登入 URL] 方塊中，以下列模式輸入 URL：`https://<companyname>.centraldesktop.com`

    b. 在**識別碼**方塊中，輸入的 URL 使用下列模式：
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. 在 [回覆 URL] 方塊中，使用下列模式輸入 URL：`https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > 這些都不是真正的值。 更新這些值與實際的識別項，回覆 URL 和登入 URL。 請連絡[Central Desktop 用戶端支援小組](https://imeetcentral.com/contact-us)取得這些值。 

4. 在**SAML 簽章憑證**區段中，選取**憑證**。 然後儲存您的電腦上的憑證檔案。

    ![憑證下載連結](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. 選取 [儲存] 按鈕。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-central-desktop-tutorial/tutorial_general_400.png)
    
6. 在**Central Desktop 組態**區段中，選取**設定 Central Desktop**開啟**設定登入**視窗。 從 [快速參考] 區段中複製 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]。

    ![中央桌面設定](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. 登入您**Central Desktop**租用戶。

8. 移至 [設定] 。 選取**進階**，然後選取**單一登入**。

    ![設定 - 進階](./media/active-directory-saas-central-desktop-tutorial/ic769563.png "設定 - 進階")

9. 在**單一登入設定**頁面上，採取下列步驟：

    ![單一登入設定](./media/active-directory-saas-central-desktop-tutorial/ic769564.png "單一登入設定")
    
    a. 選取 [啟用 SAML v2 單一登入] 。
    
    b. 在**SSO URL**方塊中，貼上**SAML 實體識別碼**您從 Azure 入口網站複製的值。
    
    c. 在**SSO 登入 URL**方塊中，貼上**SAML 單一登入服務 URL**您從 Azure 入口網站複製的值。
    
    d. 在**SSO 登出 URL**方塊中，貼上**登出 URL**您從 Azure 入口網站複製的值。

10. 在**訊息簽章驗證方法**區段中，執行下列步驟：

    ![訊息簽章驗證方法](./media/active-directory-saas-central-desktop-tutorial/ic769565.png "訊息簽章驗證方法")。 選取 [憑證] 。
    
    b. 在**SSO 憑證**清單中，選取**RSH SHA256**。
    
    c. 在記事本中開啟下載的憑證。 然後複製憑證的內容，並將它貼入**SSO 憑證**欄位。
        
    d. 選取 [顯示 SAMLv2 登入頁面的連結] 。
    
    e. 選取**更新**。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本。 您將從這個應用程式之後**Active Directory** > **企業應用程式**區段中，選取**單一登入**索引標籤，然後再存取 內嵌透過文件**組態**底部區段。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請採取下列步驟：**

1. 在 **Azure 入口網站**的左側窗格中，選取 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請移至 [使用者和群組]。 然後選取 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，請選取 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，採取下列步驟：

    ![[使用者] 對話方塊](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。
 
### <a name="create-a-central-desktop-test-user"></a>建立 Central Desktop 測試使用者

讓 Azure AD 使用者能夠登入，您必須是佈建 Central Desktop 應用程式中。 本章節描述如何在 Central Desktop 中建立 Azure AD 使用者帳戶。

> [!NOTE]
> 要佈建 Azure AD 使用者帳戶，您可以使用任何其他 Central Desktop 使用者帳戶建立工具或 Api 所提供的 Central Desktop。

**將使用者帳戶佈建到 Central Desktop：**

1. 登入您的 Central Desktop 租用戶。

2. 移至**人員** > **內部成員**。

3. 選取**新增內部成員**。

    ![People](./media/active-directory-saas-central-desktop-tutorial/ic781051.png "People")
    
4. 在**新成員的電子郵件地址**方塊中，輸入您想要佈建，然後選取 Azure AD 帳戶**下一步**。

    ![電子郵件地址的新成員](./media/active-directory-saas-central-desktop-tutorial/ic781052.png "電子郵件地址的新成員")

5. 選取**新增內部成員**。

    ![新增內部成員](./media/active-directory-saas-central-desktop-tutorial/ic781053.png "新增內部成員")
   
   >[!NOTE]
   >您新增的使用者會收到電子郵件，其中包含用於啟用帳戶確認連結。
   
### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，以啟用使用者使用 Azure 單一登入授與他們存取 Central Desktop 許 Simon。

![指派使用者角色][200] 

**若要指派至 Central Desktop 的許 Simon，採取下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視。 移至目錄檢視，並移至**企業應用程式**。

2. 選取 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取**Central Desktop**。

    ![Central Desktop 中的連結應用程式清單](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 選取 [新增] 按鈕。 然後，在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊中，選取 [使用者] 清單中的 [Britta Simon]。

6. 在 [使用者和群組] 對話方塊中，按一下 [選取] 按鈕。

7. 在**將作業加入**對話方塊中，選取**指派** 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，使用存取面板測試 Azure AD 單一登入組態。

當您選取 Central Desktop 磚存取面板中時，您會自動取得登入您的 Central Desktop 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_203.png

