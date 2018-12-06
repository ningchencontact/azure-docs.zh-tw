---
title: 教學課程：Azure Active Directory 與 SAP NetWeaver 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAP NetWeaver 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: fac22508e679c1e1c93ec62a5b120ba9c7c52317
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162328"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>教學課程：Azure Active Directory 與 SAP NetWeaver 整合

在本教學課程中，您會了解如何整合 SAP NetWeaver 與 Azure Active Directory (Azure AD)。

SAP NetWeaver 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 SAP NetWeaver 的人員。
- 您可以讓使用者使用其 Azure AD 帳戶自動登入 SAP NetWeaver (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 SAP NetWeaver 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 SAP NetWeaver 單一登入的訂用帳戶
- 至少需要 SAP NetWeaver V7.20

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。
本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 SAP NetWeaver
2. 設定並測試 Azure AD 單一登入

## <a name="adding-sap-netweaver-from-the-gallery"></a>從資源庫新增 SAP NetWeaver

若要設定 SAP NetWeaver 與 Azure AD 整合，您需要從資源庫將 SAP NetWeaver 新增到受控 SaaS 應用程式清單中。

**若要從資源庫新增 SAP NetWeaver，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **SAP NetWeaver**，從結果面板中選取 [SAP NetWeaver]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 SAP NetWeaver](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 SAP NetWeaver 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 SAP NetWeaver 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 SAP NetWeaver 中的相關使用者之間建立連結關聯性。

若要使用 SAP NetWeaver 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 SAP NetWeaver 測試使用者](#creating-sapnetweaver-test-user)** - 使 SAP NetWeaver 中 Britta Simon 的對應身分連結到該使用者在 Azure AD 中的代表身分。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，並在您的 SAP NetWeaver 應用程式中設定單一登入。

**若要使用 SAP NetWeaver 設定 Azure AD 單一登入，請執行下列步驟：**

1. 開啟新的 Web 瀏覽器視窗，以系統管理員身分登入您的 SAP NetWeaver 公司網站

2. 確定 **http** 和 **https** 服務為作用中，並已在 **SMICM** T-Code 中指派適當的連接埠。

3. 登入 SAP 系統 (T01) 的商務用戶端 (需要 SSO)，並啟用 HTTP 安全性工作階段管理。

    a. 移至交易代碼 **SICF_SESSIONS**。 它會顯示所有相關的設定檔參數以及目前的值。 如下所示：
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > 依照貴組織的需求來調整上述參數。這裡所提供的上述參數僅作為指示。

    b. 如果需要調整參數，在 SAP 系統的執行個體/預設設定檔中，重新啟動 SAP 系統。

    c. 在相關用戶端上按兩下，以啟用 HTTP 安全性工作階段。

    ![憑證下載連結](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. 啟用以下 SICF 服務：
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. 在 SAP 系統 [T01/122] 的商務用戶端中，移至交易代碼 **SAML2**。 它會在瀏覽器中開啟使用者介面。 在此範例中，我們假設 122 為 SAP 商務用戶端。

    ![憑證下載連結](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. 提供您的使用者名稱和密碼以在使用者介面中輸入，然後按一下 [編輯]。

    ![憑證下載連結](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. 從 [提供者名稱] 從 T01122 取代為 **http://T01122**，然後按一下 [儲存]。

    > [!NOTE]
    > 根據預設，提供者名稱來自 <sid><client> 格式，但 Azure AD 預期有 <protocol>://<name> 格式的名稱，並建議將提供者名稱保持為 https://<sid><client>，以允許在 Azure AD 中設定多個 SAP NetWeaver ABAP 引擎。

    ![憑證下載連結](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **產生服務提供者中繼資料**：在 SAML 2.0 使用者介面上設定好 [本機提供者] 和 [信任的提供者] 設定後，下一個步驟是產生服務提供者的中繼資料檔案 (其中包含 SAP 中的所有設定、驗證內容和其他組態)。 產生這個檔案後，我們就需要在 Azure AD 中上傳此檔案。

    ![憑證下載連結](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. 移至 [本機提供者] 索引標籤。

    b. 按一下 [中繼資料]。

    c. 在電腦上儲存所產生的 [中繼資料 XML 檔案]，然後在 [基本 SAML 設定] 區段中上傳該檔案，以在 Azure 入口網站中自動填入 [識別碼] 和 [回覆 URL] 值。

8. 在 Azure 入口網站的 [SAP NetWeaver] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

9. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![設定單一登入](common/tutorial_general_301.png)

10. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![設定單一登入](common/editconfigure.png)

11. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]，上傳您稍早取得的 [服務提供者中繼資料檔案]。

    ![上傳中繼資料檔案](common/editmetadataupload.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]。

    ![上傳中繼資料檔案](common/uploadmetadata.png)

    c. 當中繼資料檔案成功上傳後，會自動在 [基本 SAML 設定] 區段文字方塊中填入 [識別碼] 及 [回覆 URL] 值，如下所示：

    ![SAP NetWeaver 網域及 URL 單一登入資訊](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_url.png)

    d. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ `https://<your company instance of SAP NetWeaver>`

12. SAP NetWeaver 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性] 區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [使用者屬性] 對話方塊。

    ![屬性區段](./media/sapnetweaver-tutorial/edit_attribute.png)

13. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    a. 按一下 [編輯] 圖示以開啟 [管理使用者宣告] 對話方塊。
    
    ![屬性區段](./media/sapnetweaver-tutorial/nameidattribute.png)

    b. 在 [管理使用者宣告] 索引標籤上，執行下列步驟：

    ![屬性區段](./media/sapnetweaver-tutorial/nameidattribute1.png)

    * 選取 [轉換]。
  
    * 從 [轉換] 清單中選取 `ExtractMailPrefix()`。
  
    * 從 [參數 1] 清單中選取 `user.userprincipalname`。

    * 按一下 [檔案] 。

14. 在 [SAML 簽署憑證] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載] 以下載 [同盟中繼資料 XML]，然後將中繼資料檔案儲存在電腦上。

    ![憑證下載連結](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_certificate.png)

15. 在 [設定 SAP NetWeaver] 區段上，依據您的需求複製適當的 URL。

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

    ![SAP NetWeaver 組態](common/configuresection.png)

16. 登入 SAP 系統並移至交易程式碼 SAML2。 它會開啟包含 SAML 組態畫面的新瀏覽器視窗。

17. 若要設定信任的識別提供者 (Azure AD) 的端點，請移至 [信任的提供者] 索引標籤。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

18. 按下 [新增]，然後從操作功能表中選取 [上傳中繼資料檔案]。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

19. 上傳您從 Azure 入口網站下載的中繼資料檔案。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

20. 在下一個畫面中輸入別名名稱。 例如 aadsts，然後按 [下一步] 繼續。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

21. 確定 [摘要演算法] 應該是 **SHA-256**，而不需要任何變更，並且按 [下一步]。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

22. 在 [單一登入端點] 上，使用 [HTTP POST] 並且按 [下一步] 繼續。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

23. 在 [單一登入端點] 上，選取 [HTTPRedirect] 並且按 [下一步] 繼續。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

24. 在 [成品端點]，按 [下一步] 繼續。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

25. 在 [驗證需求] 上，按一下 [完成]。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

26. 移至 [信任的提供者] 索引標籤 > [識別身分同盟] (位於畫面底部)。 按一下 [編輯]。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

27. 按一下 [識別身分同盟] 索引標籤 (視窗底部) 之下的 [新增]。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

28. 在快顯視窗中，從 [支援的 NameID 格式] 中選取 [未指定]，然後按一下 [確定]。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

29. 請注意，[使用者識別碼來源] 和 [使用者識別碼對應模式] 值會決定 SAP 使用者與 Azure AD 宣告之間的連結。  

    ####<a name="scenario-sap-user-to-azure-ad-user-mapping"></a>案例：SAP 使用者與 Azure AD 使用者的對應。

    a. SAP 的 NameID 詳細資料螢幕擷取畫面。

    ![設定單一登入](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. 提及 Azure AD 必要宣告的螢幕擷取畫面。

    ![設定單一登入](./media/sapnetweaver-tutorial/claimsaad1.png)

    ####<a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>案例：根據在 SU01 中設定的電子郵件地址選取 SAP 使用者識別碼。 在此情況下，應針對每位需要 SSO 的使用者在 su01 中設定電子郵件識別碼。

    a.  SAP 的 NameID 詳細資料螢幕擷取畫面。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. 提及 Azure AD 必要宣告的螢幕擷取畫面。

    ![設定單一登入](./media/sapnetweaver-tutorial/claimsaad2.png)

30. 按一下 [儲存]，然後按一下 [啟用] 以啟用識別提供者。

    ![設定單一登入](./media/sapnetweaver-tutorial/configuration1.png)

31. 出現提示時，按一下 [確定]。

    ![設定單一登入](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![建立 Azure AD 使用者][100]

2. 在畫面頂端選取 [新增使用者]。

    ![建立 Azure AD 測試使用者](common/create_aaduser_01.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![建立 Azure AD 測試使用者](common/create_aaduser_02.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性] [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="creating-sap-netweaver-test-user"></a>建立 SAP NetWeaver 測試使用者

在本節中，您要在 SAP NetWeaver 中建立名為 Britta Simon 的使用者。 請與您內部的 SAP 專家小組合作，或與貴組織的 SAP 夥伴合作，在 SAP NetWeaver 平台中新增使用者。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 SAP NetWeaver 的存取權授與 ，讓 Britta Simon 能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![指派使用者][201]

2. 在應用程式清單中，選取 [SAP NetWeaver] 。

    ![設定單一登入](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="testing-single-sign-on"></a>測試單一登入

1. 啟用識別提供者 Azure AD 後，請嘗試存取下列 URL 來檢查 SSO (不會出現輸入使用者名稱和密碼的提示)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (或) 使用下列 URL

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > 以實際的 SAP 主機名稱取代 sapurl。

2. 上述 URL 應會帶您前往以下所述的畫面。 如果您能夠連到以下頁面，就表示 Azure AD SSO 成功完成設定。

    ![設定單一登入](./media/sapnetweaver-tutorial/testingsso.png)

3. 如果出現使用者名稱和密碼提示，請使用下面的 URL 來啟用追蹤，以診斷問題。

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
