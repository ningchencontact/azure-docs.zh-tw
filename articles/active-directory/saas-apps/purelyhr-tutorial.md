---
title: 教學課程：Azure Active Directory 與 PurelyHR 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 PurelyHR 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 86a9c454-596d-4902-829a-fe126708f739
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0197a18cd70a7f4305768c96b41a05adfc127df4
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621051"
---
# <a name="tutorial-azure-active-directory-integration-with-purelyhr"></a>教學課程：Azure Active Directory 與 PurelyHR 整合

在本教學課程中，您會了解如何整合 PurelyHR 與 Azure Active Directory (Azure AD)。
PurelyHR 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 PurelyHR 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 PurelyHR (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 PurelyHR 與 Azure AD 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 PurelyHR 單一登入功能的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* PurelyHR 支援由 **SP** 和 **IDP** 起始的 SSO

* PurelyHR 支援 **Just In Time** 使用者佈建

## <a name="adding-purelyhr-from-the-gallery"></a>從資源庫新增 PurelyHR

若要設定將 PurelyHR 整合到 Azure AD 中，您需要從資源庫將 PurelyHR 新增到受控 SaaS app 清單。

**如要從資源庫新增 PurelyHR，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **PurelyHR**，從結果面板中選取 [PurelyHR]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 PurelyHR](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 PurelyHR 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 PurelyHR 中相關使用者之間的連結關聯性。

若要使用 PurelyHR 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 PurelyHR 單一登入](#configure-purelyhr-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 PurelyHR 測試使用者](#create-purelyhr-test-user)** - 使 PurelyHR 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 PurelyHR 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [PurelyHR] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![PurelyHR 網域及 URL 單一登入資訊](common/both-replyurl.png)

    在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<companyID>.purelyhr.com/sso-consume`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![PurelyHR 網域及 URL 單一登入資訊](common/both-signonurl.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的回覆 URL 與登入 URL 更新這些值。 請連絡 [PurelyHR 用戶端支援小組](https://support.purelyhr.com/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

6. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載]，以依據您的需求從指定選項下載 [憑證 (Base64)]，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

7. 在 [設定 PurelyHR] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-purelyhr-single-sign-on"></a>設定 PurelyHR 單一登入

1. 若要在 **PurelyHR** 端設定單一登入，請以系統管理員身分登入其網站。

2. 從工具列選項開啟**儀表板**，然後按一下 [SSO 設定]。

3. 如下所述貼上方塊中的值-

    ![設定單一登入](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. 在記事本中開啟您從 Azure 入口網站下載的 **Certificate(Bas64)**，並複製憑證值。 將複製的值貼至 [X.509 憑證] 方塊。

    b. 在 [Idp 簽發者 URL] 方塊中，貼上從 Azure 入口網站複製的 **Azure AD 識別碼**。

    c. 在 [IDP 端點 URL] 方塊中，貼上從 Azure 入口網站複製的 [登入 URL]。 

    d. 核取 [自動建立使用者] 核取方塊，以在 PurelyHR 中啟用自動使用者佈建。

    e. 按一下 [儲存變更] 來儲存這些設定。

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

在本節中，您會把 PurelyHR 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [PurelyHR]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [PurelyHR]。

    ![應用程式清單中的 PurelyHR 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-purelyhr-test-user"></a>建立 PurelyHR 測試使用者

若要讓 Azure AD 使用者可以登入 PurelyHR，則必須將他們佈建到 PurelyHR。 在 PurelyHR 中，佈建是自動進行的工作，當自動使用者佈建功能啟用時，您不需要進行任何手動步驟。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [PurelyHR] 圖格時，應該會自動登入您設定 SSO 的 PurelyHR。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

