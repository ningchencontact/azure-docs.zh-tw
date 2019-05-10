---
title: 教學課程：Azure Active Directory 與 Alibaba Cloud Service (角色型 SSO) 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Alibaba Cloud Service (角色型 SSO) 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8de2f7cb90e004673c59282a8023d55df364220a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65140720"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>教學課程：Azure Active Directory 與 Alibaba Cloud Service (角色型 SSO) 整合

在本教學課程中，您將了解如何整合 Alibaba Cloud Service (角色型 SSO) 與 Azure Active Directory (Azure AD)。
將 Alibaba Cloud Service (角色型 SSO) 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 CAlibaba Cloud Service (角色型 SSO) 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Alibaba Cloud Service (角色型 SSO) (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Alibaba Cloud Service (角色型 SSO) 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 Alibaba Cloud Service (角色型 SSO) 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Alibaba Cloud Service (角色型 SSO) 支援由 **IDP** 起始的 SSO

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>從資源庫新增 Alibaba Cloud Service (角色型 SSO)

若要設定將 Alibaba Cloud Service (角色型 SSO) 整合到 Azure AD 中，您必須將 Alibaba Cloud Service (角色型 SSO) 從資源庫新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Alibaba Cloud Service (角色型 SSO)，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Alibaba Cloud Service (角色型 SSO)**，並從結果面板中選取 [Alibaba Cloud Service (角色型 SSO)]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Alibaba Cloud Service (角色型 SSO)](common/search-new-app.png)

5. 在 [Alibaba Cloud Service (角色型 SSO)] 頁面上，按一下左側瀏覽窗格中的 [屬性]，然後複製**物件識別碼**並將其儲存在電腦上以供後續使用。

    ![屬性設定](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)
    
## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Alibaba Cloud Service (角色型 SSO) 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Alibaba Cloud Service (角色型 SSO) 中相關使用者之間的連結關聯性。

若要設定及測試與 Alibaba Cloud Service (角色型 SSO) 搭配運作的 Azure AD 單一登入，您必須完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[在 Alibaba Cloud Service 中設定角色型單一登入](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** - 讓您的使用者能夠使用此功能。
2. **[設定 Alibaba Cloud Service (角色型 SSO) 單一登入](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Alibaba Cloud Service (角色型 SSO) 測試使用者](#create-alibaba-cloud-service-role-based-sso-test-user)**：使 Alibaba Cloud Service (角色型 SSO) 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Alibaba Cloud Service (角色型 SSO) 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/) 的 [Alibaba Cloud Service (角色型 SSO)] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 如果您有**服務提供者中繼資料檔案**，請在 [基本 SAML 設定] 區段上執行下列步驟：

    >[!NOTE]
    >您會從這個 [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml) 取得服務提供者中繼資料

    a. 按一下 [上傳中繼資料檔案]。

    ![image](common/upload-metadata.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]。

    ![image](common/browse-upload-metadata.png)

    c. 當中繼資料檔案成功上傳後，會自動在 [Alibaba Cloud Service (角色型 SSO)] 區段文字方塊中填入 [識別碼] 及 [回覆 URL] 值：

    ![image](common/idp-intiated.png)

    > [!Note]
    > 如果 [識別碼] 和 [回覆 URL] 值未自動填入，則請根據您的需求手動填入這些值。

5. Alibaba Cloud Service (角色型 SSO) 應用程式會預期要有特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增到您的 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯] 圖示以開啟 [使用者屬性] 對話方塊。

    ![image](common/edit-attribute.png)

6. 除了以上屬性之外，Alibaba Cloud Service (角色型 SSO) 應用程式還預期 SAML 回應中會再多傳回幾個屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | Name | 命名空間 | 來源屬性|
    | ---------------| ------------| --------------- |
    | 角色 | https://www.aliyun.com/SAML-Role/Attribute | user.assignedroles |
    | RoleSessionName | https://www.aliyun.com/SAML-Role/Attribute | user.userprincipalname |

    > [!NOTE]
    > 請按一下[這裡](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)，以了解如何在 Azure AD 中設定**角色**

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間] 保持空白。

    d. 選取 [來源] 作為 [屬性]。

    e. 在 [來源屬性] 清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [檔案] 。

7. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載] 以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

8. 在 [設定 Alibaba Cloud Service (角色型 SSO)] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>在 Alibaba Cloud Service 中設定角色型單一登入

1. 使用 Account1 登入 Alibaba Cloud [RAM 主控台](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9)。

2. 在左側瀏覽窗格中，選取 [SSO]。

3. 在 [角色型 SSO] 索引標籤上，按一下 [建立 IdP]。

4. 在顯示的頁面上，於 [IdP 名稱] 欄位中輸入 `AAD`，於 [附註] 欄位中輸入描述，按一下 [上傳] 來上傳您之前下載的同盟中繼資料檔案，然後按一下 [確定]。

5. 成功建立 IdP 後，按一下 [建立 RAM 角色]。

6. 在 [RAM 角色名稱] 欄位中輸入 `AADrole`，從 [選取 IdP] 下拉式清單中選取 `AAD`，然後按一下 [確定]。

    >[!NOTE]
    >您可以視需要對角色授與權限。 在建立 IdP 和對應的角色之後，建議您儲存 IdP 和角色的 ARN 以供後續使用。 您可以在 [IdP 資訊] 頁面和 [角色資訊] 頁面上取得 ARN。

7. 將 Alibaba Cloud RAM 角色 (AADrole) 與 Azure AD 使用者 (u2) 建立關聯：若要將 RAM 角色與 Azure AD 使用者建立關聯，您必須依照下列步驟在 Azure AD 中建立角色：

    a. 登入 [Azure AD Graph 總管](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9)。

    b. 按一下 [修改權限] 以取得用於建立角色的必要權限。

    ![圖表設定](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. 從清單中選取下列權限，然後按一下 [修改權限]，如下圖所示。

    ![圖表設定](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >授與權限之後，再次登入 Graph 總管。

    d. 在 [Graph 總管] 頁面上，選取第一個下拉式清單中的 [GET] 和第二個下拉式清單中的 [beta]。 然後在下拉式清單旁邊的欄位中輸入 `https://graph.microsoft.com/beta/servicePrincipals`，並按一下 [執行查詢]。

    ![圖表設定](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >如果您要使用多個目錄，則可以在查詢的欄位中輸入 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`。

    e. 在 [回應預覽] 區段中，從 [服務主體] 擷取 appRoles 屬性以供後續使用。

    ![圖表設定](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >您可以在查詢的欄位中輸入 `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` 來找到 appRoles 屬性。 請注意，`objectID` 是您從 Azure AD 的 [屬性] 頁面所複製的物件識別碼。

    f. 返回 [Graph 總管]，將方法從 **GET** 變更為 **PATCH**，將下列內容貼到 [要求本文] 區段，然後按一下 [執行查詢]：
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > `value` 是您在 RAM 主控台中所建立 IdP 和角色的 ARN。 在這裡，您可以視需要新增多個角色。 Azure AD 會在 SAML 回應中以宣告值的形式傳送這些角色的值。 但是，您只能在修補作業的 `msiam_access` 部分之後新增角色。 若要讓建立程序變得順利，建議您使用識別碼產生器 (例如 GUID 產生器) 來即時產生識別碼。

    g. [服務主體] 在使用必要角色進行修補之後，請遵循教學課程中**指派 Azure AD 測試使用者**一節的步驟將角色連結至 Azure AD 使用者 (u2)。

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>設定 Alibaba Cloud Service (角色型 SSO) 單一登入

若要在 **Alibaba Cloud Service (角色型 SSO)** 端設定單一登入，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML] 和所複製的適當 URL 傳送給 [Alibaba Cloud Service (角色型 SSO) 支援小組](https://www.aliyun.com/service/)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 `brittasimon@yourcompanydomain.extension`。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Alibaba Cloud Service (角色型 SSO) 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Alibaba Cloud Service (角色型 SSO)]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Alibaba Cloud Service (角色型 SSO)]。

    ![應用程式清單中的 Alibaba Cloud Service (角色型 SSO) 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 索引標籤上，選取使用者清單中的 u2，然後按一下 [選取]。 然後，按一下 [指派]。

    ![測試設定](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

6. 檢視指派的角色並測試 Alibaba Cloud Service (角色型 SSO)。

    ![測試設定](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >在指派使用者 (u2) 後，所建立的角色會自動連結至使用者。 如果您已建立多個角色，則必須視需要將適當的角色連結至使用者。 如果您想要將角色型 SSO 從 Azure AD 實作到多個 Alibaba 雲端帳戶，請重複上述步驟。

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>建立 Alibaba Cloud Service (角色型 SSO) 測試使用者

在本節中，您會在 Alibaba Cloud Service (角色型 SSO) 中建立名為 Britta Simon 的使用者。 請與 [Alibaba Cloud Service (角色型 SSO) 支援小組](https://www.aliyun.com/service/)合作，在 Alibaba Cloud Service (角色型 SSO) 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-single-sign-on"></a>測試單一登入 

完成上述設定後，請遵循下列步驟來測試 Alibaba Cloud Service (角色型 SSO)：

1. 在 Azure 入口網站中，移至 [Alibaba Cloud Service (角色型 SSO)] 頁面、選取 [單一登入]，然後按一下 [測試]。

    ![測試設定](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. 按一下 [以目前使用者身分登入]。

    ![測試設定](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. 在 [帳戶選取] 頁面中，選取 [u2]。

    ![測試設定](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. 隨即會顯示下列頁面，指出角色型 SSO 已成功。

    ![測試設定](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

