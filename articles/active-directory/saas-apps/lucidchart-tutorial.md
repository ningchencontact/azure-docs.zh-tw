---
title: 教學課程：Azure Active Directory 與 Lucidchart 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Lucidchart 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e12b5984eb2fe4e54ffec7d6b4a2a6da202c70e4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097794"
---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>教學課程：Azure Active Directory 與 Lucidchart 整合

在本教學課程中，您將了解如何整合 Lucidchart 與 Azure Active Directory (Azure AD)。
Lucidchart 與 Azure AD 整合有下列優點：

* 您可以在 Azure AD 中控制可存取 Lucidchart 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Lucidchart (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要進行 Azure AD 與 Lucidchart 整合的設定，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 啟用 Lucidchart 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Lucidchart 支援 **SP** 起始的 SSO
* Lucidchart 支援 **Just In Time** 使用者佈建

## <a name="adding-lucidchart-from-the-gallery"></a>從資源庫新增 Lucidchart

若要進行 Lucidchart 與 Azure AD 整合的設定，您需要從資源庫將 Lucidchart 新增至受控 SaaS 應用程式清單中。

**若要從資源庫新增 Lucidchart，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Lucidchart**，從結果面板中選取 [Lucidchart]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Lucidchart](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您將以名為 **Britta Simon** 的測試使用者身分，設定及測試與 Lucidchart 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Lucidchart 中相關使用者之間的連結關聯性。

若要設定及測試與 Lucidchart 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Lucidchart 單一登入](#configure-lucidchart-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Lucidchart 測試使用者](#create-lucidchart-test-user)** - 使 Lucidchart 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定 Lucidchart 的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/) 的 [Lucidchart]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![Lucidchart 網域與 URL 單一登入資訊](common/sp-signonurl.png)

    在 [登入 URL]  文字方塊中，將 URL 輸入為：`https://chart2.office.lucidchart.com/saml/sso/azure`

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 Lucidchart]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-lucidchart-single-sign-on"></a>設定 Lucidchart 單一登入

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Lucidchart 公司網站。

2. 在頂端的功能表中，按一下 [小組]  。

    ![小組](./media/lucidchart-tutorial/ic791190.png "小組")

3. 按一下 [應用程式] \> [管理 SAML]  。

    ![管理 SAML](./media/lucidchart-tutorial/ic791191.png "管理 SAML")

4. 在 [SAML 驗證設定]  對話方塊頁面上執行下列步驟：

    a. 選取 [啟用 SAML 驗證]  ，然後按一下 [選用]  。

    ![SAML 驗證設定](./media/lucidchart-tutorial/ic791192.png "SAML 驗證設定")

    b. 在 [網域]  文字方塊中，輸入您的網域，然後按一下 [變更憑證]  。

    ![變更憑證](./media/lucidchart-tutorial/ic791193.png "變更憑證")

    c. 開啟您下載的中繼資料檔案，然後複製內容並貼到並 [上傳中繼資料]  文字方塊中。

    ![上傳中繼資料](./media/lucidchart-tutorial/ic791194.png "上傳中繼資料")

    d. 選取 [Automatically Add new users to the team]\(自動新增使用者至小組\)  ，然後按一下 [儲存變更]  。

    ![儲存變更](./media/lucidchart-tutorial/ic791195.png "儲存變更")

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您將把 Lucidchart 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Lucidchart]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Lucidchart]  。

    ![應用程式清單中的 Lucidchart 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-lucidchart-test-user"></a>建立 Lucidchart 測試使用者

沒有動作項目可讓您設定 Lucidchart 使用者佈建。  當指派的使用者嘗試使用存取面板登入 Lucidchart 時，Lucidchart 會檢查使用者是否存在。  

如果尚無可用的使用者帳戶，Lucidchart 會自動予以建立。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Lucidchart 圖格時，應該會自動登入您已設定 SSO 的 Lucidchart。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
