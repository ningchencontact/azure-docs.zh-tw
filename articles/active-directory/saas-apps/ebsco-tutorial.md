---
title: 教學課程：Azure Active Directory 與 EBSCO 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 EBSCO 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2753daf225016d3bd8e07383193a1260b40a36d5
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564970"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>教學課程：Azure Active Directory 與 EBSCO 整合

在本教學課程中，您將了解如何整合 EBSCO 與 Azure Active Directory (Azure AD)。
整合 EBSCO 與 Azure AD 將會有下列優點：

* 您可以在 Azure AD 中控制可存取 EBSCO 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 EBSCO (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 EBSCO 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 EBSCO 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* EBSCO 支援由 **SP** 和 **IDP** 起始的 SSO

* EBSCO 支援 **Just In Time** 使用者佈建

## <a name="adding-ebsco-from-the-gallery"></a>從資源庫新增 EBSCO

若要設定將 EBSCO 整合到 Azure AD 中，您必須從資源庫將 EBSCO 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 EBSCO，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左側導覽面板中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **EBSCO**，從結果面板中選取 [EBSCO]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 EBSCO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，搭配 EBSCO 來設定和測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 EBSCO 中相關使用者之間的連結關聯性。

若要設定及測試 EBSCO 的 Azure AD 單一登入，您必須完成下列建置區塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 EBSCO 單一登入](#configure-ebsco-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 EBSCO 測試使用者](#create-ebsco-test-user)** - 在 EBSCO 中建立 Britta Simon 的對應項目，使該項目與 Azure AD 中代表該使用者的項目連結。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要搭配 EBSCO 設定 Azure AD 單一登入，請執行以下步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [EBSCO] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上按一下 [編輯] 圖示，以開啟 [基本 SAML 組態] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![EBSCO 網域和 URL 單一登入資訊](common/idp-identifier.png)

    在 [識別碼] 文字方塊中，鍵入 URL：`pingsso.ebscohost.com`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![映像](common/both-preintegrated-signon.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > [登入 URL] 的值不是真正的值。 請使用實際的「登入 URL」來更新此值。 請連絡 [EBSCO 客戶支援小組](mailto:sso@ebsco.com)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

    o   **唯一元素：**  

    o   **Custid** = 輸入唯一的 EBSCO 客戶識別碼 

    o   **Profile** = 客戶可以自訂連結，將使用者導向至特定的設定檔 (視他們向 EBSCO 購買的產品而定)。 他們可以輸入特定的設定檔識別碼。 主要識別碼是 eds (EBSCO 探索服務) 和 ehost (EBSOCOhost 資料庫)。 相同項目的指示提供於[這裡](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)。

6. EBSCO 應用程式會預期特定格式的 SAML 判斷提示，這會需要您將自訂屬性對應加入至您的 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯] **** 圖示，以開啟 [使用者屬性] **** 對話方塊。

    ![映像](common/edit-attribute.png)

     > [!Note]
    > **name** 是必要屬性，其會與 EBSCO 應用程式中的 [名稱識別碼值] 相對應。 依預設會新增此屬性，因此您無須手動新增。

7. 除了以上屬性外，EBSCO 應用程式還會預期 SAML 回應傳回更多屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示： 

    | Name | 來源屬性|
    | ---------------| --------------- |    
    | 名字   | user.givenname |
    | 姓氏   | user.surname |
    | 電子郵件   | user.mail |

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間] 保持空白。

    d. 選取 [來源] 作為 [屬性]。

    e. 在 [來源屬性] 清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [檔案] 。

8. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載] 以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

9. 在 [設定 EBSCO] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-ebsco-single-sign-on"></a>設定 EBSCO 單一登入

若要在 **EBSCO** 端設定單一登入，您必須將從 Azure 入口網站下載的 [中繼資料 XML] 和複製的適當 URL 傳送給 [EBSCO 支援小組](mailto:sso@ebsco.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

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

在本節中，您會把 EBSCO 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [EBSCO]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [EBSCO]。

    ![應用程式清單中的 EBSCO 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-ebsco-test-user"></a>建立 EBSCO 測試使用者

使用 EBSCO 時，會自動佈建使用者。

**若要佈建使用者帳戶，請執行下列步驟：**

Azure AD 會將所需的資料傳遞至 EBSCO 應用程式。 EBSCO 的使用者佈建可以自動執行，或是使用一次性表單。 這取決於客戶是否有已儲存個人設定的大量現有 EBSCOhost 帳戶。 相同的問題也可在實作期間與 [EBSCO 支援小組](mailto:sso@ebsco.com)討論。 無論如何，客戶都不需要在測試之前建立任何 EBSCOhost 帳戶。

   >[!Note]
   >您可以自動化 EBSCOhost 使用者佈建/個人化。 向 [EBSCO 支援小組](mailto:sso@ebsco.com)洽詢關於 Just-in-Time 使用者佈建的問題。 

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

1. 當您在存取面板中按一下 EBSCO 圖格時，應該會自動登入您的 EBSCO 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

2. 登入應用程式後，請按一下右上角的 [登入] 按鈕。

    ![應用程式清單中的 EBSCO 登入](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. 您會收到一次性的提示，要您透過 [立即將您現有的 MyEBSCOhost 帳戶連結至機構帳戶] 或 [建立新的 MyEBSCOhost 帳戶並將其連結至您的機構帳戶]，進行機構/SAML 登入的配對。 此帳戶用於 EBSCOhost 應用程式的個人化。 請選取 [建立新的帳戶] 選項，您會看到個人化的表單已使用 saml 回應中的值預先完成，如以下螢幕擷取畫面所示。 請按一下 [繼續] 以儲存此選取項目。
    
     ![應用程式清單中的 EBSCO 使用者](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. 完成上述設定後，請清除 cookie/快取，然後重新登入。 您無須手動重新登入，系統會記住個人化設定

## <a name="additional-sesources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

