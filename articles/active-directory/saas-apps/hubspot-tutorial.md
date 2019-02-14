---
title: 教學課程：Azure Active Directory 與 HubSpot 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 HubSpot 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fd70b80dd512b530d367d57cd9fc04ec28f27b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211694"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>教學課程：Azure Active Directory 與 HubSpot 整合

在本教學課程中，您將了解如何整合 HubSpot 與 Azure Active Directory (Azure AD)。

HubSpot 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 HubSpot 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 HubSpot (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定與 HubSpot 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 HubSpot 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 HubSpot
2. 設定並測試 Azure AD 單一登入

## <a name="adding-hubspot-from-the-gallery"></a>從資源庫新增 HubSpot

若要設定 HubSpot 與 Azure AD 整合，您需要從資源庫將 HubSpot 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 HubSpot，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![[應用程式]][2]

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![[應用程式]][3]

4. 在搜尋方塊中，輸入 **HubSpot**。 從結果面板選取 [HubSpot]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/hubspot-tutorial/tutorial_hubspot_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 HubSpot 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 HubSpot 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 HubSpot 中相關使用者之間的連結關聯性。

若要設定及測試與 HubSpot 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **建立 HubSpot 測試使用者** - 在 HubSpot 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 HubSpot 應用程式中設定單一登入。

**若要使用 HubSpot 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [HubSpot] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![設定單一登入](./media/hubspot-tutorial/tutorial_general_301.png)

3. 如果您要從任何其他模式變更為 [SAML] 模式，請按一下畫面頂端的 [變更單一登入模式]。

    ![設定單一登入](./media/hubspot-tutorial/tutorial_general_300.png)

4. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![設定單一登入](./media/hubspot-tutorial/tutorial_general_302.png)

5. 若您想要以 **IDP** 起始模式設定應用程式，請在 [基本 SAML 組態] 區段執行下列步驟：

    ![HubSpot 網域與 URL 單一登入資訊](./media/hubspot-tutorial/tutorial_hubspot_url.png)

    a. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：`https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼及回覆 URL 來更新這些值 (本教學課程稍後會說明)。 請連絡 [HubSpot 用戶端支援小組](https://help.hubspot.com/)以取得這些值。

    c. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![HubSpot 網域與 URL 單一登入資訊](./media/hubspot-tutorial/tutorial_hubspot_url1.png)

    在 [登入 URL] 文字方塊中，輸入 URL：`https://app.hubspot.com/login`

6. 在 [SAML 簽署憑證] 頁面上的 [SAML 簽署憑證] 區段中，按一下 [下載] 以下載**憑證 (Base64)**，然後將憑證檔案儲存在電腦上。

    ![設定單一登入](./media/hubspot-tutorial/tutorial_hubspot_certificate.png)

7. 在 [設定 HubSpot] 區段中，按一下複製按鈕以複製 [登入 URL] 和 [Azure AD 識別碼] 值。

    ![設定單一登入](./media/hubspot-tutorial/tutorial_hubspot_configure.png)

8. 在瀏覽器中開啟新索引標籤，登入您的 HubSpot 系統管理員帳戶。

9. 按一下頁面右上角的**設定圖示**。

    ![設定單一登入](./media/hubspot-tutorial/config1.png)

10. 按一下 [帳戶預設值]。

    ![設定單一登入](./media/hubspot-tutorial/config2.png)

11. 向下捲動至 [安全性] 區段，然後按一下 [設定]。

    ![設定單一登入](./media/hubspot-tutorial/config3.png)

12. 在 [設定單一登入] 區段中，執行下列步驟：

    ![設定單一登入](./media/hubspot-tutorial/config4.png)

    a. 按一下 [複製] 按鈕以複製 [對象 URl (服務提供者實體識別碼)] 值，並將其貼至 Azure 入口網站中 [基本 SAML 設定] 的 [識別碼] 文字方塊中。

    b. 按一下 [複製] 按鈕以複製**登入 URl、ACS、收件者或重新導向**值，並將其貼至 Azure 入口網站中 [基本 SAML 設定] 的 [回覆 URL] 文字方塊中。

    c. 在 [識別提供者識別碼或簽發者 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼] 值。

    d. 在 [識別提供者單一登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    e. 在「記事本」中開啟所下載的 **憑證 (Base64)** 檔案。 將其內容複製到剪貼簿，然後貼到 [X.509 憑證] **** 方塊中。

    f. 按一下 [確認]。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![建立 Azure AD 使用者][100]

2. 在畫面頂端選取 [新增使用者]。

    ![建立 Azure AD 測試使用者](./media/hubspot-tutorial/create_aaduser_01.png) 

3. 在 [使用者] 屬性中，執行下列步驟。

    ![建立 Azure AD 測試使用者](./media/hubspot-tutorial/create_aaduser_02.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性] [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="creating-a-hubspot-test-user"></a>建立 HubSpot 測試使用者

若要讓 Azure AD 使用者可以登入 HubSpot，則必須將他們佈建至 HubSpot。
HubSpot 需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 **HubSpot** 公司網站。

2. 按一下頁面右上角的**設定圖示**。

    ![設定單一登入](./media/hubspot-tutorial/config1.png)

3. 按一下 [使用者和小組]。

    ![設定單一登入](./media/hubspot-tutorial/user1.png)

4. 按一下 [建立使用者]。

    ![設定單一登入](./media/hubspot-tutorial/user2.png)

5. 輸入使用者的電子郵件地址，例如在 [電子郵件地址] 文字方塊中輸入 **brittasimon@contoso.com**，然後按 [下一步]。

    ![設定單一登入](./media/hubspot-tutorial/user3.png)

6. 在 [建立使用者] 區段中，請瀏覽每個個別的索引標籤，並且為使用者選取適當的選項和權限，然後按 [下一步]。

    ![設定單一登入](./media/hubspot-tutorial/user4.png)

7. 按一下 [傳送] 以傳送邀請給使用者。

    ![設定單一登入](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > 使用者會在接受邀請之後啟動。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 HubSpot 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![指派使用者][201]

2. 在應用程式清單中，選取 [HubSpot]。

    ![設定單一登入](./media/hubspot-tutorial/tutorial_hubspot_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

在存取面板中按一下 [HubSpot] 圖格時，您應該會取得 HubSpot 應用程式的自動登入頁面。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hubspot-tutorial/tutorial_general_01.png
[2]: ./media/hubspot-tutorial/tutorial_general_02.png
[3]: ./media/hubspot-tutorial/tutorial_general_03.png
[4]: ./media/hubspot-tutorial/tutorial_general_04.png
[100]: ./media/hubspot-tutorial/tutorial_general_100.png
[200]: ./media/hubspot-tutorial/tutorial_general_200.png
[201]: ./media/hubspot-tutorial/tutorial_general_201.png
[202]: ./media/hubspot-tutorial/tutorial_general_202.png
[203]: ./media/hubspot-tutorial/tutorial_general_203.png
