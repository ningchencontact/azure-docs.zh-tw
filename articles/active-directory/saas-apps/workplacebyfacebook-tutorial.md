---
title: 教學課程：Azure Active Directory 與 Workplace by Facebook 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Workplace by Facebook 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6cdc7ef8ee991719153f6daed01fbb76f945a7a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194932"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>教學課程：Azure Active Directory 與 Workplace by Facebook 整合

在本教學課程中，您會了解如何將 Workplace by Facebook 與 Azure Active Directory (Azure AD) 整合。
將 Workplace by Facebook 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 Workplace by Facebook 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Workplace by Facebook (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Workplace by Facebook 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Workplace by Facebook 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

> [!NOTE]
> Facebook 有兩項產品 Workplace Standard (免費) 和 Workplace Premium (付費)。 任何 Workplace Premium 租用戶都可以設定 SCIM 和 SSO 整合，而無須任何其他費用或授權。 Workplace Standard 執行個體中並未提供 SSO 和 SCIM。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Workplace by Facebook 支援 **SP** 起始的 SSO
* Workplace by Facebook 支援 **Just-In-Time 佈建**
* Workplace by Facebook 支援**[自動使用者佈建](workplacebyfacebook-provisioning-tutorial.md)**

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>從資源庫新增 Workplace by Facebook

若要設定將 Workplace by Facebook 整合到 Azure AD 中，您需要從資源庫將 Workplace by Facebook 新增到受控 SaaS 應用程式清單中。

**若要從資源庫新增 Workplace by Facebook，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Workplace by Facebook**，從結果面板中選取 [Workplace by Facebook]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 Workplace by Facebook](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 Workplace by Facebook 設定和測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Workplace by Facebook 中相關使用者之間的連結關聯性。

若要設定及測試與 Workplace by Facebook 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Workplace by Facebook 單一登入](#configure-workplace-by-facebook-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Workplace by Facebook 測試使用者](#create-workplace-by-facebook-test-user)** - 使 Workplace by Facebook 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 Workplace by Facebook 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Workplace by Facebook] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    ![Workplace by Facebook 網域及 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL：`https://<instancename>.facebook.com`

    b. 在 [識別碼 (實體識別碼)] 文字方塊中，使用下列模式輸入 URL：`https://www.facebook.com/company/<instanceID>`。

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請參閱 [Workplace 公司儀表板] 的 [驗證] 頁面，以查看您 Workplace 社群的正確值。

5. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載]，以依據您的需求從指定選項下載 [憑證 (Base64)]，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 Workplace by Facebook] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-workplace-by-facebook-single-sign-on"></a>設定 Workplace by Facebook 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Workplace by Facebook 公司網站。
  
   > [!NOTE]
   > 為將參數傳遞給 Azure AD，Workplace 可利用大小最多 2.5 KB 的查詢字串，作為 SAML 驗證流程的一部分。

2. 在 [Admin Panel] \(系統管理面板\) 中，依序移至 [Security] \(安全性\) 索引標籤和 [Authentication] \(驗證\)。

3. 在 [SAML 驗證] 下，從下拉式清單中選取 [僅限 SSO]。

4. 將您從 Azure 入口網站的 [Workplace by Facebook 設定] 區段中複製的值輸入對應的欄位中：

    * 在 [SAML URL] 文字方塊中，貼上您從 Azure 入口網站複製的**登入 URL** 值。
    * 在 [SAML 簽發者 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 **Azure AD 識別碼**值。
    * 在 [SAML 登出重新導向] \(選擇性\) 中，貼上您從 Azure 入口網站複製的**登出 URL** 值。
    * 在從 Azure 入口網站下載的記事本檔案中開啟您的 **base-64 編碼憑證**，將憑證的內容複製到剪貼簿，再貼到 [SAML 憑證] 文字方塊。

5. 您可能需要輸入 [基本 SAML 設定] 區段下所列的對象 URL、收件者 URL 和 ACS (判斷提示取用者服務) URL。

6. 捲動到區段的底部，然後按一下 [測試 SSO] 按鈕。 快顯視窗中的這個結果隨即顯示，並會出現 Azure AD 登入頁面。 如往常輸入您的認證以進行驗證。

    **疑難排解：** 確保從 Azure AD 傳回的電子郵件地址與您用來登入的 Workplace 帳戶相同。

7. 一旦測試已順利完成後，捲動到頁面底部，然後按一下 [儲存] 按鈕。

8. 現在，使用 Workplace 的所有使用者都將會看到進行驗證的 Azure AD 登入頁面。

9. **SAML 登出重新導向 (選擇性)** -

    您可以選擇性地選擇設定 SAML 登出 URL，可用來指向 Azure AD 的登出頁面。 當啟用並設定此設定時，就不會再將使用者導向至 Workplace 登出頁面。 相反地，系統會將使用者重新導向至 [SAML 登出重新導向] 設定中所新增的 URL。

### <a name="configuring-reauthentication-frequency"></a>設定重新驗證頻率

您可以將 Workplace 設定為每一天、每三天、每一週、每兩週、每一個月或一律不會提示進行 SAML 檢查。

> [!NOTE]
> 行動應用程式的 SAML 檢查最小值是設定為一週。

您也可以使用下列按鈕，強制所有使用者重設 SAML：要求所有使用者立即進行 SAML 驗證。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Workplace by Facebook 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Workplace by Facebook]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入 **Workplace by Facebook** 並加以選取。

    ![應用程式清單中的 Workplace by Facebook 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-workplace-by-facebook-test-user"></a>建立 Workplace by Facebook 測試使用者

本節會在 Workplace by Facebook 中建立名為 Britta Simon 的使用者。 Workplace by Facebook 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作。 如果使用者不存在於 Workplace by Facebook 中，當您嘗試存取 Workplace by Facebook 時，就會建立一個新的。

>[!Note]
>如果您需要手動建立使用者，請連絡 [Workplace by Facebook 用戶端支援小組](https://workplace.fb.com/faq/)

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Workplace by Facebook] 圖格時，應該會自動登入您設定 SSO 的 Workplace by Facebook。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [設定使用者佈建](workplacebyfacebook-provisioning-tutorial.md)
