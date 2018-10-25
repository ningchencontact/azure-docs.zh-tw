---
title: 教學課程：Azure Active Directory 與 Zscaler Three 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Zscaler Three 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.openlocfilehash: 0ef8fc2ea8b006d49dd54d638183a58bf78a5797
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311772"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>教學課程：Azure Active Directory 與 Zscaler Three 整合

在本教學課程中，您將了解如何整合 Zscaler Three 與 Azure Active Directory (Azure AD)。

將 Zscaler Three 與 Azure AD 整合可提供下列優點：

- 您可以在 Azure AD 中控制可存取 Zscaler Three 的人員
- 您可以讓使用者利用自己的 Azure AD 帳戶，來自動登入 Zscaler Three (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Zscaler Three 的整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Zscaler Three 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在這裡取得一個月試用：[試用供應項目](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。
本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Zscaler Three
2. 設定並測試 Azure AD 單一登入

## <a name="adding-zscaler-three-from-the-gallery"></a>從資源庫新增 Zscaler Three

若要設定將 Zscaler Three 整合到 Azure AD 中，您需要從資源庫將 Zscaler Three 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Zscaler Three，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![[應用程式]][2]

3. 按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![[應用程式]][3]

4. 在結果窗格中，選取 [Zscaler Three]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 Zscaler Three 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Zscaler Three 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 Zscaler Three 中的相關使用者之間建立連結關聯性。

若要使用 Zscaler Three 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Proxy 設定](#configuring-proxy-settings)** - 在 Internet Explorer 中進行 Proxy 設定
3. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 Zscaler Three 測試使用者](#creating-a-zscaler-three-test-user)** - 使 Zscaler Three 中 Britta Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
6. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在 Zscaler Three 應用程式中設定單一登入。

**若要設定與 Zscaler Three 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Zscaler Three] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![設定單一登入](./media/zscaler-three-tutorial/tutorial_general_301.png)

3. 如果您要從任何其他模式變更為 [SAML] 模式，請按一下畫面頂端的 [變更單一登入模式]。

    ![設定單一登入](./media/zscaler-three-tutorial/tutorial_general_300.png)

4. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![設定單一登入](./media/zscaler-three-tutorial/tutorial_general_302.png)

5. 在 [基本 SAML 設定] 區段上，執行下列步驟：

    ![設定單一登入](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    在 [回覆 URL] 文字方塊中，輸入 URL：`https://login.zscalerthree.net/sfc_sso`

    > [!NOTE]
    > 您必須使用實際的「登入 URL」來更新此值。 請連絡 [Zscaler Three 用戶端支援小組](https://www.zscaler.com/company/contact)以取得這些值。

6. 在 [SAML 簽署憑證] 區段上，按一下 [下載] 以下載 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png)

8. 在 [設定 Zscaler Three] 區段上，複製 [登入 URL]。

    ![設定單一登入](./media/zscaler-three-tutorial/tutorial_zscalerthree_configure.png)

9. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入 Zscaler Three 公司網站。

10. 在頂端的功能表中，按一下 [系統管理] 。

    ![管理](./media/zscaler-three-tutorial/ic800206.png "管理")

9. 在 [管理系統管理員和角色] 下按一下 [管理使用者和驗證]。

    ![管理使用者和驗證](./media/zscaler-three-tutorial/ic800207.png "管理使用者和驗證")

10. 在 [選擇您的組織的驗證選項]  區段中，執行下列步驟：

    ![驗證](./media/zscaler-three-tutorial/ic800208.png "驗證")

    a. 選取 [使用 SAML 單一登入進行驗證] 。

    b. 按一下 [設定 SAML 單一登入參數] 。

11. 在 [設定 SAML 單一登入參數] 對話方塊頁面上，執行下列步驟，然後按一下 [完成]

    ![單一登入](./media/zscaler-three-tutorial/ic800209.png "單一登入")

    a. 將 [登入 URL] 值 (您從 Azure 入口網站複製的值) 貼到 [傳送用於驗證之使用者的 SAML 入口網站 URL] 文字方塊。

    b. 在 [包含登入名稱的屬性] 文字方塊中，輸入 **NameID**。

    c. 若要上傳您下載的憑證，請按一下 Zscaler pem 。

    d. 選取 [啟用 SAML 自動佈建] 。

12. 在 [設定使用者驗證]  對話方塊頁面上執行下列步驟：

    ![管理](./media/zscaler-three-tutorial/ic800210.png "管理")

    a. 按一下 [檔案] 。

    b. 按一下 [立即啟用] 。

## <a name="configuring-proxy-settings"></a>進行 Proxy 設定

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>在 Internet Explorer 中進行 Proxy 設定

1. 啟動 **Internet Explorer**。

2. 從 [工具] 功能表選取 [網際網路選項] 可開啟 [網際網路選項] 對話方塊。

     ![網際網路選項](./media/zscaler-three-tutorial/ic769492.png "網際網路選項")

3. 按一下 [連線]  索引標籤。
  
     ![連線](./media/zscaler-three-tutorial/ic769493.png "連線")

4. 按一下 [區域網路設定] 可開啟 [區域網路設定] 對話方塊。

5. 在 [Proxy 伺服器] 區段中，執行下列步驟：

    ![Proxy 伺服器](./media/zscaler-three-tutorial/ic769494.png "Proxy 伺服器")

    a. 選取 [在您的區域網路使用 Proxy 伺服器]。

    b. 在 [位址] 文字方塊中輸入 **gateway.zscalerthree.net**。

    c. 在 [連接埠] 文字方塊中輸入 **80**。

    d. 選取 [近端網址不使用 Proxy 伺服器] 。

    e. 按一下 [確定] 關閉 [區域網路 (LAN) 設定] 對話方塊。

6. 按一下 [確定] 關閉 [網際網路選項] 對話方塊。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者]，然後選取 [所有使用者]。

    ![建立 Azure AD 使用者][100]

2. 選取畫面頂端的 [新增使用者]。

    ![建立 Azure AD 測試使用者](./media/zscaler-three-tutorial/create_aaduser_01.png) 

3. 在 [使用者] 屬性中，執行下列步驟。

    ![建立 Azure AD 測試使用者](./media/zscaler-three-tutorial/create_aaduser_02.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性]、[顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="creating-a-zscaler-three-test-user"></a>建立 Zscaler Three 測試使用者

若要允許 Azure AD 使用者登入 Zscaler Three，他們必須佈建到 Zscaler Three。 Zscaler Three 需以手動的方式佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：

1. 登入 **Zscaler Three** 租用戶。

2. 按一下 [系統管理] 。

    ![管理](./media/zscaler-three-tutorial/ic781035.png "管理")

3. 按一下 [使用者管理] 。

     ![新增](./media/zscaler-three-tutorial/ic781036.png "新增")

4. 在 [使用者] 索引標籤中，按一下 [新增]。

    ![新增](./media/zscaler-three-tutorial/ic781037.png "新增")

5. 在 [新增使用者] 區段中，執行下列步驟：

    ![新增使用者](./media/zscaler-three-tutorial/ic781038.png "新增使用者")

    a. 輸入 [使用者識別碼]、[使用者顯示名稱]、[密碼]、[確認密碼]，然後選取您要佈建之有效 Azure AD 帳戶的 [群組] 和 [部門]。

    b. 按一下 [檔案] 。

> [!NOTE]
> 您可以使用任何其他的 Zscaler Three 使用者帳戶建立工具或 Zscaler Three 提供的 API 來佈建 Azure AD 使用者帳戶。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Zscaler Three 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式]。

    ![指派使用者][201]

2. 在應用程式清單中，選取 [Zscaler Three]。

    ![設定單一登入](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202]

4. 按一下 [新增使用者] 按鈕，然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Zscaler Three 圖格時，應該會自動登入您的 Zscaler Three 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-three-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-three-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-three-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-three-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-three-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-three-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-three-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-three-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-three-tutorial/tutorial_general_203.png