---
title: "教學課程：Azure Active Directory 與 TOPdesk - Secure 整合 | Microsoft Docs"
description: "了解如何設定單一登入 Azure Active Directory 與 TOPdesk-Secure 之間。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: ca3362bc3f966adaf9940f6eb4bec5235c6ea7d8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>教學課程：Azure Active Directory 與 TOPdesk - Secure 整合

在本教學課程，了解如何整合 TOPdesk-Secure 與 Azure Active Directory (Azure AD)。

TOPdesk-Secure 的整合與 Azure AD 讓您獲得下列優勢：

- 您可以控制可以存取 TOPdesk-Secure 的 Azure AD 中。
- 您可以讓您自動取得登入 TOPdesk-Secure （單一登入） 具有其 Azure AD 帳戶的使用者。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 TOPdesk-與 Azure AD 整合安全，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 TOPdesk - Secure 單一登入功能的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 加入 TOPdesk-Secure 從資源庫
2. 設定並測試 Azure AD 單一登入

## <a name="adding-topdesk---secure-from-the-gallery"></a>加入 TOPdesk-Secure 從資源庫
若要設定 TOPdesk-整合保護至 Azure AD，您需要新增 TOPdesk-從資源庫到清單中的受管理的 SaaS 應用程式的安全。

**若要加入 TOPdesk-Secure 從資源庫，執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在 搜尋 方塊中，輸入**TOPdesk-Secure**，選取**TOPdesk-Secure**然後按一下 從結果面板**新增**按鈕以加入應用程式。

    ![TOPdesk-Secure [結果] 清單中](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您設定和測試 Azure AD 單一登入與 TOPdesk-安全根據稱為 「 許 Simon"的測試使用者。

單一登入工作，如 Azure AD 需要知道 TOPdesk-Secure 中對等項目的使用者是使用者在 Azure AD 中。 換句話說，必須建立 Azure AD 使用者與 TOPdesk-Secure 中相關的使用者之間的連結關聯性。

在 TOPdesk-Secure、 指派的值**使用者名稱**做為值的 Azure AD 中**Username**建立的連結關聯性。

若要設定和測試 Azure AD 單一登入與 TOPdesk-安全，您必須完成下列的建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 TOPdesk-安全的測試使用者](#create-a-topdesk---secure-test-user)** -若要將已連結至使用者的 Azure AD 表示的許 Simon TOPdesk-Secure 中對應項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您可以啟用 Azure AD 單一登入 Azure 入口網站中，並設定單一登入 TOPdesk-安全的應用程式中。

**若要設定 Azure AD 單一登入與 TOPdesk-Secure，請執行下列步驟：**

1. 在 Azure 入口網站上**TOPdesk-Secure**應用程式整合頁面上，按一下 **單一登入**。

    ![設定單一登入連結][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_samlbase.png)

3. 在**TOPdesk-Secure 的網域和 Url**區段中，執行下列步驟：

    ![TOPdesk-安全的網域和 Url 的單一登入資訊](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰`https://<companyname>.topdesk.net`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：`https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 稍後在教學課程說明回覆 URL。 請連絡[TOPdesk-Secure 的用戶端支援小組](http://www.topdesk.com/us/support)取得這些值。 

4. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_certificate.png) 

5. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_400.png)

6. 在**TOPdesk-Secure 設定**區段中，按一下**設定 TOPdesk-Secure**開啟**設定登入**視窗。 從 [快速參考] 區段中複製 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]。

    ![TOPdesk-的安全性設定](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_configure.png)
    
7. 以系統管理員身分登入您的 **TOPdesk - Secure** 公司網站。

8. 在 [TOPdesk] 功能表中按一下 [設定]。

    ![設定](./media/active-directory-saas-topdesk-secure-tutorial/ic790598.png "設定")

9. 按一下 [登入設定] 。

    ![登入設定](./media/active-directory-saas-topdesk-secure-tutorial/ic790599.png "登入設定")

10. 展開 [登入設定] 功能表，然後按一下 [一般]。

    ![一般](./media/active-directory-saas-topdesk-secure-tutorial/ic790600.png "一般")

11. 在 [SAML 登入] 組態區段的 **Secure** 區段中，執行下列步驟：

    ![技術設定](./media/active-directory-saas-topdesk-secure-tutorial/ic790855.png "技術設定")
   
    a. 按 [下載]  來下載公用中繼資料檔案，然後再將它儲存在本機電腦上。
   
    b. 開啟此中繼資料檔案，然後找到 **AssertionConsumerService** 節點。
    
    ![判斷提示取用者服務](./media/active-directory-saas-topdesk-secure-tutorial/ic790856.png "判斷提示取用者服務")
   
    c. 複製**AssertionConsumerService**值，請在 [回覆 URL] 文字方塊中貼上此值**TOPdesk-Secure 的網域和 Url** > 一節。

12. 若要建立憑證檔案，請執行下列步驟：
    
    ![憑證](./media/active-directory-saas-topdesk-secure-tutorial/ic790606.png "憑證")
    
    a. 從 Azure 入口網站開啟下載的中繼資料檔案。

    b. 展開 **RoleDescriptor** 節點，其具有 **fed:ApplicationServiceType** 的 **xsi:type**。

    c. 複製 **X509Certificate** 節點的值。

    d. 儲存複製的 **X509Certificate** 值到本機電腦的檔案中。

13. 在 **Public** 區段中，按一下 [加入]。
    
    ![新增](./media/active-directory-saas-topdesk-secure-tutorial/ic790607.png "新增")

14. 在 [SAML 組態輔助程式]  對話頁面上，執行下列步驟：
    
    ![SAML 組態輔助程式](./media/active-directory-saas-topdesk-secure-tutorial/ic790608.png "SAML 組態輔助程式")
    
    a. 若要上傳您從 Azure 入口網站下載的中繼資料檔案，請在 [同盟中繼資料] 下按一下 [瀏覽]。

    b. 若要上傳您的憑證檔案，請在 [憑證 (RSA)] 下按一下 [瀏覽]。

    c. 若要上傳您從 TOPdesk 支援小組取得的標誌檔案，請在 [標誌圖示] 下按一下 [瀏覽]。

    d. 在 [使用者名稱屬性] 文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    e. 在 [顯示名稱]  文字方塊中，輸入您的組態名稱。

    f. 按一下 [檔案] 。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，按一下 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="create-a-topdesk---secure-test-user"></a>建立 TOPdesk-安全的測試使用者

若要讓 Azure AD 使用者可以登入 TOPdesk - Secure，則必須將他們佈建到 TOPdesk - Secure。  
TOPdesk - Secure 需以手動的方式佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 以系統管理員身分登入您的 **TOPdesk - Secure** 公司網站。
2. 在上方功能表中按一下 [TOPdesk] \> [新增] \> [支援檔案] \> [操作員]。
   
    ![操作員](./media/active-directory-saas-topdesk-secure-tutorial/ic790610.png "操作員")

3. 在 [新增操作員]  對話方塊上，執行下列步驟：
   
    ![新增操作員](./media/active-directory-saas-topdesk-secure-tutorial/ic790611.png "新增操作員")
   
    a. 按一下**一般** 索引標籤。
   
    b. 在**姓氏**文字方塊中，使用者姓氏類型喜歡**Simon**。
   
    c. 在 [位置] 區段中選取該帳戶的 [網站]。
   
    d. 在 [TOPdesk 登入] 區段的 [登入名稱] 文字方塊中，輸入使用者的登入名稱。
   
    e. 按一下 [檔案] 。

> [!NOTE]
> 您可以使用任何其他的 TOPdesk - Secure 使用者帳戶建立工具或 TOPdesk - Secure 提供的 API 來佈建 AAD 使用者帳戶。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，以啟用要使用 Azure 單一登入授與存取權給 TOPdesk-Secure 許 Simon。

![指派使用者角色][200] 

**將許 Simon 指派給 TOPdesk-Secure，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取**TOPdesk-Secure**。

    ![TOPdesk-應用程式清單中的安全連結](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_app.png)  

3. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您按一下 TOPdesk-安全的磚，在存取面板中，您應該取得自動登入 TOPdesk-安全的應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_203.png

