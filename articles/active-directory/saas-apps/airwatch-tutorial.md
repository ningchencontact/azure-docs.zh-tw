---
title: 教學課程：Azure Active Directory 與 AirWatch 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 AirWatch 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 366ffa560cb16f6445eca93e79068fba6cc31e6d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813112"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>教學課程：Azure Active Directory 與 AirWatch 整合

在本教學課程中，您將了解如何整合 AirWatch 與 Azure Active Directory (Azure AD)。
AirWatch 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 AirWatch 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 AirWatch (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 AirWatch 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 AirWatch 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* AirWatch 支援 **SP** 起始的 SSO

## <a name="adding-airwatch-from-the-gallery"></a>從資源庫新增 AirWatch

若要設定 AirWatch 與 Azure AD 整合，您需要從資源庫將 AirWatch 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 AirWatch，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **AirWatch**，並從結果面板中選取 [AirWatch]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 AirWatch](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 AirWatch 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 AirWatch 中相關使用者之間的連結關聯性。

若要設定及測試與 AirWatch 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 AirWatch 單一登入](#configure-airwatch-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 AirWatch 測試使用者](#create-airwatch-test-user)** - 使 AirWatch 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 AirWatch 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/) 的 [AirWatch] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    ![AirWatch 網域與 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL：`https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. 在 [識別碼 (實體識別碼)] 文字方塊中，輸入如下的值：`AirWatch`

    > [!NOTE]
    > 這不是真正的值。 使用實際的「登入 URL」來更新此值。 請連絡 [AirWatch 用戶端支援小組](https://www.air-watch.com/company/contact-us/)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 AirWatch] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-airwatch-single-sign-on"></a>設定 AirWatch 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 AirWatch 公司網站。

2. 在左側導覽窗格中按一下 [帳戶]，然後按一下 [系統管理員]。

   ![系統管理員](./media/airwatch-tutorial/ic791920.png "系統管理員")

3. 展開 [設定] 功能表，然後按一下 [目錄服務]。

   ![設定](./media/airwatch-tutorial/ic791921.png "設定")

4. 按一下 [使用者] 索引標籤，在 [基準 DN] 文字方塊中輸入您的網域名稱，然後按一下 [儲存]。

   ![使用者](./media/airwatch-tutorial/ic791922.png "使用者")

5. 按一下 [伺服器]  索引標籤。

   ![伺服器](./media/airwatch-tutorial/ic791923.png "伺服器")

6. 執行下列步驟：

    ![上傳](./media/airwatch-tutorial/ic791924.png "上傳")   

    a. 針對 [目錄類型]，選取 [無]。

    b. 選取 [使用 SAML 進行驗證] 。

    c. 若要上傳已下載的憑證，請按一下 [上傳] 。

7. 在 [要求]  區段中，執行下列步驟：

    ![要求](./media/airwatch-tutorial/ic791925.png "要求")  

    a. 針對 [要求繫結類型]，選取 [POST]。

    b. 在 Azure 入口網站的 [設定在 Airwatch 單一登入] 對話頁面上，複製 [登入 URL] 值，然後將它貼到 [識別提供者單一登入 URL] 文字方塊中。

    c. 針對 [NameID 格式]，選取 [電子郵件地址]。

    d. 按一下 [檔案] 。

8. 再按一次 [使用者]  索引標籤。

    ![使用者](./media/airwatch-tutorial/ic791926.png "使用者")

9. 在 [屬性]  區段中，執行下列步驟：

    ![屬性](./media/airwatch-tutorial/ic791927.png "屬性")

    a. 在 [物件識別碼] 文字方塊中，輸入 `http://schemas.microsoft.com/identity/claims/objectidentifier`。

    b. 在 [使用者名稱] 文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    c. 在 [顯示名稱] 文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。

    d. 在 [名字] 文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。

    e. 在 [姓氏] 文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`。

    f. 在 [電子郵件] 文字方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    g. 按一下 [檔案] 。

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

在本節中，您會將 AirWatch 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [AirWatch]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [AirWatch]。

    ![應用程式清單中的 AirWatch 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-airwatch-test-user"></a>建立 AirWatch 測試使用者

若要讓 Azure AD 使用者能夠登入 AirWatch，必須將他們佈建到 AirWatch。 AirWatch 需以手動方式佈建。

**若要設定使用者佈建，請執行下列步驟：**

1. 以系統管理員身分登入您的 **AirWatch** 公司網站。

2. 在左側導覽窗格中按一下 [帳戶]，然後按一下 [使用者]。
  
   ![使用者](./media/airwatch-tutorial/ic791929.png "使用者")

3. 在 [使用者] 功能表中，按一下 [清單檢視]，然後按一下 [新增] \> [新增使用者]。
  
   ![新增使用者](./media/airwatch-tutorial/ic791930.png "新增使用者")

4. 在 [新增/編輯使用者]  對話方塊中，執行下列步驟：

   ![新增使用者](./media/airwatch-tutorial/ic791931.png "新增使用者")

   a. 在相關的文字方塊中，輸入您想要佈建之有效 Azure Active Directory 帳戶的 [使用者名稱]、[密碼]、[確認密碼]、[名字]、[姓氏]、[電子郵件地址]。

   b. 按一下 [檔案] 。

> [!NOTE]
> 您可以使用任何其他的 AirWatch 使用者帳戶建立工具或 AirWatch 提供的 API 來佈建 AAD 使用者帳戶。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [AirWatch] 圖格時，應該會自動登入您已設定 SSO 的 AirWatch。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)