---
title: 教學課程：Azure Active Directory 與 Citrix Netscaler 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Citrix Netscaler 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 6d434295a6a46ee5b7089608cbf788ff91589fb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "65863770"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>教學課程：Azure Active Directory 與 Citrix Netscaler 整合

在本教學課程中，您會了解如何整合 Citrix Netscaler 與 Azure Active Directory (Azure AD)。
將 Citrix Netscaler 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 Citrix Netscaler 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Citrix Netscaler (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Citrix Netscaler 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Citrix Netscaler 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Citrix Netscaler 支援由 **SP** 起始的 SSO

* Citrix Netscaler 支援 **Just In Time** 使用者佈建

## <a name="adding-citrix-netscaler-from-the-gallery"></a>從資源庫新增 Citrix Netscaler

若要設定將 Citrix Netscaler 整合到 Azure AD 中，您必須從資源庫將 Citrix Netscaler 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Citrix Netscaler，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Citrix Netscaler**，從結果面板中選取 [Citrix Netscaler]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 Citrix Netscaler](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Citrix Netscaler 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Citrix Netscaler 中相關使用者之間的連結關聯性。

若要設定及測試與 Citrix Netscaler 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Citrix Netscaler 單一登入](#configure-citrix-netscaler-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Citrix Netscaler 測試使用者](#create-citrix-netscaler-test-user)** - 在 Citrix Netscaler 中建立一個與 Azure AD 中代表 Britta Simon 之使用者連結的 Britta Simon 對應項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Citrix Netscaler 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/) 的 [Citrix Netscaler] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    ![Citrix Netscaler 網域及 URL 單一登入資訊](common/sp-identifier-reply.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL：`https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. 在 [識別碼 (實體識別碼)] 文字方塊中，使用下列模式輸入 URL：`https://<<Your FQDN>>`

    c. 在 [回覆 URL (判斷提示取用者服務 URL)] 文字方塊中，以下列模式輸入 URL：`https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Citrix Netscaler 用戶端支援小組](https://www.citrix.com/contact/technical-support.html)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

    > [!NOTE]
    > 為了讓 SSO 能夠運作，應該要能夠從公用網站存取這些 URL。 您必須在 Netscaler 端啟用防火牆或其他安全性設定，才能讓 Azure AD 在所設定的 ACS URL 上公佈權杖。

5. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [安裝 Citrix Netscaler] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-citrix-netscaler-single-sign-on"></a>設定 Citrix Netscaler 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Citrix Netscaler 租用戶。

2. 確定 **NetScaler 韌體版本 = NS12.1:Build 48.13.nc**。

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure01.png)

3. 在 [VPN Virtual Server] \(VPN 虛擬伺服器\) 頁面上，執行下列步驟：

     ![設定單一登入](./media/citrix-netscaler-tutorial/configure02.png)

    a. 將閘道設定 [ICA Only] \(僅限 ICA\) 設定為 **true**。
    
    b. 將 [Enable Authentication] \(啟用驗證\) 設定為 **true**。
    
    c. [DTLS] 為選擇性。
    
    d. 確定 [SSLv3] 為 [Disabled] \(已停用\)。

4. 如下所示，會建立一個自訂 [SSL Ciphers] \(SSL 加密\) 群組，以在 https://www.ssllabs.com 取得 A+：

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure03.png)

5. 在 [Configure Authentication SAML Server] \(設定驗證 SAML 伺服器\) 頁面上，執行下列步驟：

      ![設定單一登入](./media/citrix-netscaler-tutorial/configure04.png)

    a. 在 [Name] \(名稱\) 文字方塊中，輸入您伺服器的名稱。

    b. 在 [Redirect URL] \(重新導向 URL\) 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    c. 在 [Single Logout URL] \(單一登出 URL\) 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。

    d. 在 [IDP Certificate Name] \(IDP 憑證名稱\) 中，按一下 [+] 號以新增您從 Azure 入口網站下載的憑證。 上傳憑證之後，請從下拉式清單中選取該憑證。

    e. 以下是需要在此頁面上設定的其他欄位

      ![設定單一登入](./media/citrix-netscaler-tutorial/configure24.png)

    f. 在 [Requested Authentication Context] \(要求的驗證內容\) 選取 [Exact] \(完全符合\)。

    g. 在 [Signature Algorithm] \(簽章演算法\) 選取 [RSA-SHA256]。

    h. 在 [Digest Method] \(摘要方法\) 選取 [SHA256]。

    i. 勾選 [Enforce Username] \(強制使用使用者名稱\)。

    j. 按一下 [檔案] &gt; [新增] &gt; [專案] 

6. 若要設定 [Session Profile] \(工作階段設定檔\)，請執行下列步驟：

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure06.png)

    a. 在 [Name] \(名稱\) 文字方塊中，輸入您工作階段設定檔的名稱。

    b. 在 [Client Experience] \(用戶端體驗\) 索引標籤上進行變更，如以下螢幕擷取畫面所示。

    c. 繼續在 [General] \(一般\) 索引標籤上進行變更 (如下所示)，然後按一下 [OK] \(確定\)

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure07.png)

    d. 在 [Published Applications] \(已發佈的應用程式\) 索引標籤上進行變更 (如以下螢幕擷取畫面所示)，然後按一下 [OK] \(確定\)。

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure08.png)

    e. 在 [Security] \(安全性\) 索引標籤上進行變更 (如以下螢幕擷取畫面所示)，然後按一下 [OK] \(確定\)。

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure09.png)

7. 將 [ICA Connections] \(ICA 連線\) 設定為在「工作階段可靠性連接埠」**2598** 上進行連線，如以下螢幕擷取畫面所示。

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure10.png)

8. 在 [SAML] 區段上，新增 [Servers] \(伺服器\)，如以下螢幕擷取畫面所示。

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure11.png)

9. 在 [SAML] 區段上，新增 [Policies] \(原則\)，如以下螢幕擷取畫面所示。

     ![設定單一登入](./media/citrix-netscaler-tutorial/configure12.png)

10. 在 [Global Settings] \(全域設定\) 頁面上，移至 [Clientless Access] \(無用戶端存取\) 區段。

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure13.png)

11. 在 [Configuration] \(設定\) 索引標籤上，執行下列步驟：

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure14.png)

    a. 選取 [Allow Domains] \(允許網域\)。

    b. 在 [Domain Name] \(網域名稱\) 文字方塊中，選取網域。

    c. 按一下 [確定]。

12. 進行 **StoreFront** 的 [Receiver for Web Sites] \(網站的接收端\) 相關設定，如以下螢幕擷取畫面所示：

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure15.png)

13. 在 [Manage Authentication Methods - Corp] \(管理驗證方法 - 公司\) 快顯視窗上，執行下列步驟：

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure16.png)

    a. 選取「使用者名稱和密碼」。

    b. 選取 [Pass-through from NetScaler Gateway] \(從 NetScaler 閘道傳遞\)。

    c. 按一下 [確定]。

14. 在 [Configure Trusted Domains] \(設定受信任的網域\) 快顯視窗上，執行下列步驟：

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure17.png)

    a. 選取 [Trusted domains only] \(僅限受信任的網域\)。

    b. 按一下 [Add] \(新增\)，以在 [Trusted domains] \(受信任的網域\) 文字方塊中新增您的網域。

    c. 從 [Default domain] \(預設網域\) 清單中選取預設網域。

    d. 選取 [Show domains list in logon page] \(在登入頁面上顯示網域清單\)。

    e. 按一下 [確定]。

15. 在 [Manage NetScaler Gateways] \(管理 NetScaler 閘道\) 快顯視窗上，執行下列步驟：

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure18.png)

    a. 按一下 [Add] \(新增\)，以在 [NetScaler Gateways] \(NetScaler 閘道\) 文字方塊中新增您的 NetScaler 閘道。

    b. 按一下 [關閉] 。

16. 在 StoreFront 的 [General Settings] \(一般設定\) 索引標籤上，執行下列步驟：

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure19.png)

    a. 在 [Display name] \(顯示名稱\) 文字方塊中，輸入您的 NetScaler 閘道名稱。

    b. 在 [NetScaler Gateway URL] \(NetScaler 閘道 URL\) 文字方塊中，輸入您的 NetScaler 閘道 URL。

    c. 在 [Usage or role] \(使用方式或角色\) 選取 [Authentication and HDX routing] \(驗證和 HDX 路由\)。

    d. 按一下 [確定]。

17. 在 StoreFront 的 [Secure Ticket Authority] \(安全票證授權單位\) 索引標籤上，執行下列步驟：

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure20.png)

    a. 按一下 [Add] \(新增\) 按鈕，以在文字方塊中新增您的 [Secure Ticket Authority URL] \(安全票證授權單位 URL\)。

    b. 選取 [Enable session reliability] \(啟用工作階段可靠性\)。

    c. 按一下 [確定]。

18. 在 StoreFront 的 [Authentication Settings] \(驗證設定\) 索引標籤上，執行下列步驟：

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure21.png)

    a. 選取您的 [Version] \(版本\)。

    b. 在 [Logon type] \(登入類型\) 選取 [Domain] \(網域\)。

    c. 輸入您的 [Callback URL] \(回呼 URL\)。

    d. 按一下 [確定]。

19. 在 StoreFront 的 [Deploy Citrix Receiver] \(部署 Citrix Receiver\) 索引標籤上，執行下列步驟：

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure22.png)

    a. 在 [Deployment option] \(部署選項\) 選取 [Use Receiver for HTML5 if local Receiver is unavailable] \(如果無法使用本機接收端，則使用 HTML5 的接收端\)。

    b. 按一下 [確定]。

20. 在 [Manage Beacons] \(管理指標\) 快顯視窗上，執行下列步驟：

    ![設定單一登入](./media/citrix-netscaler-tutorial/configure23.png)

    a. 在 [Internal beacon] \(內部指標\) 選取 [Use the service URL] \(使用服務 URL\)。

    b. 按一下 [Add] \(新增\)，以在 [External beacons] \(外部指標\) 文字方塊中新增您的 URL。

    c. 按一下 [確定]。

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

在本節中，您會將 Citrix Netscaler 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Citrix Netscaler]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Citrix Netscaler]。

    ![應用程式清單中的 [Citrix Netscaler] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-citrix-netscaler-test-user"></a>建立 Citrix Netscaler 測試使用者

本節會在 Citrix Netscaler 中建立名為 Britta Simon 的使用者。 Citrix Netscaler 支援預設會啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Citrix Netscaler 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

>[!NOTE]
>如果您需要手動建立使用者，則需要連絡 [Citrix Netscaler 用戶端支援小組](https://www.citrix.com/contact/technical-support.html)。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Citrix Netscaler] 圖格時，應該會自動登入您已設定 SSO 的 Citrix Netscaler。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

