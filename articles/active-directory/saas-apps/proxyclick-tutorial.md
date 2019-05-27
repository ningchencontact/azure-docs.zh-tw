---
title: 教學課程：Azure Active Directory 與 Proxyclick 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Proxyclick 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: b797c7d49495aac90abb31a9214bf2928784a866
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "65868694"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>教學課程：Azure Active Directory 與 Proxyclick 整合

在本教學課程中，您會了解如何整合 Proxyclick 與 Azure Active Directory (Azure AD)。
將 Proxyclick 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Proxyclick 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Proxyclick (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Proxyclick 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Proxyclick 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Proxyclick 支援由 **SP** 和 **IDP** 起始的 SSO

## <a name="adding-proxyclick-from-the-gallery"></a>從資源庫新增 Proxyclick

若要設定以將 Proxyclick 整合到 Azure AD 中，您需要從資源庫將 Proxyclick 新增到受控的 SaaS 應用程式清單。

**若要從資源庫新增 Proxyclick，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Proxyclick**，從結果面板中選取 [Proxyclick]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 [Proxyclick]](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 Proxyclick 來設定和測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Proxyclick 中相關使用者之間的連結關聯性。

若要搭配 Proxyclick 設定和測試 Azure AD 單一登入，您需要完成下列基本工作：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Proxyclick 單一登入](#configure-proxyclick-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Proxyclick 測試使用者](#create-proxyclick-test-user)** - 讓 Proxyclick 中的 Britta Simon 對應項目得以連結至 Azure AD 中代表該使用者的項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要搭配 Proxyclick 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Proxyclick] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![[Proxyclick 網域及 URL] 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://saml.proxyclick.com/init/<companyId>`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://saml.proxyclick.com/consume/<companyId>`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![[Proxyclick 網域及 URL] 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > 這些都不是真正的值。 您將會使用實際的識別碼、回覆 URL 與登入 URL (稍後會在本教學課程中說明) 來更新這些值。

6. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載]，以依據您的需求從指定選項下載 [憑證 (Base64)]，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

7. 在 [設定 Proxyclick] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-proxyclick-single-sign-on"></a>設定 Proxyclick 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Proxyclick 公司網站。

2. 選取 [Account & Settings] \(帳戶和設定\)。

    ![[Proxyclick 設定]](./media/proxyclick-tutorial/configure1.png)

3. 向下捲動至 [INTEGRATIONS] \(整合\)，並選取 [SAML]。

    ![[Proxyclick 設定]](./media/proxyclick-tutorial/configure2.png)

4. 在 [SAML] 區段中，執行下列步驟：

    ![[Proxyclick 設定]](./media/proxyclick-tutorial/configure3.png)

    a. 複製 [SAML 取用者 URL] 值，並且在 Azure 入口網站上貼入 [基本 SAML 設定] 區段的 [回覆 URL] 文字方塊中。

    b. 複製 [SAML SSO Redirect URL] \(SAML SSO 重新導向 URL\) 值，並將其貼到 Azure 入口網站上 [基本 SAML 設定] 區段的 [登入 URL] 和 [識別碼] 文字方塊中。

    c. 將 [SAML Request Method] \(SAML 要求方法\) 選取為 [HTTP Redirect] \(HTTP 重新導向\)。

    d. 在 [簽發者] 文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼] 值。

    e. 在 [SAML 2.0 端點 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    f. 在記事本中開啟您從 Azure 入口網站下載的憑證檔，然後將它貼到 [Certificate] \(憑證\) 文字方塊中。

    g. 按一下 [儲存變更] 。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 brittasimon@yourcompanydomain.extension。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Proxyclick 的存取權授與 Britta Simon，讓該使用者能使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Proxyclick]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Proxyclick]。

    ![應用程式清單中的 [Proxyclick] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-proxyclick-test-user"></a>建立 Proxyclick 測試使用者

若要讓 Azure AD 使用者能夠登入 Proxyclick，必須將使用者佈建到 Proxyclick。 Proxyclick 需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Proxyclick 公司網站。

1. 在頂端導覽列上按一下 [Colleagues] \(同事\)。

    ![新增員工](./media/proxyclick-tutorial/user1.png)

1. 按一下 [Add Colleague] \(新增同事\)。

    ![新增員工](./media/proxyclick-tutorial/user2.png)

1. 在 [Add a colleague] \(新增同事\) 區段中，執行下列步驟：

    ![新增員工](./media/proxyclick-tutorial/user3.png)

    a. 在 [電子郵件] 文字方塊中，輸入像是 brittasimon@contoso.com 的使用者電子郵件地址。

    b. 在 [名字] 文字方塊中，輸入使用者的名字，例如 Britta。

    c. 在 [姓氏] 文字方塊中，輸入使用者的姓氏，例如 Simon。

    d. 按一下 [新增使用者] 。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Proxyclick] 圖格時，應該會自動登入您已設定 SSO 的 Proxyclick。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

