---
title: 教學課程：Azure Active Directory 與 ADP 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 ADP 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.openlocfilehash: 487c9cb145000b92a4aa664ea2bd159026104b6b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065149"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>教學課程：Azure Active Directory 與 ADP

在本教學課程中，您會了解如何將 ADP 與 Azure Active Directory (Azure AD) 整合。
整合 ADP 與 Azure AD 提供下列優點：

* 您可以在 Azure AD 中控制可存取 ADP 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 ADP (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 ADP 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 ADP 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* ADP 支援由 **IDP** 起始的 SSO

## <a name="adding-adp-from-the-gallery"></a>從資源庫新增 ADP

若要設定將 ADP 整合到 Azure AD 中，您需要從資源庫將 ADP 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 ADP，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **ADP**，從結果面板中選取 [ADP]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 ADP](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 ADP 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 ADP 中相關使用者之間的連結關聯性。

若要設定及測試與 ADP 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 ADP 單一登入](#configure-adp-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 ADP 測試使用者](#create-adp-test-user)** - 在 ADP 中建立一個與 Azure AD 中代表 Britta Simon 之項目連結的 Britta Simon 對應項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 ADP 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 Azure 入口網站的 **ADP** 應用程式整合頁面上，按一下 [屬性] 索引標籤，然後執行下列步驟： 

    ![單一登入屬性](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. 將 [為使用者啟用登入] 欄位值設定為 [是]。

    b. 複製 [使用者存取 URL]，而且您必須將其貼到 [設定登入 URL] 區段中，本教學課程稍後會說明。

    c. 將 [需要使用者指派] 欄位值設定為 [是]。

    d. 將 [是否要向使用者顯示] 欄位值設定為 [否]。

2. 在 [Azure 入口網站](https://portal.azure.com/)的 [ADP] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

3. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

4. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

5. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    ![ADP 網域與 URL 單一登入資訊](common/idp-identifier.png)

    在 [識別碼 (實體識別碼)] 文字方塊中，輸入 URL：`https://fed.adp.com`

6. ADP 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性] 區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [使用者屬性] 對話方塊。 宣告名稱將一律為 **"PersonImmutableID"**，且其值為我們為了與 **employeeid** 對應而顯示的值。 

    會透過 **employeeid** 完成從 Azure AD 到 ADP 的使用者對應，但您也可以根據應用程式設定，將其對應到不同的值。 因此，請先與 [ADP 支援小組](https://www.adp.com/contact-us/overview.aspx)合作以使用正確的使用者識別碼，然後將該值與 **"PersonImmutableID"** 宣告對應。

    ![映像](common/edit-attribute.png)

7. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，使用 [編輯] 圖示來編輯宣告或使用 [新增宣告] 來新增宣告，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：
    
    | Name | 來源屬性 | 
    | ---------------| --------------- |
    | PersonImmutableID  | user.employeeid |

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間] 保持空白。

    d. 選取 [來源] 作為 [屬性]。

    e. 在 [來源屬性] 清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]。

    g. 按一下 [檔案] 。

    > [!NOTE] 
    > 您必須先連絡 [ADP 支援小組](https://www.adp.com/contact-us/overview.aspx)，向其要求租用戶的唯一使用者識別碼屬性值，才能設定 SAML 判斷提示。 您需要此值來設定應用程式的自訂宣告。 

8. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

### <a name="configure-adp-single-sign-on"></a>設定 ADP 單一登入

若要在 **ADP** 端設定單一登入，您必須將已下載的**中繼資料 XML** 上傳至 [ADP 網站](https://adpfedsso.adp.com/public/login/index.fcc)。

> [!NOTE]  
> 此流程可能需要幾天的時間。

### <a name="configure-your-adp-services-for-federated-access"></a>設定 ADP 服務以進行同盟存取

>[!Important]
> 您的員工如果需要對 ADP 服務進行同盟存取，您就必須將其指派給 ADP 服務應用程式，接著再將其重新指派給特定的 ADP 服務。
從您的 ADP 代表收到確認之後，請設定您的 ADP 服務並指派/管理使用者，以控制使用者對特定 ADP 服務的存取權。

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **ADP**，從結果面板中選取 [ADP]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 ADP](common/search-new-app.png)

5. 在 Azure 入口網站的 **ADP** 應用程式整合頁面上，按一下 [屬性] 索引標籤，然後執行下列步驟：  

    ![單一登入已連結的屬性](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  將 [為使用者啟用登入] 欄位值設定為 [是]。

    b.  將 [需要使用者指派] 欄位值設定為 [是]。

    c.  將 [是否要向使用者顯示] 欄位值設定為 [是]。

6. 在 [Azure 入口網站](https://portal.azure.com/)的 [ADP] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

7. 在 [選取單一登入方法] 對話方塊上，選取 [連結的] 作為 [模式]， 以將您的應用程式連結至 **ADP**。

    ![已連結的單一登入](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. 瀏覽至 [設定登入 URL] 區段，執行下列步驟︰

    ![單一登入屬性](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. 貼上您從上面 [屬性] 索引標籤 (來自主要 ADP 應用程式) 複製的 [使用者存取 URL]。
                                                             
    b. 以下是 5 個支援不同**轉送狀態 URL** 的應用程式。 您必須手動將特定應用程式的適當**轉送狀態 URL** 值附加至 [使用者存取 URL]。
    
    * **ADP Workforce Now**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **儲存** 您的變更。

10. 從您的 ADP 代表收到確認之後，使用一或兩個使用者開始進行測試。

    a. 將幾個使用者指派給 ADP 服務應用程式，以測試同盟存取。

    b. 當使用者存取資源庫上的 ADP 服務應用程式，且能夠存取其 ADP 服務時，即表示測試成功。
 
11. 確認測試成功時，將同盟 ADP 服務指派給個別的使用者或使用者群組 (本教學課程稍後會說明)，然後向您的員工首度發行。
 
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

在本節中，您會將 ADP 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [ADP]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入 **ADP** 並加以選取。

    ![應用程式清單中的 ADP 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-adp-test-user"></a>建立 ADP 測試使用者

本節目標是在 ADP 中建立名為 Britta Simon 的使用者。 請與 [ADP 支援小組](https://www.adp.com/contact-us/overview.aspx)合作，在 ADP 帳戶中新增使用者。 

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [ADP] 圖格時，應該會自動登入您已設定 SSO 的 ADP。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

