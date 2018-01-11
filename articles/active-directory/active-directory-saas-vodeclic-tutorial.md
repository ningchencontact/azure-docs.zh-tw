---
title: "教學課程： Azure Active Directory 整合與 Vodeclic |Microsoft 文件"
description: "了解如何設定單一登入 Azure Active Directory 與 Vodeclic 之間。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: 940c7bb5040fb91a03b01dc43ee07d52e3d4e63b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>教學課程： Azure Active Directory 整合與 Vodeclic

在本教學課程中，您可以了解如何與 Azure Active Directory (Azure AD) 整合 Vodeclic。

使用 Azure AD 整合 Vodeclic 可以提供下列優點：

- 您可以控制可以存取 Vodeclic Azure AD 中。
- 您可以啟用您的使用者會自動取得登入 Vodeclic （單一登入，或 SSO） 與 Azure AD 帳戶。
- 您可以在 Azure 入口網站集中管理您的帳戶。

若您想了解 SaaS 應用程式與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 整合與 Vodeclic，您需要下列項目：

- Azure AD 訂用帳戶
- Vodeclic SSO 啟用訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，請遵循下列建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，[取得一個月免費試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫加入 Vodeclic
2. 設定並測試 Azure AD 單一登入

## <a name="add-vodeclic-from-the-gallery"></a>從資源庫加入 Vodeclic
若要設定 Vodeclic 整合 Azure AD，您需要加入 Vodeclic 從資源庫，您的受管理的 SaaS 應用程式清單。

**若要從資源庫加入 Vodeclic，採取下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 移至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要加入新的應用程式，選取**新的應用程式**在對話方塊頂端的按鈕。

    ![新增應用程式按鈕][3]

4. 在 [搜尋] 方塊中，輸入**Vodeclic**。 選取**Vodeclic**結果面板，然後選取從**新增**按鈕以加入應用程式。

    ![在 [結果] 清單中的 Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

本節中，設定和測試 Azure AD 單一登入 Vodeclic 根據測試使用者稱為 「 許 Simon。 」

單一登入工作，如 Azure AD 需要知道誰對應項目中的使用者 Vodeclic 是使用者在 Azure AD 中。 換句話說，您需要在 Vodeclic 中建立 Azure AD 使用者和相關的使用者之間的連結。

在 Vodeclic，價值**Username**相同的值做**使用者名稱**在 Azure AD 中。 現在您已經建立兩名使用者之間的連結。

若要設定和測試 Azure AD 單一登入與 Vodeclic，請完成下列的建置組塊：

1. [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓使用者使用這項功能。
2. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 Britta Simon 測試 Azure AD 單一登入。
3. [建立測試使用者 Vodeclic](#create-a-vodeclic-test-user) Vodeclic 連結至使用者的 Azure AD 表示法中有許 Simon 對應項目。
4. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. [測試單一登入](#test-single-sign-on)，驗證設定是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您可以啟用 Azure AD 單一登入 Azure 入口網站中，並 Vodeclic 應用程式中設定單一登入。

**若要設定 Azure AD 單一登入與 Vodeclic，採取下列步驟：**

1. 在 Azure 入口網站上**Vodeclic**應用程式整合頁面上，選取**單一登入**。

    ![設定單一登入連結][4]

2. 在**單一登入**對話方塊的 **單一登入模式**，選取**SAML 型登入**來啟用單一登入。
 
    ![單一登入對話方塊](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. 如果您想要將應用程式中的設定**IDP**起始模式中，在**Vodeclic 網域和 Url**區段中，執行下列步驟：

    ![Vodeclic 網域和 Url 的單一登入資訊](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. 在 [識別碼] 方塊中，以下列模式輸入 URL：`https://<companyname>.lms.vodeclic.net/auth/saml`

    b. 在 [回覆 URL] 方塊中，使用下列模式輸入 URL：`https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. 如果您想要將應用程式中的設定**SP**初始的模式中，選取**顯示進階的 URL 設定**核取方塊，然後執行下列步驟：

    ![Vodeclic 網域和 Url 的單一登入資訊](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url1.png)

    在 [登入 URL] 方塊中，以下列模式輸入 URL：`https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > 這些都不是真正的值。 更新這些值與實際的識別項，回覆 URL 和登入 URL。 請連絡[Vodeclic 用戶端支援小組](mailto:hotline@vodeclic.com)取得這些值。

5. 在 [SAML 簽章憑證] 區段中，選取 [中繼資料 XML]。 然後，將中繼資料檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. 選取 [ **儲存**]。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-vodeclic-tutorial/tutorial_general_400.png)
    
7. 若要設定單一登入上**Vodeclic**側邊，傳送下載**中繼資料 XML**至[Vodeclic 支援小組](mailto:hotline@vodeclic.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本。 從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，選取 [單一登入] 索引標籤，即可透過底部的 [設定] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請採取下列步驟：**

1. 在 **Azure 入口網站**的左側窗格中，選取 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請移至 [使用者和群組]。 然後選取 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，請選取 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，採取下列步驟：

    ![[使用者] 對話方塊](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。
 
### <a name="create-a-vodeclic-test-user"></a>建立 Vodeclic 測試使用者

本節中，您可以建立 Vodeclic 中呼叫許 Simon 的使用者。 使用[Vodeclic 支援小組](mailto:hotline@vodeclic.com)Vodeclic 平台中新增的使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

> [!NOTE]
> 根據應用程式需求，您可能需要取得您的電腦在允許清單中。 會發生，您需要共用公用 IP 位址與[Vodeclic 支援小組](mailto:hotline@vodeclic.com)。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，以啟用要使用 Azure 單一登入授與存取權 Vodeclic 許 Simon。

![指派使用者角色][200] 

**若要指派 Vodeclic 許 Simon，採取下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，然後移至目錄檢視。 接下來，移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取**Vodeclic**。

    ![應用程式清單中的 [Vodeclic] 連結](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 選取 [新增] 按鈕。 然後，在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊中，選取 [使用者] 清單中的 [Britta Simon]。

6. 在 [使用者和群組] 對話方塊中，選取 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您選取 Vodeclic 磚存取面板中時，您取得自動登入 Vodeclic 應用程式。

如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_203.png

