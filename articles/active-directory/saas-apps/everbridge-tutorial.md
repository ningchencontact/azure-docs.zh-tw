---
title: 教學課程：Azure Active Directory 與 EverBridge 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 EverBridge 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: 40645db589409ac80c69f1e7595e20869b08f5d0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194673"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>教學課程：Azure Active Directory 與 EverBridge 整合

在本教學課程中，您將了解如何整合 EverBridge 與 Azure Active Directory (Azure AD)。

EverBridge 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 EverBridge 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 EverBridge (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 EverBridge 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 EverBridge 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 EverBridge
2. 設定並測試 Azure AD 單一登入

## <a name="adding-everbridge-from-the-gallery"></a>從資源庫新增 EverBridge

若要設定 EverBridge 與 Azure AD 的整合作業，您必須從資源庫將 EverBridge 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 EverBridge，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，鍵入 **EverBridge**，從結果面板中選取 **EverBridge**，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 EverBridge](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 EverBridge 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 EverBridge 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 EverBridge 中的相關使用者之間建立連結關聯性。

若要設定及測試與 EverBridge 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 EverBridge 測試使用者](#creating-an-everbridge-test-user)** - 在 EverBridge 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 EverBridge 應用程式中設定單一登入。

**若要設定透過 EverBridge 使用 Azure AD 單一登入功能，請執行下列步驟：**

1. 在 Azure 入口網站的 [EverBridge] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

2. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![設定單一登入](common/tutorial_general_301.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![設定單一登入](common/editconfigure.png)

    >[!NOTE]
    >您需要在兩端透過管理員入口網站或成員入口網站來進行設定 (即 Azure 入口網站和 Everbridge 入口網站)。

4. 若要將 **EverBridge** 應用程式設為 **EverBridge 管理員入口網站**，請在 [基本 SAML 設定] 區段執行下列步驟：

    ![EverBridge 網域及 URL 單一登入資訊](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：`https://sso.everbridge.net/<API_Name>`

    b. 在 **[回覆 URL]** 文字方塊中，以下列模式輸入 URL：`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [EverBridge 支援小組](mailto:support@everbridge.com)以取得這些值。

5. 若要將 **EverBridge** 應用程式設為 **EverBridge 成員入口網站**，請在 [基本 SAML 設定] 區段執行下列步驟：

    * 若您想要在 **IDP** 起始模式中設定應用程式：

        ![EverBridge 網域及 URL 單一登入資訊](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

        * 在 [識別碼] 文字方塊中，使用下列模式輸入 URL： `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

        * 在 **[回覆 URL]** 文字方塊中，以下列模式輸入 URL：`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

    * 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

        ![EverBridge 網域及 URL 單一登入資訊](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

        * 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」和「登入 URL」更新這些值。 請連絡 [EverBridge 支援小組](mailto:support@everbridge.com)以取得這些值。

6. 在 [SAML 簽署憑證] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載] 以下載 [同盟中繼資料 XML]，然後將中繼資料檔案儲存在電腦上。

    ![憑證下載連結](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. 在 [設定 EverBridge] 區段上，依據需求複製適當的 URL。

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

    ![EverBridge 設定](common/configuresection.png)

8. 若要為 **EverBridge** 將 SSO 設為 **EverBridge 管理員入口網站**應用程式，請執行下列步驟： 
 
9. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 EverBridge。

9. 在頂端的功能表中，按一下 [設定] 索引標籤，然後選取 [安全性] 之下的 [單一登入]。
   
    ![設定單一登入](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. 在 [名稱] 文字方塊中，輸入識別碼提供者的名稱 (例如：您的公司名稱)。
   
    b. 在 [API 名稱] 文字方塊中，輸入 API 的名稱。
   
    c. 按一下 [選擇檔案] 按鈕，上傳您從 Azure 入口網站下載的中繼資料檔案。
   
    d. 在 [SAML 身分識別位置] 中，選取 [身分識別位於 Subject 陳述式的 NameIdentifier 元素中]。
   
    e. 在 [識別提供者登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的**登入 URL** 值。
   
    f. 在 [服務提供者起始的要求繫結] 中，選取 [HTTP 重新導向]。

    g. 按一下 [儲存] 

10. 若要在 **EverBridge** 上將單一登入設為 **EverBridge 成員入口網站**，您需要將所下載的**同盟中繼資料 XML** 傳送至 [EverBridge 支援小組](mailto:support@everbridge.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![建立 Azure AD 使用者][100]

2. 在畫面頂端選取 [新增使用者]。

    ![建立 Azure AD 測試使用者](common/create_aaduser_01.png) 

3. 在 [使用者] 屬性中，執行下列步驟。

    ![建立 Azure AD 測試使用者](common/create_aaduser_02.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性] [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。
  
### <a name="creating-an-everbridge-test-user"></a>建立 EverBridge 測試使用者

在本節中，您會在 Everbridge 中建立名為 Britta Simon 的使用者。 與 [Everbridge 支援小組](mailto:support@everbridge.com)合作，在 Everbridge 平台中新增使用者。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 EverBridge 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![指派使用者][201]

2. 在應用程式清單中，選取 [EverBridge] 。

    ![設定單一登入](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 EverBridge 圖格時，應該會自動登入您的 EverBridge 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
