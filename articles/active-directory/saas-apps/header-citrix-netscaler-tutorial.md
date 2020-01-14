---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Citrix NetScaler (以標頭為基礎的驗證) 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Citrix NetScaler 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0adc7bf-696d-44c9-a57a-f9e9471b8710
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9c442ca731ecb10f977c19b86cb32caded36659
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75470533"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-header-based-authentication"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Citrix NetScaler (以標頭為基礎的驗證) 整合

在本教學課程中，您會了解如何整合 Citrix NetScaler 與 Azure Active Directory (Azure AD)。 在整合 Citrix NetScaler 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Citrix NetScaler 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Citrix NetScaler。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Citrix NetScaler 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Citrix NetScaler 支援由 **SP** 起始的 SSO

* Citrix NetScaler 支援 **Just In Time** 使用者佈建

- [設定 Citrix NetScaler 單一登入以進行以標頭為基礎的驗證](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [設定 Citrix NetScaler 單一登入以進行以 Kerberos 為基礎的驗證](citrix-netscaler-tutorial.md)


## <a name="adding-citrix-netscaler-from-the-gallery"></a>從資源庫新增 Citrix NetScaler

若要設定將 Citrix NetScaler 整合到 Azure AD 中，您必須從資源庫將 Citrix NetScaler 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Citrix NetScaler**。
1. 從結果面板選取 [Citrix NetScaler]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>設定及測試 Citrix NetScaler 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Citrix NetScaler 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Citrix NetScaler 中相關使用者之間的連結關聯性。

若要設定及測試與 Citrix NetScaler 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Citrix NetScaler SSO](#configure-citrix-netscaler-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Citrix NetScaler 測試使用者](#create-citrix-netscaler-test-user)** - 在 Citrix NetScaler 中建立一個與 Azure AD 中代表 Britta Simon 之使用者連結的 B.Simon 對應項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Citrix NetScaler]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://<<Your FQDN>>`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡 [Citrix NetScaler 用戶端支援小組](https://www.citrix.com/contact/technical-support.html)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

    > [!NOTE]
    > 為了讓 SSO 能夠運作，應該要能夠從公用網站存取這些 URL。 您必須在 Netscaler 端啟用防火牆或其他安全性設定，才能讓 Azure AD 在所設定的 ACS URL 上公佈權杖。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [應用程式同盟中繼資料 URL]  、加以複製並儲存到您的記事本。

    ![憑證下載連結](common/certificatebase64.png)

1. Citrix NetScaler 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增到您的 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯]  圖示，然後變更屬性對應。

    ![image](common/edit-attribute.png)

1. 除了以上屬性外，Citrix NetScaler 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | 名稱 | 來源屬性|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    1. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    1. 讓 [命名空間]  保持空白。

    1. 選取 [來源] 作為 [屬性]  。

    1. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    1. 按一下 [確定]  。

    1. 按一下 [檔案]  。


1. 在 [設定 Citrix NetScaler]  區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Citrix NetScaler 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Citrix NetScaler]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-citrix-netscaler-sso"></a>設定 Citrix NetScaler SSO

- [設定 Citrix NetScaler 單一登入以進行以標頭為基礎的驗證](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [設定 Citrix NetScaler 單一登入以進行以 Kerberos 為基礎的驗證](citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>發佈 Web 伺服器 

1. 建立 [虛擬伺服器]  。

    a. 移至 [流量管理] > [負載平衡] > [服務]  。
    
    b. 按一下 [新增]  。

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/web01.png)

    c. 針對執行下列應用程式的 Web 伺服器指定其詳細資料：
    * **服務名稱**
    * **伺服器 IP/現有伺服器**
    * **通訊協定**
    * **通訊埠**

     ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>設定 Load Balancer

1. 若要設定 Load Balancer，請執行下列步驟：

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/load01.png)

    a. 移至 [流量管理] > [負載平衡] > [虛擬伺服器]  。

    b. 按一下 [新增]  。

    c. 指定下列詳細資料：

    * **名稱**
    * **通訊協定**
    * **IP 位址**
    * **通訊埠**
    * 按一下 [確定] 

### <a name="bind-virtual-server"></a>繫結虛擬伺服器

繫結 Load Balancer 與先前建立的虛擬伺服器。

![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/bind01.png)

![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>繫結憑證

我們會將此服務發佈為 SSL，因此請繫結伺服器憑證，然後測試您的應用程式。

![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/bind03.png)

![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML 設定檔

### <a name="create-authentication-policy"></a>建立驗證原則

1. 移至 [安全性] > [AAA – 應用程式流量] > [原則] > [驗證] > [驗證原則]  。

2. 按一下 [新增]  ，然後指定 [詳細資料]。

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/policy01.png)

    a. [驗證原則]  的名稱。

    b. 運算式：**true**。

    c. 動作類型 **SAML**。

    d. 動作 = 按一下 [新增]  (遵循「建立驗證 SAML 伺服器精靈」)。
    
    e. 按一下 [驗證原則]  上的 [建立]。

### <a name="create-authentication-saml-server"></a>建立驗證 SAML 伺服器

1. 執行下列步驟：

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/server01.png)

    a. 指定 [名稱]  。

    b. 匯入中繼資料 (從 Azure SAML UI 指定您在前面複製的同盟中繼資料 URL)。
    
    c. 指定 [簽發者名稱]  。

    d. 按一下 [建立]  。

### <a name="create-authentication-virtual-server"></a>建立驗證虛擬伺服器

1.  移至 [安全性] > [AAA - 應用程式流量] > > [驗證虛擬伺服器]  。

2.  按一下 [新增]  並且執行下列步驟：

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/server02.png)

    a.  提供 [名稱]  。

    b.  選擇 [無法定址]  。

    c.  通訊協定 [SSL]  。

    d.  按一下 [確定]  。

    e.  按一下 **[繼續]** 。

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>將驗證虛擬伺服器設定為使用 Azure AD

您將需要修改 [驗證虛擬伺服器] 的 2 個區段。

1.  **進階驗證原則**

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/virtual01.png)

    a. 選取您先前建立的 [驗證原則]  。

    b. 按一下 [繫結]  。

      ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/virtual02.png)

2. **以表單為基礎的虛擬伺服器**

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/virtual03.png)

    a.  您將需要提供 [FQDN]  ，原因是 UI 會強制要求您提供。

    b.  選擇您想要使用 Azure AD 驗證來保護的 [虛擬伺服器 Load Balancer]  。

    c.  按一下 [繫結]  。

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >請務必也在 [驗證虛擬伺服器設定] 頁面上按一下 [完成]  。

3. 確認變更。 瀏覽至應用程式 URL。 您應該會看到租用戶登入頁面，而不是先前未驗證的存取。

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-header-based-authentication"></a>設定 Citrix NetScaler 單一登入以進行以標頭為基礎的驗證

### <a name="citrix-adc-configuration"></a>Citrix ADC 設定

### <a name="create-an-rewrite-action"></a>建立重寫動作

1. 移至 [AppExpert] > [重寫] > [重寫動作]  。
 
    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/header01.png)

2.  按一下 [新增]  。

    a.  指定 [名稱]  。

    b.  類型 = **INSERT_HTTP_HEADER**。

    c.  指定 [標頭名稱]  (在此範例中為 SecretID)。

    d.  指定運算式 **aaa.USER.ATTRIBUTE(`mySecretID`)** ，其中 **mySecretID** 是傳送至 Citrix ADC 的 Azure AD SAML 宣告。

    e.  按一下頁面底部的 [新增]  。

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/header02.png)
 
### <a name="create-a-rewrite-policy"></a>建立重寫原則。

1.  移至 [AppExpert] > [重寫] > [重寫原則]  。
 
    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/header03.png)

2.  按一下 [新增]  。

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/header04.png)

    a.  指定 [名稱]  。

    b.  選擇先前建立的 [動作]  。

    c . 運算式指定 **true**。

    d.  按一下 [建立]  。

### <a name="bind-rewrite-policy-to-virtual-servers"></a>將重寫原則繫結至虛擬伺服器

1. 使用 GUI 將重寫原則繫結至特定虛擬伺服器。

2. 瀏覽至 [流量管理] > [負載平衡] > [虛擬伺服器]  。

3. 在 [虛擬伺服器] 的 [詳細資料] 窗格清單中，選取您要作為重寫原則繫結目標的 [虛擬伺服器]  ，然後按一下 [開啟]  。

4. 在 [設定虛擬伺服器 (負載平衡)] 對話方塊中，選取 [原則]  索引標籤。在 NetScaler 上設定的所有原則都會出現在清單上。
 
    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/header06.png)

5.  在您要作為此虛擬伺服器繫結目標的原則名稱旁邊，選取 [核取方塊]  。

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/header07.png)

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/header08.png)
 
6.  按一下 [確定]  。 狀態列中會出現一則訊息，指出原則的設定已成功。

### <a name="modify-saml-server-to-extract-attributes-from-claim"></a>修改 SAML 伺服器以從宣告中擷取屬性

1.  移至 [安全性] > [AAA - 應用程式流量] > [原則] > [驗證] > [進階原則] > [動作] > [伺服器]  。

2.  為應用程式選取適當的 [驗證 SAML 伺服器]  。
 
    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/header09.png)

3. 在 [屬性] 區段下，輸入您要使用 “ , “ 來擷取的 SAML 屬性。 在此案例中，我們會指定名為 **mySecretID** 的屬性。
 
    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/header10.png)

4. 請確認我的存取應用程式。

    ![Citrix NetScaler 設定](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-citrix-netscaler-test-user"></a>建立 Citrix NetScaler 測試使用者

本節會在 Citrix NetScaler 中建立名為 B.Simon 的使用者。 Citrix NetScaler 支援預設會啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Citrix NetScaler 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

> [!NOTE]
> 如果您需要手動建立使用者，則需要連絡 [Citrix NetScaler 用戶端支援小組](https://www.citrix.com/contact/technical-support.html)。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Citrix NetScaler] 圖格時，應該會自動登入您已設定 SSO 的 Citrix NetScaler。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Citrix NetScaler](https://aad.portal.azure.com/)

- [設定 Citrix NetScaler 單一登入以進行以 Kerberos 為基礎的驗證](citrix-netscaler-tutorial.md)
