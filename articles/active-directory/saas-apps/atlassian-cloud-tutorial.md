---
title: 教學課程：Azure Active Directory 與 Atlassian Cloud 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Atlassian Cloud 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49d44fa0926afac917ae0ba355d37f13a354f432
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57887929"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>教學課程：Azure Active Directory 與 Atlassian Cloud 整合

在本教學課程中，您會了解如何整合 Atlassian Cloud 與 Azure Active Directory (Azure AD)。
Atlassian Cloud 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Atlassian Cloud 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Atlassian Cloud (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Atlassian Cloud 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Atlassian Cloud 單一登入的訂用帳戶
* 若要針對 Atlassian Cloud 產品啟用安全性聲明標記語言 (SAML) 單一登入，您必須設定 Atlassian Access。 深入了解 [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager) \(英文\)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Atlassian Cloud 支援由 **SP 和 IDP** 起始的 SSO

## <a name="adding-atlassian-cloud-from-the-gallery"></a>從資源庫新增 Atlassian Cloud

若要設定將 Atlassian Cloud 整合到 Azure AD 中，您需要從資源庫將 Atlassian Cloud 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Atlassian Cloud，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Atlassian Cloud**，從結果面板中選取 [Atlassian Cloud]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 Atlassian Cloud](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Atlassian Cloud 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Atlassian Cloud 中相關使用者之間的連結關聯性。

若要設定及測試與 Atlassian Cloud 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Atlassian Cloud 單一登入](#configure-atlassian-cloud-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Atlassian Cloud 測試使用者](#create-atlassian-cloud-test-user)**：使 Atlassian Cloud 中具有與 Azure AD 中的 Britta Simon 連結的相對應使用者。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Atlassian Cloud 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Atlassian Cloud] 應用程式整合分頁上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 區段上，若您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![[Application Name] 網域及 URL 單一登入資訊](common/idp-relay.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://auth.atlassian.com/saml/<unique ID>`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. 按一下 [設定額外的 URL]。

    d. 在 [轉送狀態] 文字方塊中，使用下列模式輸入 URL：`https://<instancename>.atlassian.net`

    > [!NOTE]
    > 上述值並非真正的值。 請使用實際的識別碼和回覆 URL 來更新這些值。 您將可從本教學課程稍後說明的 [Atlassian Cloud SAML 設定] 畫面取得這些真正的值。

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![[Application Name] 網域及 URL 單一登入資訊](common/both-signonurl.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<instancename>.atlassian.net`

    > [!NOTE]
    > 上述登入 URL 值並非真正的值。 使用實際的登入 URL 來更新此值。 請連絡 [Atlassian Cloud 用戶端支援小組](https://support.atlassian.com/)以取得此值。

6. 您的 Atlassian Cloud 應用程式會預期要有特定格式的 SAML 判斷提示，這需要您將自訂屬性對應新增到您的「SAML 權杖屬性」設定。 下列螢幕擷取畫面顯示預設屬性清單，其中的 **nameidentifier** 與 **user.userprincipalname** 相對應。 Atlassian Cloud 應用程式要求 **nameidentifier** 需與 **user.mail** 相對應，因此您必須按一下 [編輯] 圖示以編輯屬性對應，並變更屬性對應。

    ![映像](common/edit-attribute.png)

7. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載]，以依據您的需求從指定選項下載 [憑證 (Base64)]，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

8. 在 [安裝 Atlassian Cloud] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-atlassian-cloud-single-sign-on"></a>設定 Atlassian Cloud 單一登入

1. 若要針對您的應用程式設定 SSO，請使用系統管理員認證來登入 Atlassian 入口網站。

2. 您必須先驗證您的網域再繼續設定單一登入。 如需詳細資訊，請參閱 [Atlassian 網域驗證](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)文件。

3. 在左側窗格中，選取 [SAML single sign-on] \(SAML 單一登入\) 。 如果您尚未這樣做，請訂閱 Atlassian Identity Manager。

    ![設定單一登入](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

4. 在 [Add SAML configuration] \(新增 SAML 設定\) 視窗中，執行下列操作：

    ![設定單一登入](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. 在 [Identity provider Entity ID] \(身分識別提供者實體識別碼\) 方塊中，貼上您從 Azure 入口網站複製的 SAML 實體識別碼。

    b. 在 [Identity provider SSO URL] \(身分識別提供者 SSO URL\) 方塊中，貼上您從 Azure 入口網站複製的 SAML 單一登入服務 URL。

    c. 開啟從 Azure 入口網站下載的憑證 (.txt 檔案)，複製值 (不含 *Begin Certificate* 和 *End Certificate* 行)，然後將它貼到 [Public X509 certificate] \(公用 X509 憑證\) 方塊中。

    d. 按一下 [儲存組態] 。

5. 若要確定您已設定正確的 URL，請執行下列操作來更新 Azure AD 設定：

    ![設定單一登入](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. 在 SAML 視窗中，複製 [SP Identity ID] \(SP 身分識別識別碼\)，然後貼到 Azure 入口網站中 [Atlassian Cloud 網域及 URL] 底下的 [識別碼] 方塊中。

    b. 在 SAML 視窗中，複製 [SP Assertion Consumer Service URL] \(SP 判斷提示取用者服務 URL\)，然後貼到 Azure 入口網站中 [Atlassian Cloud 網域及 URL] 底下的 [回覆 URL] 方塊中。 登入 URL 是您 Atlassian Cloud 的租用戶 URL。

    > [!NOTE]
    > 如果您是現有的客戶，在更新 Azure 入口網站中的 [SP Identity ID] \(SP 身分識別識別碼\) 和 [SP Assertion Consumer Service URL] \(SP 判斷提示取用者服務 URL\) 值之後，請選取 [Yes, update configuration] \(是，更新設定\)。 如果您是新客戶，則可以略過這個步驟。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Atlassian Cloud 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Atlassian Cloud]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入並選取 [Atlassian Cloud]。

    ![應用程式清單中的 [Atlassian Cloud] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-atlassian-cloud-test-user"></a>建立 Atlassian Cloud 測試使用者

若要讓 Azure AD 使用者能夠登入 Atlassian Cloud，請執行下列操作，在 Atlassian Cloud 中手動佈建使用者帳戶：

1. 在 [Administration] \(管理\) 窗格中，選取 [Users] \(使用者\)。

    ![Atlassian Cloud [Users] \(使用者\) 連結](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. 若要在 Atlassian Cloud 中建立使用者，請選取 [Invite user] \(邀請使用者\)。

    ![建立 Atlassian Cloud 使用者](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. 在 [Email address] \(電子郵件地址\) 方塊中，輸入使用者的電子郵件地址，然後指派應用程式存取權。

    ![建立 Atlassian Cloud 使用者](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. 若要傳送電子郵件邀請給使用者，請選取 [Invite users] \(邀請使用者\)。 電子郵件邀請會傳送給使用者，使用者在接受邀請之後，就會在系統中變成作用中使用者。

> [!NOTE]
> 您也可以在 [Users] \(使用者\) 區段中選取 [Bulk Create] \(大量建立\) 按鈕來大量建立使用者。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Atlassian Cloud] 圖格時，應該會自動登入您已設定 SSO 的 Atlassian Cloud。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
   
