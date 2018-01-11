---
title: "教學課程：Azure Active Directory 與 SilkRoad Life Suite 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 SilkRoad Life Suite 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 0d6af7af7d6b28ff3ea9d518a65b8267a83b71b4
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>教學課程：Azure Active Directory 與 SilkRoad Life Suite 整合

在本教學課程中，您可以了解如何與 Azure Active Directory (Azure AD) 整合 SilkRoad 生命套件。

SilkRoad Life Suite 與 Azure AD 整合提供下列優點：

- 您可以控制可以存取 SilkRoad 生命套件的 Azure AD 中。
- 您可以啟用自動取得登入到 SilkRoad 生命 Suite 的資訊 （單一登入） 讓使用者使用其 Azure AD 帳戶。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定與 SilkRoad Life Suite 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 SilkRoad Life Suite 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫加入 SilkRoad Life Suite
2. 設定並測試 Azure AD 單一登入

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>從資源庫加入 SilkRoad Life Suite
若要設定 SilkRoad Life Suite 與 Azure AD 整合，您需要從資源庫將 SilkRoad Life Suite 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 SilkRoad Life Suite，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在 搜尋 方塊中，輸入**SilkRoad 生命套件**，選取**SilkRoad 生命套件**然後按一下 從結果面板**新增**按鈕以加入應用程式。

    ![在 [結果] 清單中的 SilkRoad 生命套件](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，設定，並根據測試使用者稱為 「 許 Simon"SilkRoad 生命套件使用測試 Azure AD 單一登入。

單一登入工作，如 Azure AD 需要知道 SilkRoad 生命套件中的對等項目的使用者是使用者在 Azure AD 中。 換句話說，Azure AD 使用者和 SilkRoad Life Suite 中的相關使用者之間必須建立連結關聯性。

SilkRoad 生命套件中的值指派**使用者名**做為值的 Azure AD 中**Username**建立的連結關聯性。

若要設定及測試對 SilkRoad Life Suite 的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立測試使用者 SilkRoad 生命套件](#create-a-silkroad-life-suite-test-user)** -若要連結至使用者的 Azure AD 表示 SilkRoad 生命套件中有許 Simon 對應項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您可以啟用 Azure AD 單一登入 Azure 入口網站中，並 SilkRoad 生命 Suite 應用程式中設定單一登入。

**若要使用 SilkRoad Life Suite 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站上**SilkRoad 生命套件**應用程式整合頁面上，按一下 **單一登入**。

    ![設定單一登入連結][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

3. 在**SilkRoad 生命套件網域和 Url**區段中，執行下列步驟：

    ![SilkRoad 生命套件網域和 Url 的單一登入資訊](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰`https://<subdomain>.silkroad-eng.com/Authentication/`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL： 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL： 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來更新這些值。 請連絡[SilkRoad 生命套件的用戶端支援小組](https://www.silkroad.com/locations/)取得這些值。 

4. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

5. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_400.png)
    
6. 在**SilkRoad 生命套件組態**區段中，按一下**設定 SilkRoad 生命套件**開啟**設定登入**視窗。 從 [快速參考] 區段中複製 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]。

    ![SilkRoad 生命套件組態](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

7. 以系統管理員身分登入您的 SilkRoad 公司網站。 
 
    >[!NOTE] 
    > 若要取得用於設定與 Microsoft Azure AD 的同盟存取的 SilkRoad Life Suite 驗證應用程式，請連絡 SilkRoad 支援人員或您的 SilkRoad 服務代表。

8. 移至 [服務提供者]，然後按一下 [同盟詳細資料]。 
   
    ![Azure AD 單一登入][10]

9. 按一下 [下載同盟中繼資料] ，然後將資料檔儲存在您的電腦中。
   
    ![Azure AD 單一登入][11] 

10. 在您的 **SilkRoad** 應用程式中，按一下 [驗證來源]。
   
    ![Azure AD 單一登入][12] 

11. 按一下 [加入驗證來源] 。 
   
    ![Azure AD 單一登入][13] 

12. 在 [加入驗證來源]  區段中，執行下列步驟： 
   
    ![Azure AD 單一登入][14]
  
    a. 在下**選項 2-中繼資料檔案**，按一下 **瀏覽**上傳從 Azure 入口網站下載的中繼資料檔案。
  
    b. 按一下 [使用檔案資料建立身分識別提供者]。

13. 在 [驗證來源] 區段中，按一下 [編輯]。 
    
     ![Azure AD 單一登入][15] 

14. 在 [編輯驗證來源]  對話方塊中，執行下列步驟： 
    
     ![Azure AD 單一登入][16] 

    a. 對 [已啟用] 選取 [是]。

    b. 在**EntityId**文字方塊中，貼上的值**SAML 實體識別碼**您從 Azure 入口網站複製的。
   
    c. 在**IdP 描述**文字方塊中，輸入您的組態的描述 (例如： *Azure AD 的 SSO*)。

    d. 在**中繼資料檔案**文字方塊中上, 傳**中繼資料**您從 Azure 入口網站下載的檔案。
  
    e. 在**IdP 名稱**文字方塊中，輸入屬於您的組態的名稱 (例如： *Azure SP*)。
  
    f. 在**登出服務 URL**文字方塊中，貼上的值**登出 URL**您從 Azure 入口網站複製的。

    g. 在**登入服務 URL**文字方塊中，貼上的值**SAML 單一登入服務 URL**您從 Azure 入口網站複製的。

    h. 按一下 [檔案] 。

15. 停用所有其他驗證來源。 
    
     ![Azure AD 單一登入][17]

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，按一下 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="create-a-silkroad-life-suite-test-user"></a>建立 SilkRoad Life Suite 測試使用者

在本節中，您可以建立稱為許 Simon SilkRoad 生命套件中的使用者。 使用[SilkRoad 生命套件的用戶端支援小組](https://www.silkroad.com/locations/)SilkRoad 生命套件平台中新增的使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。 

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，以啟用要使用 Azure 單一登入授與存取權 SilkRoad 生命套件許 Simon。

![指派使用者角色][200] 

**若要將 Britta Simon 指派到 SilkRoad Life Suite，請執行以下步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [SilkRoad Life Suite] 。

    ![SilkRoad 生命套件中的連結應用程式清單](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

3. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在 [存取面板] 中按一下 [SilkRoad Life Suite] 磚時，您應該會自動登入您的 SilkRoad Life Suite 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
