---
title: 教學課程：Azure Active Directory 與 SAP Business Object Cloud 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAP Business Object Cloud 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: ae2a28dea5fe240bbf216e8ec92f29c186c92255
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825335"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>教學課程：Azure Active Directory 與 SAP Business Object Cloud 整合

在本教學課程中，您將了解如何整合 SAP Business Object Cloud 與 Azure Active Directory (Azure AD)。
SAP Business Object Cloud 與 Azure AD 整合有下列優點：

* 您可以在 Azure AD 中控制可存取 SAP Business Object Cloud 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 SAP Business Object Cloud (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要進行 Azure AD 與 SAP Business Object Cloud 整合的設定，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 SAP Business Object Cloud 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* SAP Business Object Cloud 支援 **SP** 起始的 SSO

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>從資源庫新增 SAP Business Object Cloud

若要進行 SAP Business Object Cloud 與 Azure AD 整合的設定，您需要從資源庫將 SAP Business Object Cloud 新增至受控 SaaS 應用程式清單中。

**若要從資源庫新增 SAP Business Object Cloud，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **SAP Business Object Cloud**，從結果面板中選取 [SAP Business Object Cloud]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 SAP Business Object Cloud](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您將以名為 **Britta Simon** 的測試使用者身分，設定及測試與 SAP Business Object Cloud 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 SAP Business Object Cloud 中相關使用者之間的連結關聯性。

若要設定及測試與 SAP Business Object Cloud 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 SAP Business Object Cloud 單一登入](#configure-sap-business-object-cloud-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 SAP Business Object Cloud 測試使用者](#create-sap-business-object-cloud-test-user)** - 使 SAP Business Object Cloud 中 Britta Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定 SAP Business Object Cloud 的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SAP Business Object Cloud] 應用程式整合分頁上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    ![SAP Business Object Cloud 網域及 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL：
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. 在 [識別碼 (實體識別碼)] 文字方塊中，使用下列模式輸入 URL：
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > 這些 URL 中的值僅供示範。 使用實際的「登入 URL」及「識別碼 URL」來更新這些值。 若要取得登入 URL，請連絡 [SAP Business Object Cloud 用戶端支援小組](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/)。 您可以從系統管理員主控台下載 SAP Business Object Cloud 中繼資料，以取得識別項 URL。 稍後在本教學課程中會加以說明。

4. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>設定 SAP Business Object Cloud 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 SAP Business Object Cloud 公司網站。

2. 選取 [功能表] > [系統] > [管理]。
    
    ![依序選取 [功能表]、[系統] 和 [管理]](./media/sapboc-tutorial/config1.png)

3. 在 [安全性] 索引標籤上，選取 [編輯] \(畫筆) 圖示。
    
    ![在 [安全性] 索引標籤上，選取 [編輯] 圖示](./media/sapboc-tutorial/config2.png)  

4. 選取 [SAML 單一登入 (SSO)] 作為 [驗證方法]。

    ![選取 [SAML 單一登入] 作為驗證方法](./media/sapboc-tutorial/config3.png)  

5. 選取 [下載]，以下載服務提供者中繼資料 (步驟 1)。 在中繼資料檔案中，尋找並複製 **entityID** 值。 在 Azure 入口網站的 [基本 SAML 設定] 對話方塊上，將值貼入 [識別碼] 方塊。

    ![複製並貼上 entityID 值](./media/sapboc-tutorial/config4.png)  

6. 若要在您從 Azure 入口網站下載的檔案中上傳服務提供者中繼資料 (步驟 2)**，請在 [上傳識別提供者中繼資料]** 下，選取 [上傳]。  

    ![在上傳識別提供者中繼資料下，選取 [上傳]](./media/sapboc-tutorial/config5.png)

7. 在 [使用者屬性值] 清單中，選取您需要用於實作的使用者屬性 (步驟 3)。 此使用者屬性會對應到識別提供者。 若要在使用者的頁面上輸入自訂屬性，請使用 [自訂 SAML 對應] 選項。 或者，您可以選取 [電子郵件] 或 [使用者識別碼]作為使用者屬性。 在本範例中，我們選取 [電子郵件]，因為我們在 Azure 入口網站的 [使用者屬性和宣告] 區段中，使用 **userprincipalname** 屬性來對應使用者識別項宣告。 這會提供唯一的使用者電子郵件，並傳送給每個成功 SAML 回應中的 SAP Business Object Cloud 應用程式。

    ![選取使用者屬性](./media/sapboc-tutorial/config6.png)

8. 若要使用識別提供者 (步驟 4) 來驗證帳戶，請在 [登入認證 (電子郵件)] 方塊中，輸入使用者的電子郵件地址。 然後，選取 [驗證帳戶]。 系統會將登入認證新增至使用者帳戶。

    ![輸入電子郵件，並選取 [驗證帳戶]](./media/sapboc-tutorial/config7.png)

9. 選取 [儲存] 圖示。

    ![[儲存] 圖示](./media/sapboc-tutorial/save.png)

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

在本節中，您將把 SAP Business Object Cloud 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [SAP Business Object Cloud]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [SAP Business Object Cloud]。

    ![應用程式清單中的 SAP Business Object Cloud 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-sap-business-object-cloud-test-user"></a>建立 SAP Business Object Cloud 測試使用者

必須在 SAP Business Object Cloud 中佈建 Azure AD 使用者之後，他們才能登入 SAP Business Object Cloud。 在 SAP Business Object Cloud 中，必須以手動方式進行佈建。

若要佈建使用者帳戶：

1. 以系統管理員身分登入您的 SAP Business Object Cloud 公司網站。

2. 選取 [功能表] > [安全性] > [使用者]。

    ![新增員工](./media/sapboc-tutorial/user1.png)

3. 若要在 [使用者] 頁面上新增新的使用者詳細資料，請選取 [+]。 

    ![新增使用者頁面](./media/sapboc-tutorial/user4.png)

    然後完成下列步驟：

    a. 在 [使用者識別碼] 方塊中，輸入使用者的使用者識別碼，例如 **Britta**。

    b. 在 [名字] 方塊中，輸入使用者的名字，例如 **Britta**。

    c. 在 [姓氏] 方塊中，輸入使用者的姓氏，例如 **Simon**。

    d. 在 [顯示名稱] 方塊中，輸入使用者的全名，例如 **Britta Simon**。

    e. 在 [電子郵件] 方塊中，輸入使用者的電子郵件地址，例如 **brittasimon@contoso.com**。

    f. 在 [選取角色] 頁面上，選取適當的使用者角色，然後選取 [確定]。

      ![選取角色](./media/sapboc-tutorial/user3.png)

    g. 選取 [儲存] 圖示。    

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [SAP Business Object Cloud] 圖格時，應該會自動登入您設定 SSO 的 SAP Business Object Cloud。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

