---
title: 教學課程：Azure Active Directory 與 G Suite 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 G Suite 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.openlocfilehash: dd413f9a7eba60fd72e7cc29f44f49b72eaaf806
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769401"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>教學課程：Azure Active Directory 與 G Suite 整合

在本教學課程中，您將了解如何整合 G Suite 與 Azure Active Directory (Azure AD)。
G Suite 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 G Suite 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 G Suite (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 G Suite 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 G Suite 單一登入的訂用帳戶
- Google Apps 訂用帳戶或 Google Cloud Platform 訂用帳戶。

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。 本文件是使用新的使用者單一登入體驗所建立。 如果您仍然使用舊版，則安裝程式看起來會不同。 您可以在 G-Suite 應用程式的單一登入設定中啟用新的體驗。 移至 [Azure AD]、[企業應用程式]，選取 [G Suite]，選取 [單一登入]，然後按一下 [試用新的體驗]。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="frequently-asked-questions"></a>常見問題集

1. **問：這項整合支援 Google Cloud Platform 單一登入與 Azure AD 的整合嗎？**

    答：是。 Google Cloud Platform 和 Google Apps 共用相同的驗證平台。 因此，為了進行 GCP 整合，您需要設定 Google Apps 單一登入。

2. **問：Chromebook 及其他 Chrome 裝置是否與 Azure AD 單一登入相容？**
  
    答：是，使用者能夠使用其 Azure AD 認證來登入其 Chromebook 裝置。 若要了解為何使用者可能收到兩次提供認證的提示，請參閱此 [G Suite 支援文章](https://support.google.com/chrome/a/answer/6060880) 。

3. **問：如果我啟用單一登入，使用者是否將能夠使用其 Azure AD 認證來登入任何 Google 產品 (例如 Google Classroom、GMail、Google 雲端硬碟、YouTube 等)？**

    答：是，視您選擇為組織啟用或停用的 [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) 而定。

4. **問：我是否可以只為一部分 G Suite 使用者啟用單一登入？**

    答：否，開啟單一登入會立即要求您的所有 G Suite 使用者使用其 Azure AD 認證進行驗證。 由於 G Suite 不支援使用多個識別提供者，因此 G Suite 環境的識別提供者可以是 Azure AD 或 Google 其中之一，但不可同時是兩者。

5. **問：如果使用者透過 Windows 登入，他們是否會自動向 G Suite 進行驗證，而不會收到輸入密碼的提示？**

    答：有兩個選項可允許這樣的情況。 第一個是，使用者可以透過 [Azure Active Directory Join](../device-management-introduction.md)登入 Windows 10 裝置。 另一個是，使用者可以登入已加入某個內部部署 Active Directory 網域的 Windows 裝置，其中此內部部署 Active Directory 已透過 [Active Directory 同盟服務 (AD FS)](../hybrid/plan-connect-user-signin.md) 部署而能夠單一登入到 Azure AD。 這兩個選項都需要您執行下列教學課程的步驟，才能在 Azure AD 與 G Suite 之間啟用單一登入。

6. **問：當我收到「無效的電子郵件」錯誤訊息時該怎麼辦？**

    答：此設定需要電子郵件屬性，使用者才能夠登入。 目前無法手動設定此屬性。

    系統針對任何具備有效 Exchange 授權的使用者，自動填入電子郵件屬性。 如果使用者並未啟用電子郵件功能，則會收到此錯誤，因為應用程式需要取得此屬性才能授與存取權。

    您可以使用系統管理員帳戶前往 portal.office.com，然後按一下系統管理中心、帳單、訂用帳戶，選取您的 Office 365 訂用帳戶，然後按一下 [指派使用者]，選取您要檢查其訂用帳戶的使用者，然後在右窗格中按一下編輯授權。

    指派 O365 授權後，可能需要幾分鐘的時間才會套用。 然後，系統會自動填入 user.mail 屬性，此問題應該就解決了。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* G Suite 支援 **SP** 起始的 SSO
* G Suite 支援**[自動使用者佈建](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)**

## <a name="adding-g-suite-from-the-gallery"></a>從資源庫新增 G Suite

若要設定 G Suite 與 Azure AD 整合，您需要從資源庫將 G Suite 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 G Suite，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **G Suite**，從結果面板中選取 **G Suite**，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 G Suite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 G Suite 設定和測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 G Suite 中相關使用者之間的連結關聯性。

若要使用 G Suite 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 G Suite 單一登入](#configure-g-suite-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 G Suite 測試使用者](#create-g-suite-test-user)** - 使 G Suite 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 G Suite 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [G Suite] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定] 區段上，如果要設定 **Gmail**，執行下列步驟：

    ![G Suite 網域與 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL] 文字方塊中，使用下列格式輸入 URL︰`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [G Suite 用戶端支援小組](https://www.google.com/contact/)以取得這些值。

5. 在 [基本 SAML 設定] 區段上，如果要設定 **Google Cloud Platform**，執行下列步驟：

    ![G Suite 網域與 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL] 文字方塊中，使用下列格式輸入 URL︰`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [G Suite 用戶端支援小組](https://www.google.com/contact/)以取得這些值。

6. G Suite 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示上述的範例。 [唯一的使用者識別碼] 的預設值是 **user.userprincipalname**，但是 G Suite 會預期這是與使用者電子郵件地址對應的值。 對此您可以使用清單中的 **user.mail** 屬性，或者根據組織組態使用適當的屬性值。

    ![映像](common/edit-attribute.png)

7. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，使用 [編輯] 圖示來編輯宣告或使用 [新增宣告] 來新增宣告，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    | Name | 來源屬性 |
    | ---------------| --------------- |
    | 唯一的使用者識別碼 | User.mail |

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間] 保持空白。

    d. 選取 [來源] 作為 [屬性]。

    e. 在 [來源屬性] 清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]。

    g. 按一下 [檔案] 。

8. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載]，以依據您的需求從指定選項下載 [憑證 (Base64)]，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

9. 在 [設定 G Suite] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-g-suite-single-sign-on"></a>設定 G Suite 單一登入

1. 在瀏覽器中開啟新索引標籤，然後使用系統管理員帳戶登入 [G Suite 管理控制台](http://admin.google.com/)。

2. 按一下 [安全性] 。 如果您沒有看到連結，它可能隱藏在畫面底部的 [其他控制項]  功能表之下。

    ![按一下 [安全性]。][10]

3. 在 [安全性] 頁面上，按一下 [設定單一登入 (SSO)]。

    ![按一下 [SSO]。][11]

4. 執行下列組態變更：

    ![設定 SSO][12]

    a. 選取 [使用第三方識別提供者來設定 SSO]。

    b. 在 G Suite 的 [登入頁面 URL] 欄位中，貼上您從 Azure 入口網站複製的 [登入 URL] ****  值。

    c. 在 G Suite 的 [登出頁面 URL] 欄位中，貼上您從 Azure 入口網站複製的 [登出 URL] ****  值。

    d. 在 G Suite 的 [變更密碼 URL] 欄位中，貼上您從 Azure 入口網站複製的 [變更密碼 URL] ****  值。

    e. 在 G Suite 中，請於 [驗證憑證] 上傳您從 Azure 入口網站下載的憑證。

    f. 選取 [使用網域特定簽發者]。

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
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 G Suite 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [G Suite]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入 **G Suite** 並加以選取。

    ![應用程式清單中的 G Suite 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-g-suite-test-user"></a>建立 G Suite 測試使用者

本節目標是在 G Suite 軟體中建立名為 Britta Simon 的使用者。 G Suite 支援預設啟用的自動佈建。 在這一節沒有您需要進行的動作。 如果 G Suite 軟體中還沒有使用者，當您嘗試存取 G Suite 軟體時，就會建立新的使用者。

> [!NOTE]
> 在測試單一登入之前，如果尚未在 Azure AD 中開啟佈建，請確定您的使用者已經存在於 G Suite 中。

> [!NOTE]
> 如果您需要手動建立使用者，請連絡 [Google 支援小組](https://www.google.com/contact/)。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [G Suite] 圖格時，應該會自動登入您設定 SSO 的 G Suite。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [設定使用者佈建](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png