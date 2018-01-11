---
title: "教學課程： Azure Active Directory 整合與 FactSet |Microsoft 文件"
description: "了解如何設定單一登入 Azure Active Directory 與 FactSet 之間。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: f41072d1-b2a4-4c4d-9deb-101b52e29096
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: 5d39de5aebf5578ff9970146d36ac3cc724b3a87
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-factset"></a>教學課程： Azure Active Directory 整合與 FactSet

在本教學課程中，您可以了解如何與 Azure Active Directory (Azure AD) 整合 FactSet。

使用 Azure AD 整合 FactSet 可以提供下列優點：

- 您可以控制可以存取 FactSet Azure AD 中。
- 您可以啟用自動取得登入 (Single Sign-on) FactSet 其 Azure AD 帳戶的使用者。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 整合與 FactSet，您需要下列項目：

- Azure AD 訂用帳戶
- FactSet 單一登入啟用的訂閱

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫加入 FactSet
2. 設定並測試 Azure AD 單一登入

## <a name="adding-factset-from-the-gallery"></a>從資源庫加入 FactSet
若要設定 FactSet 整合 Azure AD，您需要加入 FactSet 從資源庫，您的受管理的 SaaS 應用程式清單。

**若要從資源庫加入 FactSet，執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在 搜尋 方塊中，輸入**FactSet**，選取**FactSet**然後按一下 從結果面板**新增**按鈕以加入應用程式。

    ![在 [結果] 清單中的 FactSet](./media/active-directory-saas-factset-tutorial/tutorial_factset_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，設定，並根據測試使用者稱為 「 許 Simon"FactSet 使用測試 Azure AD 單一登入。

單一登入工作，Azure AD 需要知道對應項目中的使用者 FactSet 是使用者在 Azure AD 中。 換句話說，必須建立 Azure AD 使用者和相關的使用者在 FactSet 之間的連結關聯性。

FactSet，在指定的值**使用者名**做為值的 Azure AD 中**Username**建立的連結關聯性。

若要設定和測試 Azure AD 單一登入與 FactSet 時，您必須完成下列的建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立測試使用者 FactSet](#create-a-factset-test-user)**  -若要在連結至使用者的 Azure AD 表示 FactSet 許 Simon 對應項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您可以啟用 Azure AD 單一登入 Azure 入口網站中，並 FactSet 應用程式中設定單一登入。

**若要設定 Azure AD 單一登入與 FactSet，執行下列步驟：**

1. 在 Azure 入口網站上**FactSet**應用程式整合頁面上，按一下 **單一登入**。

    ![設定單一登入連結][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/active-directory-saas-factset-tutorial/tutorial_factset_samlbase.png)

3. 在**FactSet 網域和 Url**區段中，執行下列步驟：

    ![FactSet 網域和 Url 的單一登入資訊](./media/active-directory-saas-factset-tutorial/tutorial_factset_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰`https://login.factset.com/services/auth-test?idpid=<GUID>`

    b. 在 [識別碼] 文字方塊中，輸入 URL：`https://login.factset.com`

    > [!NOTE] 
    > 此登入 URL 值不是真正的。 請使用實際的「登入 URL」來更新此值。 請連絡[FactSet 用戶端支援小組](mailto:support@factset.com)来取得其值。 
 
4. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-factset-tutorial/tutorial_factset_certificate.png) 

5. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-factset-tutorial/tutorial_general_400.png)

6. 若要設定單一登入上**FactSet**端，您需要傳送下載**中繼資料 XML**至[FactSet 支援小組](mailto:support@factset.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-factset-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-factset-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，按一下 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-factset-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](./media/active-directory-saas-factset-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="create-a-factset-test-user"></a>建立 FactSet 測試使用者

本節中，您可以建立 FactSet 中呼叫許 Simon 的使用者。 使用[FactSet 支援小組](mailto:support@factset.com)FactSet 平台中新增的使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。 

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，以啟用要使用 Azure 單一登入授與存取權 FactSet 許 Simon。

![指派使用者角色][200] 

**若要指派 FactSet 許 Simon，執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取**FactSet**。

    ![應用程式清單中的 [FactSet] 連結](./media/active-directory-saas-factset-tutorial/tutorial_factset_app.png)  

3. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您按一下 FactSet 磚，在存取面板時，您應該取得自動登入 FactSet 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-factset-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-factset-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-factset-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-factset-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-factset-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-factset-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-factset-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-factset-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-factset-tutorial/tutorial_general_203.png

