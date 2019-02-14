---
title: 教學課程：Azure Active Directory 與 Amazon Web Services (AWS) 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Amazon Web Services (AWS) 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e377cf749119c23d37bb4db8ab78abb1ce8c82ae
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199743"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>教學課程：Azure Active Directory 與 Amazon Web Services (AWS) 整合

在本教學課程中，您將了解如何整合 Amazon Web Services (AWS) 與 Azure Active Directory (Azure AD)。
Amazon Web Services (AWS) 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Amazon Web Services (AWS) 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Amazon Web Services (AWS) (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

您可以為多個執行個體設定多個識別碼，如下所示。

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

使用這些值，Azure AD 會移除 **#** 的值，並且傳送正確的值 `https://signin.aws.amazon.com/saml` 作為 SAML 權杖中的對象 URL。

**我們建議使用此方法，原因如下：**

a. 每個應用程式都會提供您唯一的 X509 憑證，因此每個執行個體都可以有不同的憑證到期日，而您可以依據個別的 AWS 帳戶來加以管理。 在此情況下，整體憑證變換就變得很容易。

b. 您可以在 Azure AD 中使用 AWS 應用程式來啟用使用者佈建，我們的服務便會從該 AWS 帳戶擷取所有角色。 您不需要手動新增或更新應用程式上的 AWS 角色。

c. 您可以對應用程式指派個別的應用程式擁有者，讓該擁有者可以直接在 Azure AD 中管理應用程式。

> [!Note]
> 請確定您僅使用資源庫應用程式

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Amazon Web Services (AWS) 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Amazon Web Services (AWS) 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

> [!Note]
> 如果您想將多個 AWS 帳戶整合至一個 Azure 帳戶，以使用單一登入，請參閱[本文](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Amazon Web Services (AWS) 支援由 **SP 和 IDP** 起始的 SSO

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>從資源庫新增 Amazon Web Services (AWS)

若要設定 Amazon Web Services (AWS) 與 Azure AD 整合，您需要從資源庫將 Amazon Web Services (AWS) 新增到受控 SaaS App 清單。

**若要從資源庫新增 Amazon Web Services (AWS)，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，鍵入 **Amazon Web Services (AWS)**，並從結果面板中選取 [Amazon Web Services (AWS)]，然後按一下 [新增] 按鈕新增應用程式。

     ![結果清單中的 Amazon Web Services (AWS)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Amazon Web Services (AWS) 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Amazon Web Services (AWS) 中相關使用者之間的連結關聯性。

若要使用 Amazon Web Services (AWS) 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Amazon Web Services (AWS) 單一登入](#configure-amazon-web-services-aws-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Amazon Web Services (AWS) 測試使用者](#create-amazon-web-services-aws-test-user)** - 使 Amazon Web Services (AWS) 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Amazon Web Services (AWS) 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Amazon Web Services (AWS)] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 區段中，使用者不需要執行任何步驟，因為應用程式已預先與 Azure 整合。

    ![映像](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. 若要設定多個執行個體，請提供識別碼值。 從第二個執行個體開始，請提供下列格式的識別碼值。 請使用 **#** 符號以指定唯一的 SPN 值。

    `https://signin.aws.amazon.com/saml#2`

    ![Amazon Web Services (AWS) 網域和 URL 單一登入資訊](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. Amazon Web Services (AWS) 應用程式會預期要有特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性] 區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [使用者屬性] 對話方塊。

    ![映像](common/edit-attribute.png)

7. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    | 名稱  | 來源屬性  | 命名空間 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 角色            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | 「提供 900 秒 (15 分鐘) 到 43200 秒 (12 小時) 之間的值」 |  https://aws.amazon.com/SAML/Attributes |

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 在 [命名空間] 文字方塊中，輸入該資料列顯示的命名空間值。

    d. 選取 [來源] 作為 [屬性]。

    e. 在 [來源屬性] 清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]。

    g. 按一下 [檔案] 。

8. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載] 以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

9. 在 [設定 Amazon Web Services (AWS)] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>設定 Amazon Web Services (AWS) 單一登入

1. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 Amazon Web Services (AWS) 公司網站。

2. 按一下 [AWS Home] \(AWS 首頁\)。

    ![設定單一登入首頁][11]

3. 按一下 [身分識別與存取管理] 。

    ![設定單一登入身分識別][12]

4. 按一下 [識別提供者]，然後按一下 [建立提供者]。

    ![設定單一登入提供者][13]

5. 在 [設定提供者]  對話方塊頁面上，執行下列步驟：

    ![設定單一登入對話方塊][14]

    a. 針對 [提供者類型]，選取 [SAML]。

    b. 在 [提供者名稱] 文字方塊中，輸入提供者名稱 (例如：*WAAD*)。

    c. 若要上傳從 Azure 入口網站下載的**中繼資料檔案**，請按一下 [選擇檔案]。

    d. 按一下頁面底部的 [新增] 。

6. 在 [驗證提供者資訊] 對話方塊頁面上，按一下 [建立]。

    ![設定單一登入驗證][15]

7. 按一下 [Roles] \(角色\)，然後按一下 [Create role] \(建立角色\)。

    ![設定單一登入角色][16]

8. 在 [Create role] \(建立角色\) 頁面上，執行下列步驟：  

    ![設定單一登入信任][19]

    a. 在 [Select type of trusted entity] \(選取信任的實體類型\) 底下，選取 [SAML 2.0 federation] \(SAML 2.0 同盟\)。

    b. 在 [選擇 SAML 2.0 提供者] 區段下方，選取您先前建立的 [SAML 提供者] (例如：*WAAD*)

    c. 選取 [Allow programmatic and AWS Management Console access] \(允許透過程式設計方式和 AWS 管理主控台存取\)。
  
    d. 按一下 [下一步:權限]**。

9. 在 [附加權限原則] 對話方塊中，請根據您的組織需求附加適當的原則。 按一下 下一步:**。  

    ![設定單一登入原則][33]

10. 在 [檢閱]  對話方塊上，執行下列步驟：

    ![設定單一登入檢閱][34]

    a. 在 [Role name] \(角色名稱\) 文字方塊中，輸入您的角色名稱。

    b. 在 [Role description] \(文字描述\) 文字方塊中，輸入描述。

    c. 按一下 [建立角色]。

    d. 建立所需數量的角色，並將它們對應至識別提供者。

11. 使用 AWS 服務帳戶認證，以透過 Azure AD 使用者佈建中的 AWS 帳戶來擷取角色。 若要這麼做，請開啟 AWS 主控台首頁。

12. 按一下 [服務] -> [Security,Identity& Compliance (安全性、身分識別和合規性)] -> [IAM]。

    ![從 AWS 帳戶擷取角色](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. 選取 IAM 區段中的 [原則] 索引標籤。

    ![從 AWS 帳戶擷取角色](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. 按一下 [Create policy] \(建立原則\) 來建立新原則，以從「Azure AD 使用者佈建」中的 AWS 帳戶擷取角色。

    ![建立新的原則](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. 建立您自己的原則，以從 AWS 帳戶擷取所有角色，方法是執行下列步驟：

    ![建立新的原則](./media/amazon-web-service-tutorial/policy1.png)

    a. 在 [建立原則] 區段中按一下 [JSON] 索引標籤。

    b. 在原則文件中，新增下列 JSON。

    ```

    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",

    "Action": [

    "iam:ListRoles"

    ],

    "Resource": "*"

    }

    ]

    }

    ```

    c. 按一下 [檢閱原則] 按鈕以驗證原則。

    ![定義新的原則](./media/amazon-web-service-tutorial/policy5.png)

16. 執行下列步驟，以定義**新原則**：

    ![定義新的原則](./media/amazon-web-service-tutorial/policy2.png)

    a. 將 [原則名稱] 提供為 **AzureAD_SSOUserRole_Policy**。

    b. 您可以將**描述**提供給原則，因為**此原則將允許從 AWS 帳戶擷取角色**。

    c. 按一下 [建立原則] 按鈕。

17. 執行下列步驟，以在 AWS IAM 服務中建立新的使用者帳戶：

    a. 按一下 AWS IAM 主控台中的 [使用者] 導覽。

    ![定義新的原則](./media/amazon-web-service-tutorial/policy3.png)

    b. 按一下 [新增使用者] 按鈕，以建立新的使用者。

    ![新增使用者](./media/amazon-web-service-tutorial/policy4.png)

    c. 在 [新增使用者] 區段中，執行下列步驟：

    ![新增使用者](./media/amazon-web-service-tutorial/adduser1.png)

    * 將使用者名稱輸入為 **AzureADRoleManager**。

    * 在 [存取類型] 中，選取 [以程式設計方式存取] 選項。 因此，使用者可以叫用 API，以及從 AWS 帳戶擷取角色。

    * 按一下右下角的 [Next Permissions] (接下來的權限) 按鈕。

18. 現在，執行下列步驟，以建立此使用者的新原則：

    ![新增使用者](./media/amazon-web-service-tutorial/adduser2.png)

    a. 按一下 [Attach existing policies directly] (直接連接現有原則) 按鈕。

    b. 在篩選區段 **AzureAD_SSOUserRole_Policy** 中，搜尋新建立的原則。

    c. 選取 [原則]，然後按 [下一步：**檢閱]** 按鈕。

19. 執行下列步驟，以檢閱所連接使用者的原則：

    ![新增使用者](./media/amazon-web-service-tutorial/adduser3.png)

    a. 檢閱使用者名稱、存取類型，以及對應至使用者的原則。

    b. 按一下右下角的 [建立使用者] 按鈕，以建立使用者。

20. 執行下列步驟，以下載使用者的使用者認證：

    ![新增使用者](./media/amazon-web-service-tutorial/adduser4.png)

    a. 複製使用者**存取金鑰識別碼**和**祕密存取金鑰**。

    b. 在 Azure AD 使用者佈建區段中輸入這些認證，以從 AWS 主控台擷取角色。

    c. 按一下底部的 [關閉] 按鈕。

21. 巡覽至 Azure AD 管理入口網站中 Amazon Web Services 應用程式的 [使用者佈建] 區段。

    ![新增使用者](./media/amazon-web-service-tutorial/provisioning.png)

22. 在 [用戶端祕密] 和 [祕密權杖] 欄位中，分別輸入 [存取金鑰] 和 [祕密]。

    ![新增使用者](./media/amazon-web-service-tutorial/provisioning1.png)

    a. 在 **clientsecret** 欄位中，輸入 AWS 使用者存取金鑰。

    b. 在 [祕密權杖] 欄位中，輸入 AWS 使用者祕密。

    c. 按一下 [測試連線] 按鈕，您應該可以成功測試此連線。

    d. 按一下頂端的 [儲存] 按鈕，以儲存設定。

23. 現在，請確定您開啟開關，然後按一下頂端的 [儲存] 按鈕，以在 [設定] 區段中 [開啟] [佈建狀態]。

    ![新增使用者](./media/amazon-web-service-tutorial/provisioning2.png)

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

在本節中，您會將 Amazon Web Services (AWS) 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Amazon Web Services (AWS)]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入 [Amazon Web Services (AWS)] 並加以選取。

    ![[應用程式] 清單中的 Amazon Web Services (AWS) 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-amazon-web-services-aws-test-user"></a>建立 Amazon Web Services (AWS) 測試使用者

本節目標是在 Amazon Web Services (AWS) 中建立名為 Britta Simon 的使用者。 Amazon Web Services (AWS) 不需要在其系統中針對 SSO 建立使用者，因此您不需要在這裡執行任何動作。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Amazon Web Services (AWS)] 圖格時，應該會自動登入您已設定 SSO 的 Amazon Web Services (AWS)。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="known-issues"></a>已知問題

 * 在 [佈建] 區段中，[對應] 子區段會顯示「正在載入...」訊息且永遠不會顯示屬性對應。 目前支援的唯一佈建工作流程是在使用者/群組指派期間，將角色從 AWS 匯入 Azure AD 以供選取。 此屬性對應是預先決定的，無法加以設定。
 
 * [佈建] 區段只支援一次輸入一個 AWS 租用戶的一組認證。 所有匯入的角色都會針對 AWS 租用戶寫入 Azure AD [servicePrincipal 物件](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)的 appRoles 屬性。 您可以將多個 AWS 租用戶 (以 servicePrincipal 表示) 從資源庫新增到 Azure AD 以供佈建，不過有一個已知的問題：無法將所有匯入的角色從用於佈建的多個 AWS servicePrincipal 自動寫入到用於單一登入的單一 servicePrincipal。 因應措施如下：[Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) 可用來擷取所有匯入每個 AWS servicePrincipal (已設定佈建) 的 appRoles。 這些角色字串隨後可以新增至已設定單一登入的 AWS servicePrincipal。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
