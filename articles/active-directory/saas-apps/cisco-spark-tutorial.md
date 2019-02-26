---
title: 教學課程：Azure Active Directory 與 Cisco Webex 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Cisco Webex 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1460fba4d6897dfcc6bf40b6e02ab856ffe8456
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340463"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>教學課程：Azure Active Directory 與 Cisco Webex 整合

在本教學課程中，您將了解如何整合 Cisco Webex 與 Azure Active Directory (Azure AD)。
將 Cisco Webex 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Cisco Webex 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Cisco Webex (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Cisco Webex 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Cisco Webex 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Cisco Webex 支援 **SP** 起始的 SSO

## <a name="adding-cisco-webex-from-the-gallery"></a>從資源庫新增 Cisco Webex

若要設定將 Cisco Webex 整合到 Azure AD 中，您需要將 Cisco Webex 從資源庫新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Cisco Webex，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Cisco Webex**，從結果面板中選取 [Cisco Webex]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 Cisco Webex](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Cisco Webex 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Cisco Webex 中相關使用者之間的連結關聯性。

若要設定及測試與 Cisco Webex 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Cisco Webex 單一登入](#configure-cisco-webex-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Cisco Webex 測試使用者](#create-cisco-webex-test-user)** - 以在 Cisco Webex 中建立一個與 Azure AD 中代表 Britta Simon 之項目連結的對應項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Cisco Webex 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Cisco Webex] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    ![Cisco Webex 網域及 URL 單一登入資訊](common/sp-identifier.png)

    a. 在 [登入 URL] 文字方塊中，將 URL 輸入為：`https://web.ciscospark.com/#/signin`

    b. 在 [識別碼 (實體識別碼)] 文字方塊中，使用下列模式輸入 URL：`https://idbroker.webex.com/<Org Id>`

    > [!NOTE]
    > 此識別碼值不是實際值。 請使用實際的「識別碼」來更新此值。 如果您有服務提供者中繼資料，請將其上傳至 [基本 SAML 設定] 區段，然後 [識別碼 (實體識別碼)] 值就會自動填入。

5. Cisco Webex 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應加入 SAML 權杖屬性設定中。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯] ****  圖示以新增屬性。

    ![映像](common/edit-attribute.png)

6. 除了以上屬性外，Cisco Webex 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：
    
    | Name |  來源屬性|
    | ---------------|--------- |
    | UID | user.userprincipalname |

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間] 保持空白。

    d. 選取 [來源] 作為 [屬性]。

    e. 在 [來源屬性] 清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]。

    g. 按一下 [檔案] 。

7. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載] 以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

8. 在 [設定 Cisco Webex] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-cisco-webex-single-sign-on"></a>設定 Cisco Webex 單一登入

1. 使用完整系統管理員認證，登入 [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/)。

2. 選取 [設定]，然後按一下 [驗證] 區段下方的 [修改]。

    ![設定單一登入](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
  
3. 選取 [整合協力廠商識別提供者 (進階)]，然後移至下一個畫面。

4. 在 [匯入 Idp 中繼資料] 頁面上，將 Azure AD 中繼資料檔案拖放到頁面，或使用檔案瀏覽器選項來找到並上傳 Azure AD 中繼資料檔案。 然後，選取 [需要中繼資料中憑證授權單位所簽署的憑證 (較安全)]，然後按 [下一步]。

    ![設定單一登入](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

5. 選取 [測試 SSO 連接]，並在新的瀏覽器索引標籤開啟時，透過登入向 Azure AD 進行驗證。

6. 返回 [Cisco Cloud Collaboration Management] 瀏覽器索引標籤。如果測試成功，請選取 [這項測試成功。啟用單一登入] 選項，然後按 [下一步]。

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

在本節中，您會將 Cisco Webex 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Cisco Webex]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Cisco Webex]。

    ![應用程式清單中的 [Cisco Webex] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-cisco-webex-test-user"></a>建立 Cisco Webex 測試使用者

在本節中，您會在 Cisco Webex 中建立名為 Britta Simon 的使用者。 在本節中，您會在 Cisco Webex 中建立名為 Britta Simon 的使用者。

1. 使用完整系統管理員認證，移至 [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/)。

2. 按一下 [使用者]，然後按一下 [管理使用者]。
   
    ![設定單一登入](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. 在 [管理使用者] 視窗中，選取 [手動新增或修改使用者]，然後按 [下一步]。

4. 選取 [名稱和電子郵件地址]。 然後，如下填寫文字方塊︰

    ![設定單一登入](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. 在 [名字] 文字方塊中，輸入使用者的名字，例如 **Britta**。

    b. 在 [姓氏] 文字方塊中，輸入使用者的姓氏，例如 **Simon**。

    c. 在 [電子郵件地址] 文字方塊中，輸入像是 **britta.simon@contoso.com** 的使用者電子郵件地址。

5. 按一下加號以新增 Britta Simon。 然後按 [下一步] 。

6. 在 [新增使用者的服務] 視窗中，按一下 [儲存]，然後按一下 [完成]。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Cisco Webex 圖格時，應該會自動登入您設定 SSO 的 Cisco Webex。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
