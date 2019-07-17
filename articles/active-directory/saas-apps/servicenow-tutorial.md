---
title: 教學課程：Azure Active Directory 與 ServiceNow 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 ServiceNow 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba516aa2c3d2decaa4962f1ccd0394ebe9a4a62
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706117"
---
# <a name="tutorial-integrate-servicenow-with-azure-active-directory"></a>教學課程：整合 ServiceNow 與 Azure Active Directory

在本教學課程中，您將了解如何整合 ServiceNow 與 Azure Active Directory (Azure AD)。 在整合 ServiceNow 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 ServiceNow 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 ServiceNow。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 ServiceNow 單一登入 (SSO) 的訂用帳戶。
* 若為 ServiceNow，ServiceNow 的執行個體或租用戶 (Calgary 版本或更新版本)
* 若為 ServiceNow Express，ServiceNow Express 的執行個體 (Helsinki 版本或更新版本)
* ServiceNow 租用戶必須啟用[多個提供者單一登入外掛程式](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)。 [提交服務要求](https://hi.service-now.com)即可達到此目的。
* 若要進行自動設定，請為 ServiceNow 啟用多重提供者外掛程式。
* 若要安裝所需的 ServiceNow Classic (行動) 應用程式，請移至適當的存放區並搜尋 ServiceNow Classic 應用程式，然後按一下 [下載]。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 ServiceNow 支援由 **SP** 起始的 SSO，並支援[**自動**使用者佈建](servicenow-provisioning-tutorial.md)。

ServiceNow Classic (行動) 應用程式現已設定了搭配 Azure AD 啟用 SSO 的功能，並且可同時支援 **Android** 和 **IOS** 使用者。 在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

## <a name="adding-servicenow-from-the-gallery"></a>從資源庫新增 ServiceNow

若要設定 ServiceNow 與 Azure AD 整合，您需要從資源庫將 ServiceNow 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **ServiceNow**。
1. 從結果面板中選取 [ServiceNow]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 ServiceNow 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 ServiceNow 中相關使用者之間的連結關聯性。

若要設定及測試與 ServiceNow 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
2. **[設定 ServiceNow](#configure-servicenow)** 以在應用程式端設定 SSO 設定。
3. **[針對 ServiceNow Express 設定 Azure AD SSO](#configure-azure-ad-sso-for-servicenow-express)** - 讓您的使用者能夠使用此功能。
4. **[設定 ServiceNow Express SSO](#configure-servicenow-express-sso)** - 在應用程式端設定單一登入設定。
5. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B.Simon 測試 Azure AD 單一登入。
6. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
7. **[建立 ServiceNow 測試使用者](#create-servicenow-test-user)** ，使 ServiceNow 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
8. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。
9. **[測試 ServiceNow Classic (行動) 的 SSO](#test-sso-for-servicenow-classic-mobile)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [ServiceNow]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<instance-name>.service-now.com/navpage.do`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<instance-name>.service-now.com`

    > [!NOTE]
    > 這些都不是真正的值。 您必須使用實際的「登入 URL」及「識別碼」來更新這些值 (本教學課程稍後會說明)。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  以下載憑證並將其儲存在電腦上。

   ![憑證下載連結](common/certificatebase64.png)

   a. 按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 Url]  ，並將它貼到記事本，因為本教學課程稍後會使用這個應用程式同盟中繼資料 Url。

    b. 按一下 [下載]  以下載 [憑證 (Base64)]  ，然後將憑證檔案儲存在您的電腦上。

1. 在 [設定 ServiceNow]  區段上，依據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-servicenow"></a>設定 ServiceNow

1. 以系統管理員身分登入您的 ServiceNow 應用程式。

2. 遵循下一個步驟，啟動 [整合 - 多個提供者單一登入安裝程式]  外掛程式︰

    a. 在左側導覽窗格中，從搜尋列搜尋 [系統定義]  區段，然後按一下 [外掛程式]  。

    ![啟動外掛程式](./media/servicenow-tutorial/tutorial_servicenow_03.png "啟動外掛程式")

    b. 搜尋**整合 - 多個提供者單一登入安裝程式**。

     ![啟動外掛程式](./media/servicenow-tutorial/tutorial_servicenow_04.png "啟動外掛程式")

    c. 選取外掛程式。 按一下滑鼠右鍵並選取 [啟動/升級]  。

     ![啟動外掛程式](./media/servicenow-tutorial/tutorial_activate.png "啟動外掛程式")

    d. 按一下 [啟用]  按鈕。

     ![啟動外掛程式](./media/servicenow-tutorial/tutorial_activate1.png "啟動外掛程式")

3. 在左側導覽窗格中，從搜尋列搜尋 [多重提供者 SSO]  區段，然後按一下 [屬性]  。

    ![設定應用程式 URL](./media/servicenow-tutorial/tutorial_servicenow_06.png "設定應用程式 URL")

4. 在 [多個提供者 SSO 內容]  對話方塊上，執行下列步驟：

    ![設定應用程式 URL](./media/servicenow-tutorial/ic7694981.png "設定應用程式 URL")

    * 針對 [啟用多個提供者 SSO]  ，選取 [是]  。
  
    * 針對 [啟用從所有識別提供者將使用者自動匯入使用者資料表]  ，選取 [是]  。

    * 針對 [啟用偵錯記錄以供多個提供者 SSO 整合]  ，選取 [是]  。

    * 在 [使用者資料表上的欄位...]  文字方塊中，輸入 **user_name**。
  
    * 按一下 [檔案]  。

5. 有兩種方式可設定 **ServiceNow** - 自動和手動。

6. 若要自動設定 **ServiceNow**，請依照以下步驟進行操作：

    * 返回 Azure 入口網站中的 [ServiceNow]  單一登入頁面。

    * 我們提供 ServiceNow 單鍵設定服務，也就是讓 Azure AD 自動設定 ServiceNow，以便進行 SAML 型驗證。 若要啟用這項服務，請移至 [ServiceNow 設定]  區段，按一下 [設定 ServiceNow]  以開啟 [設定單一登入] 視窗。

        ![設定單一登入](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * 在 [設定單一登入]  表單中輸入 ServiceNow 執行個體名稱、管理員使用者名稱和管理員密碼，然後按一下 [立即設定]  。 請注意，提供的系統管理員使用者名稱必須在 ServiceNow 中指派 **security_admin** 角色，才能運作。 否則，若要手動設定 ServiceNow 以使用 Azure AD 作為「SAML 識別提供者」，請按一下 [手動設定單一登入]  ，然後從 [快速參考] 區段複製 [登出 URL]、[SAML 實體識別碼] 及 [SAML 單一登入服務 URL]  。

        ![設定應用程式 URL](./media/servicenow-tutorial/configure.png "設定應用程式 URL")

    * 以系統管理員身分登入您的 ServiceNow 應用程式。

    * 在自動設定中，所有的必要設定都會在 **ServiceNow** 端上設定，但依預設不會啟用 [X.509 憑證]  。 您必須以手動方式將其對應至您在 ServiceNow 中的「識別提供者」。 請依照以下步驟進行相同操作：

    * 在左側瀏覽窗格中，從搜尋列搜尋 [Multi-Provider SSO] \(多重提供者 SSO\)  區段，然後按一下 [Identity Providers] \(識別提供者\)  。

        ![設定單一登入](./media/servicenow-tutorial/tutorial_servicenow_07.png "設定單一登入")

    * 按一下自動產生的「識別提供者」

        ![設定單一登入](./media/servicenow-tutorial/tutorial_servicenow_08.png "設定單一登入")

    *  在 [識別提供者]  區段上，執行下列步驟：

        ![設定單一登入](./media/servicenow-tutorial/automatic_config.png "設定單一登入")

        * 在 [名稱]  文字方塊中，輸入您的設定名稱 (例如 **Microsoft Azure Federated Single Sign-On**)。

        * 請從文字方塊中移除已填入的 [Identity Provider's SingleLogoutRequest] \(識別提供者的 SingleLogoutRequest\)  值。

        * 複製 [ServiceNow 首頁]  值，然後將其貼到 Azure 入口網站上的 [ServiceNow 基本 SAML 設定]  區段的 [單一登入 URL]  文字方塊中。

            > [!NOTE]
            > 將您的 [ServieNow 租用戶 URL]  與 **/navpage.do** 串連即是 ServiceNow 執行個體首頁(例如：`https://fabrikam.service-now.com/navpage.do`)。

        * 複製 [實體識別碼/簽發者]  值，然後將其貼到 Azure 入口網站上的 [ServiceNow 基本 SAML 設定]  區段的 [識別碼]  文字方塊中。

        * 請確定 [NameID Policy] \(NameID 原則\)  是設定為 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 值。 

    * 向下捲動至 [X.509 Certificate] \(X.509 憑證\)  區段，選取 [Edit] \(編輯\)  。

        ![設定單一登入](./media/servicenow-tutorial/tutorial_servicenow_09.png "設定單一登入")

    * 選取憑證，然後按一下向右箭號圖示，以新增憑證

        ![設定單一登入](./media/servicenow-tutorial/tutorial_servicenow_11.png "設定單一登入")

    * 按一下 [檔案]  。

    * 按一下頁面右上角的 [Test Connection] \(測試連線\)  。

        ![啟動外掛程式](./media/servicenow-tutorial/tutorial_activate2.png "啟動外掛程式")

    * 按一下 [Test Connection] \(測試連線\)  之後，將會顯示需要您輸入認證的快顯示窗，然後會顯示以下含有結果的頁面。 預期會有 [SSO Logout Test Results] \(SSO 登出測試結果\)  錯誤，請忽略該錯誤，然後按一下 [Activate] \(啟用\)  按鈕。

        ![設定單一登入](./media/servicenow-tutorial/servicenowactivate.png "設定單一登入")
  
7. 若要手動設定 **ServiceNow**，請依照以下步驟進行操作：

    * 以系統管理員身分登入您的 ServiceNow 應用程式。

    * 在左側的導覽窗格中，按一下 [識別提供者]  。

        ![設定單一登入](./media/servicenow-tutorial/tutorial_servicenow_07.png "設定單一登入")

    * 在 [識別提供者]  對話方塊中，按一下 [新增]  。

        ![設定單一登入](./media/servicenow-tutorial/ic7694977.png "設定單一登入")

    * 在 [Identity Providers] \(識別提供者\)  對話方塊上，按一下 [SAML]  。

        ![設定單一登入](./media/servicenow-tutorial/ic7694978.png "設定單一登入")

    * 在 [Import Identity Provider Metadata] \(匯入識別提供者中繼資料\)  快顯視窗上，執行下列步驟：

        ![設定單一登入](./media/servicenow-tutorial/idp.png "設定單一登入")

        * 輸入您從 Azure 入口網站複製的 [應用程式同盟中繼資料 URL]  。

        * 按一下 [匯入]  。

    * 這樣會讀取 IdP 中繼資料 URL 並填入所有欄位資訊。

        ![設定單一登入](./media/servicenow-tutorial/ic7694982.png "設定單一登入")

        * 在 [名稱]  文字方塊中，輸入您的設定名稱 (例如 **Microsoft Azure Federated Single Sign-On**)。

        * 請從文字方塊中移除已填入的 [Identity Provider's SingleLogoutRequest] \(識別提供者的 SingleLogoutRequest\)  值。

        * 複製 [ServiceNow 首頁]  值，然後將其貼到 Azure 入口網站上的 [ServiceNow 基本 SAML 設定]  區段的 [單一登入 URL]  文字方塊中。

            > [!NOTE]
            > 將您的 [ServieNow 租用戶 URL]  與 **/navpage.do** 串連即是 ServiceNow 執行個體首頁(例如：`https://fabrikam.service-now.com/navpage.do`)。

        * 複製 [實體識別碼/簽發者]  值，然後將其貼到 Azure 入口網站上的 [ServiceNow 基本 SAML 設定]  區段的 [識別碼]  文字方塊中。

        * 請確定 [NameID Policy] \(NameID 原則\)  是設定為 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 值。

        * 按一下 [進階]  。 在 [使用者欄位]  文字方塊中，輸入 **email** 或 **user_name**，這取決於會用哪一個欄位來唯一識別 ServiceNow 部署中的使用者。

            > [!NOTE]
            > 移至 Azure 入口網站的 [ServiceNow] > [屬性] > [單一登入]  區段，並將所要的欄位對應至 [nameidentifier]  屬性，即可設定 Azure AD 以發出 Azure AD 使用者識別碼 (使用者主體名稱) 或電子郵件地址做為 SAML 權杖中的唯一識別碼。 所選屬性儲存在 Azure AD 中的值 (例如使用者主體名稱) 必須符合所輸入欄位 (例如 user_name) 儲存在 ServiceNow 中的值

        * 按一下頁面右上角的 [Test Connection] \(測試連線\)  。

        * 按一下 [Test Connection] \(測試連線\)  之後，將會顯示需要您輸入認證的快顯示窗，然後會顯示以下含有結果的頁面。 預期會有 [SSO Logout Test Results] \(SSO 登出測試結果\)  錯誤，請忽略該錯誤，然後按一下 [Activate] \(啟用\)  按鈕。

          ![設定單一登入](./media/servicenow-tutorial/servicenowactivate.png "設定單一登入")

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>為 ServiceNow Express 設定 Azure AD SSO

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [ServiceNow]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<instance-name>.service-now.com/navpage.do`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<instance-name>.service-now.com`

    > [!NOTE]
    > 這些都不是真正的值。 您必須使用實際的「登入 URL」及「識別碼」來更新這些值 (本教學課程稍後會說明)。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 我們提供 ServiceNow 單鍵設定服務，也就是讓 Azure AD 自動設定 ServiceNow，以便進行 SAML 型驗證。 若要啟用此服務，請移至 [安裝 ServiceNow]  區段，按一下 [檢視逐步指示]  以開啟 [設定登入] 視窗。

    ![設定單一登入](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. 在 [設定單一登入]  表單中輸入 ServiceNow 執行個體名稱、管理員使用者名稱和管理員密碼，然後按一下 [立即設定]  。 請注意，提供的系統管理員使用者名稱必須在 ServiceNow 中指派 **security_admin** 角色，才能運作。 否則，若要手動設定 ServiceNow 以使用 Azure AD 作為「SAML 識別提供者」，請按一下 [手動設定單一登入]  ，然後從 [快速參考] 區段複製 [登出 URL]、[Azure AD 識別碼] 及 [登入 URL]  。

    ![設定應用程式 URL](./media/servicenow-tutorial/configure.png "設定應用程式 URL")

### <a name="configure-servicenow-express-sso"></a>設定 ServiceNow Express SSO

1. 以系統管理員身分登入您的 ServiceNow Express 應用程式。

2. 在左側導覽窗格中按一下 [單一登入]  。

    ![設定應用程式 URL](./media/servicenow-tutorial/ic7694980ex.png "設定應用程式 URL")

3. 在 [單一登入]  對話方塊中，按一下右上方的 [設定] 圖示，然後設定下列屬性︰

    ![設定應用程式 URL](./media/servicenow-tutorial/ic7694981ex.png "設定應用程式 URL")

    a. 將 [啟用多個提供者 SSO]  切換到右邊。

    b. 將 [啟用偵錯記錄以供多個提供者 SSO 整合]  切換到右邊。

    c. 在 [使用者資料表上的欄位...]  文字方塊中，輸入 **user_name**。

4. 在 [單一登入]  對話方塊上，按一下 [新增憑證]  。

    ![設定單一登入](./media/servicenow-tutorial/ic7694973ex.png "設定單一登入")

5. 在 [X.509 憑證]  對話方塊中，執行下列步驟：

    ![設定單一登入](./media/servicenow-tutorial/ic7694975.png "設定單一登入")

    a. 在 [名稱]  文字方塊中，輸入您的組態名稱 (例如：**TestSAML2.0**)。

    b. 選取 [使用中]  。

    c. 針對 [格式]  ，選取 [PEM]  。

    d. 針對 [類型]  ，選取 [信任存放區憑證]  。

    e. 在記事本中開啟您從 Azure 入口網站下載的 Base64 編碼憑證，將其內容複製到剪貼簿，然後貼到 [PEM 憑證]  文字方塊。

    f. 按一下 [更新] 

6. 在 [單一登入]  對話方塊上，按一下 [新增 IdP]  。

    ![設定單一登入](./media/servicenow-tutorial/ic7694976ex.png "設定單一登入")

7. 在 [新增識別提供者]  對話方塊的 [設定識別提供者]  之下，執行下列步驟：

    ![設定單一登入](./media/servicenow-tutorial/ic7694982ex.png "設定單一登入")

    a. 在 [名稱]  文字方塊中，輸入您的組態名稱 (例如：**SAML 2.0**)。

    b. 在 [識別提供者 URL]  欄位中，貼上您從 Azure 入口網站複製的 [識別提供者識別碼]  值。

    c. 在 [識別提供者的 AuthnRequest]  欄位中，貼上您從 Azure 入口網站複製的 [驗證要求 URL]  值。

    d. 在 [識別提供者的 SingleLogoutRequest]  欄位中，貼上您從 Azure 入口網站複製的 [登出 URL]  值

    e. 針對 [識別提供者憑證]  ，選取您在上一個步驟中建立的憑證。

8. 按一下 [進階設定]  ，然後在 [其他識別提供者屬性]  之下，執行下列步驟︰

    ![設定單一登入](./media/servicenow-tutorial/ic7694983ex.png "設定單一登入")

    a. 在 [IDP 的 SingleLogoutRequest 通訊協定繫結]  文字方塊中，輸入 **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**。

    b. 在 [名稱識別碼原則]  文字方塊中，輸入 **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**。

    c. 在 **AuthnContextClassRef 方法**中，輸入 `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`。

    d. 取消選取 [建立 AuthnContextClass]  。

9. 在 [其他服務提供者屬性]  之下，執行下列步驟︰

    ![設定單一登入](./media/servicenow-tutorial/ic7694984ex.png "設定單一登入")

    a. 在 [ServiceNow 首頁]  文字方塊中，輸入您的 ServiceNow 執行個體首頁的 URL。

    > [!NOTE]
    > 將您的 [ServieNow 租用戶 URL]  與 **/navpage.do** 串連即是 ServiceNow 執行個體首頁 (例如：`https://fabrikam.service-now.com/navpage.do`)。

    b. 在 [對象識別碼/核發者]  文字方塊中，輸入您的 ServiceNow 租用戶 URL。

    c. 在 [對象 URI]  文字方塊中，輸入您的 ServiceNow 租用戶 URL。

    d. 在 [時鐘誤差]  文字方塊中，輸入 **60**。

    e. 在 [使用者欄位]  文字方塊中，輸入 **email** 或 **user_name**，這取決於會用哪一個欄位來唯一識別 ServiceNow 部署中的使用者。

    > [!NOTE]
    > 移至 Azure 入口網站的 [ServiceNow] > [屬性] > [單一登入]  區段，並將所要的欄位對應至 [nameidentifier]  屬性，即可設定 Azure AD 以發出 Azure AD 使用者識別碼 (使用者主體名稱) 或電子郵件地址做為 SAML 權杖中的唯一識別碼。 所選屬性儲存在 Azure AD 中的值 (例如使用者主體名稱) 必須符合所輸入欄位 (例如 user_name) 儲存在 ServiceNow 中的值

    f. 按一下 [檔案]  。

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

在本節中，您會將 ServiceNow 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [ServiceNow]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-servicenow-test-user"></a>建立 ServiceNow 測試使用者

本節的目標是在 ServiceNow 中建立名為 Britta Simon 的使用者。 ServiceNow 支援自動使用者佈建，該功能預設為啟用。 您可以在[這裡](servicenow-provisioning-tutorial.md)找到關於如何設定自動使用者佈建的更多詳細資料。

> [!NOTE]
> 如果您需要手動建立使用者，請連絡 [ServiceNow 用戶端支援小組](https://www.servicenow.com/support/contact-support.html)

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [ServiceNow] 圖格時，應該會自動登入您已設定 SSO 的 ServiceNow。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="test-sso-for-servicenow-classic-mobile"></a>測試 ServiceNow Classic (行動) 的 SSO

1. 開啟您的 **ServiceNow Classic (行動)** 應用程式，並執行下列步驟：

    a. 按一下畫面下方的 [新增]  符號。

    ![登入](./media/servicenow-tutorial/test03.png)

    b. 輸入您的 ServiceNow 執行個體名稱，然後按一下 [繼續]  。

    ![登入](./media/servicenow-tutorial/test04.png)

    c. 在 [登入]  畫面上，執行下列步驟：

    ![登入](./media/servicenow-tutorial/test01.png)

    *  輸入 [使用者名稱]  ，例如 B.simon@contoso.com。

    *  按一下 [使用外部登入]  ，您將會重新導向至 Azure AD 頁面進行登入。
    
    *  輸入您的認證，此時，若有任何第三方驗證或已啟用任何其他安全性功能，使用者將必須據以回應，且應用程式的**首頁**將會顯示如下：

        ![首頁](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [設定使用者佈建](servicenow-provisioning-tutorial.md)