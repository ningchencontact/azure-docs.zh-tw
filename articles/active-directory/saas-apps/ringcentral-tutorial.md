---
title: 教學課程：Azure Active Directory 與 RingCentral 的整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 RingCentral 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: 7cd20397c122834a23586bf51af6467f58531094
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997235"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>教學課程：Azure Active Directory 與 RingCentral 的整合

在本教學課程中，您會了解如何將 RingCentral 與 Azure Active Directory (Azure AD) 整合。
將 RingCentral 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 RingCentral 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 RingCentral (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 RingCentral 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 RingCentral 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* RingCentral 支援 **SP** 起始的 SSO

## <a name="adding-ringcentral-from-the-gallery"></a>從資源庫新增 RingCentral

若要設定將 RingCentral 整合至 Azure AD 中，您需要從資源庫將 RingCentral 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 RingCentral，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，鍵入 **RingCentral**，從結果面板選取 [RingCentral]，然後按一下 [新增] 按鈕新增應用程式。

    ![結果清單中的 RingCentral](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 RingCentral 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 RingCentral 中相關使用者之間的連結關聯性。

若要設定並測試與 RingCentral 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 RingCentral 單一登入](#configure-ringcentral-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 RingCentral 測試使用者](#create-ringcentral-test-user)** - 在 RingCentra 中建立對應到 Britta Simon 的使用者，這位使用者會連結到 Azure AD 中其所代表的使用者。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 RingCentral 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [RingCentral] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 如果您有**服務提供者中繼資料檔案**，請在 [基本 SAML 設定] 區段上執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]。

    ![上傳中繼資料檔案](common/upload-metadata.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]。

    ![選擇中繼資料檔案](common/browse-upload-metadata.png)

    c. 成功上傳中繼資料檔案後，就會在 [基本 SAML 設定] 區段中自動填入 [識別碼] 和 [回覆 URL] 值。

    ![RingCentral 網域與 URL 單一登入資訊](common/sp-identifier-reply.png)

    在 [登入 URL] 文字方塊中，輸入 URL：
    
    | |
    |--|
    | `https://service.ringcentral.com`|
    | `https://service.ringcentral.com.au`|
    | `https://service.ringcentral.co.uk`|
    | `https://service.ringcentral.eu`|

    > [!Note]
    > 您會在 RingCentral SSO 設定頁面上取得**服務提供者中繼資料檔案**，本教學課程稍後會予以說明。

5. 如果您沒有**服務提供者中繼資料檔案**，請執行下列步驟：

    a. 在 [登入 URL] 文字方塊中，鍵入 URL：

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. 在 [識別碼] 文字方塊中輸入 URL：

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. 在 [回覆 URL] 文字方塊中輸入 URL：

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![RingCentral 網域與 URL 單一登入資訊](common/sp-identifier-reply.png)

6. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]，並將其儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="configure-ringcentral-single-sign-on"></a>設定 RingCentral 單一登入

1. 在不同的 Web 瀏覽器視窗中，以安全性系統管理員身分登入 RingCentral。

1. 在頂端處按一下 [工具]。

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. 瀏覽至 [單一登入]。

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. 在 [單一登入] 頁面的 [SSO 設定] 區段下，從 [步驟 1] 按一下 [編輯] 並執行下列步驟：

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. 在 [安裝單一登入] 頁面上，執行下列步驟：

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. 按一下 [瀏覽] 以上傳您從 Azure 入口網站下載的中繼資料檔案。

    b. 上傳中繼資料後，會自動在 [SSO 一般資訊] 區段中填入這些值。

    c. 在 [屬性對應] 區段下，將 [電子郵件屬性對應目標] 選取為 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. 按一下 [檔案] 。

    e. 從 [步驟 2] 按一下 [下載] 以下載**服務提供者中繼資料檔案**，並在 Azure 入口網站中，上傳至 [基本 SAML 設定] 區段，以自動填入 [識別碼] 及 [回覆 URL] 值。

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. 在同一頁上，瀏覽至 [啟用 SSO] 區段，並執行下列步驟：

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * 選取 [啟用 SSO 服務]。

    * 選取 [允許使用者使用 SSO 或 RingCentral 認證登入]。

    * 按一下 [檔案] 。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 `brittasimon@yourcompanydomain.extension`  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 RingCentral 的存取權授與 Britta Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [RingCentral]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [RingCentral]。

    ![應用程式清單中的 RingCentral 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-ringcentral-test-user"></a>建立 RingCentral 測試使用者

在本節中，您會在 RingCentral 中建立名為 Britta Simon 的使用者。 請連絡  [RingCentral 用戶端支援小組](https://success.ringcentral.com/RCContactSupp) ，以在 RingCentral 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [RingCentral] 圖格時，應該會自動登入您已設定 SSO 的 RingCentral。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
