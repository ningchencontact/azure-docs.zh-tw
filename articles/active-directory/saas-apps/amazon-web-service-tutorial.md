---
title: 教學課程：Azure Active Directory 與 Amazon Web Services (AWS) 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Amazon Web Services (AWS) 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f346c995cbc8be6e609020db799959d873ce89b3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944962"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>教學課程：整合 Amazon Web Services (AWS) 與 Azure Active Directory

在本教學課程中，您將了解如何整合 Amazon Web Services (AWS) 與 Azure Active Directory (Azure AD)。 在整合 AWS 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 AWS 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 AWS。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

![Azure AD 和 AWS 關聯性的圖表](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

您可以為多個執行個體設定多個識別碼。 例如︰

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

使用這些值，Azure AD 會移除 **#** 的值，並且傳送正確的值 `https://signin.aws.amazon.com/saml` 作為 SAML 權杖中的對象 URL。

我們建議使用此方法，原因如下：

- 每個應用程式都會提供唯一的 X509 憑證。 AWS 應用程式執行個體的每個執行個體則可以有不同的憑證到期日，並可依個別的 AWS 帳戶來管理。 在此情況下，整體憑證變換就會變得更容易。

- 您可以在 Azure AD 中使用 AWS 應用程式來啟用使用者佈建，我們的服務便會從該 AWS 帳戶擷取所有角色。 您不需要手動新增或更新應用程式上的 AWS 角色。

- 您可以為應用程式個別指派應用程式擁有者。 此人員可以直接在 Azure AD 中管理應用程式。

> [!Note]
> 請確定您只使用資源庫應用程式。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 AWS 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 AWS 支援 **SP 和 IDP** 起始的 SSO。

## <a name="add-aws-from-the-gallery"></a>從資源庫新增 AWS

若要設定將 AWS 整合到 Azure AD 中，您需要從資源庫將 AWS 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左窗格中選取 [Azure Active Directory]  服務。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Amazon Web Services (AWS)** 。
1. 從結果面板選取 [Amazon Web Services (AWS)]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 AWS 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 AWS 中相關使用者之間的連結關聯性。

若要設定及測試與 AWS 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **設定 Azure AD SSO**，讓您的使用者能夠使用此功能。
2. **設定 AWS** 以在應用程式端設定 SSO 設定。
3. **建立 Azure AD 測試使用者**，以 B.Simon 測試 Azure AD SSO。
4. **指派 Azure AD 測試使用者**，讓 B.Simon 能夠使用 Azure AD SSO。
5. **建立 AWS 測試使用者**，使 AWS 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **測試 SSO**，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Amazon Web Services (AWS)]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 設定]  的畫筆圖示，以編輯設定。

   ![醒目提示畫筆圖示的 [以 SAML 設定單一登入] 頁面螢幕擷取畫面](common/edit-urls.png)

4. 在 [基本 SAML 設定]  設定區段上，已預先設定好應用程式，並已經為 Azure 預先填入必要的 URL。 使用者必須選取 [儲存]  ，才能儲存組態。

5. 若要設定多個執行個體，請提供識別碼值。 從第二個執行個體開始，請使用下列格式，並包括 **#** 符號來指定唯一 SPN 值。

    `https://signin.aws.amazon.com/saml#2`

6. AWS 應用程式需要特定格式的 SAML 判斷提示，而且需要您加入對應到您 SAML 權杖屬性設定的自訂屬性。 以下螢幕擷取畫面顯示預設屬性清單。 選取畫筆圖示，以開啟 [使用者屬性] 對話方塊。

    ![醒目提示畫筆圖示的 [使用者屬性] 對話方塊螢幕擷取畫面](common/edit-attribute.png)

7. 除了以上屬性外，AWS 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，執行下列步驟來新增 SAML 權杖屬性。

    | Name  | 來源屬性  | 命名空間 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 角色            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | 「提供 900 秒 (15 分鐘) 到 43200 秒 (12 小時) 之間的值」 |  https://aws.amazon.com/SAML/Attributes |

    a. 選取 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![醒目提示 [新增宣告] 和 [儲存] 的 [使用者宣告] 區段螢幕擷取畫面](common/new-save-attribute.png)

    ![[管理使用者宣告] 對話方塊的螢幕擷取畫面](common/new-attribute-details.png)

    b. 在 [名稱]  中，輸入該資料列所顯示的屬性名稱。

    c. 在 [命名空間]  中，輸入該資料列顯示的命名空間值。

    d. 在 [來源]  中，選取 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 選取 [確定]  。

    g. 選取 [ **儲存**]。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找**同盟中繼資料 XML**。 選取 [下載]  以下載憑證，並將其儲存在您的電腦上。

   ![已醒目提示 [下載] 的 [SAML 簽署憑證] 區段螢幕擷取畫面](common/metadataxml.png)

1. 在 [設定 Amazon Web Services (AWS)]  區段上，依據您的需求複製適當的 URL。

   ![醒目提示設定 URL 的 [設定 Amazon Web Services (AWS)] 區段螢幕擷取畫面](common/copy-configuration-urls.png)

### <a name="configure-aws"></a>設定 AWS

1. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 AWS 公司網站。

2. 選取 [AWS 首頁]  。

    ![醒目提示 AWS 首頁圖示的 AWS 公司網站螢幕擷取畫面][11]

3. 選取 [Identity and Access Management] \(身分識別和存取管理\)  。

    ![醒目提示 IAM 的 AWS 服務頁面螢幕擷取畫面][12]

4. 選取 [識別提供者]   > [建立提供者]  。

    ![醒目提示 [識別提供者] 和 [建立提供者] 的 IAM 頁面螢幕擷取畫面][13]

5. 在 [設定提供者]  頁面上，執行下列步驟：

    ![設定提供者的螢幕擷取畫面][14]

    a. 針對 [Provider Type] \(提供者類型\)  ，選取 [SAML]  。

    b. 針對 [提供者名稱]  ，輸入提供者名稱 (例如：*WAAD*)。

    c. 若要上傳從 Azure 入口網站下載的**中繼資料檔案**，請選取 [Choose File] \(選擇檔案\)  。

    d. 選取 [Next Step] \(下一步\)  。

6. 在 [驗證提供者資訊]  對話方塊中，選取 [建立]  。

    ![醒目提示 [建立] 的驗證提供者資訊螢幕擷取畫面][15]

7. 選取 [角色]   > [建立角色]  。

    ![[角色] 頁面的螢幕擷取畫面][16]

8. 在 [Create role] \(建立角色\)  頁面上，執行下列步驟：  

    ![[建立角色] 頁面的螢幕擷取畫面][19]

    a. 在 [選取信任的實體類型]  底下，選取 [SAML 2.0 同盟]  。

    b. 在 [選擇 SAML 2.0 提供者]  下方，選取您先前建立的 [SAML 提供者]  (例如：*WAAD*)。

    c. 選取 [Allow programmatic and AWS Management Console access] \(允許透過程式設計方式和 AWS 管理主控台存取\)  。
  
    d. 完成時，選取 [下一步:**權限]** 。

9. 在 [附加權限原則]  對話方塊中，請根據您的組織需求附加適當的原則。 然後，選取 **[下一步：檢閱]** 。  

    ![[附加權限原則] 對話方塊的螢幕擷取畫面][33]

10. 在 [檢閱]  對話方塊上，執行下列步驟：

    ![[檢閱] 對話方塊的螢幕擷取畫面][34]

    a. 在 [角色名稱]  中，輸入您的角色名稱。

    b. 在 [角色描述]  文字方塊中，輸入描述。

    c. 選取 [建立角色]  。

    d. 建立所需數量的角色，並將它們對應至識別提供者。

11. 使用 AWS 服務帳戶認證，以透過 Azure AD 使用者佈建中的 AWS 帳戶來擷取角色。 若要這麼做，請開啟 AWS 主控台首頁。

12. 選取 [服務]  。 在 [安全性、身分識別與合規性]  底下，選取 [IAM]  。

    ![已醒目提示服務和 IAM 的 AWS 主控台首頁螢幕擷取畫面](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. 在 [IAM] 區段中，選取 [原則]  。

    ![醒目提示 [原則] 的 IAM 區段螢幕擷取畫面](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. 選取 [建立原則]  來建立新原則，以從「Azure AD 使用者佈建」中的 AWS 帳戶擷取角色。

    ![醒目提示 [建立原則] 的 [建立角色] 頁面螢幕擷取畫面](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. 建立您自己的原則，以從 AWS 帳戶擷取所有角色。

    ![醒目提示 [JSON] 的 [建立原則] 頁面螢幕擷取畫面](./media/amazon-web-service-tutorial/policy1.png)

    a. 在 [建立原則]  區段中，選取 [JSON]  索引標籤。

    b. 在原則文件中，新增下列 JSON：

    ```json
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

    c. 選取 [檢閱原則]  以驗證原則。

    ![[建立原則] 頁面的螢幕擷取畫面](./media/amazon-web-service-tutorial/policy5.png)

16. 定義新的原則。

    ![醒目提示名稱和描述欄位的 [建立原則] 頁面螢幕擷取畫面](./media/amazon-web-service-tutorial/policy2.png)

    a. 針對 [名稱]  ，輸入 **AzureAD_SSOUserRole_Policy**。

    b. 針對 [描述]  ，輸入**此原則將允許從 AWS 帳戶擷取角色**。

    c. 選取 [建立原則]  。

17. 在 AWS IAM 服務中建立新的使用者帳戶。

    a. 在 AWS IAM 主控台中，選取 [使用者]  。

    ![已醒目提示使用者的 AWS IAM 主控台螢幕擷取畫面](./media/amazon-web-service-tutorial/policy3.png)

    b. 若要建立新的使用者，請選取 [新增使用者]  。

    ![[新增使用者] 按鈕的螢幕擷取畫面](./media/amazon-web-service-tutorial/policy4.png)

    c. 在 [新增使用者]  區段中：

    ![醒目提示使用者名稱和存取類型的 [新增使用者] 頁面螢幕擷取畫面](./media/amazon-web-service-tutorial/adduser1.png)

    * 將使用者名稱輸入為 **AzureADRoleManager**。

    * 針對 [存取類型]，選取 [以程式設計方式存取]  。 因此，使用者可以叫用 API，以及從 AWS 帳戶擷取角色。

    * 選取 [下一步：權限]  。

18. 為此使用者建立新的原則。

    ![[新增使用者] 的螢幕擷取畫面](./media/amazon-web-service-tutorial/adduser2.png)

    a. 選取 [直接附加現有的原則]  。

    b. 在篩選區段 **AzureAD_SSOUserRole_Policy** 中，搜尋新建立的原則。

    c. 選取原則，然後選取 **[下一步：檢閱]** 。

19. 檢閱附加使用者的原則。

    ![醒目提示 [建立使用者] 的 [新增使用者] 頁面螢幕擷取畫面](./media/amazon-web-service-tutorial/adduser3.png)

    a. 檢閱使用者名稱、存取類型，以及對應至使用者的原則。

    b. 選取 [建立使用者]  。

20. 下載使用者的使用者認證。

    ![[新增使用者] 的螢幕擷取畫面](./media/amazon-web-service-tutorial/adduser4.png)

    a. 複製使用者**存取金鑰識別碼**和**祕密存取金鑰**。

    b. 在 Azure AD 使用者佈建區段中輸入這些認證，以從 AWS 主控台擷取角色。

    c. 選取 [關閉]  。

21. 在 Azure AD 管理入口網站的 AWS 應用程式中，移至[佈建]  。

    ![醒目提示 [佈建] 的 AWS 應用程式螢幕擷取畫面](./media/amazon-web-service-tutorial/provisioning.png)

22. 在 [用戶端祕密]  和 [祕密權杖]  欄位中，分別輸入存取金鑰和祕密。

    ![[系統管理員認證] 對話方塊的螢幕擷取畫面](./media/amazon-web-service-tutorial/provisioning1.png)

    a. 在 **clientsecret** 欄位中，輸入 AWS 使用者存取金鑰。

    b. 在 [祕密權杖]  欄位中，輸入 AWS 使用者祕密。

    c. 選取 [測試連線]  。

    d. 選取 [儲存]  來儲存設定。

23. 針對 [設定]  區段中的 [佈建狀態]  ，選取 [開啟]  。 然後選取 [儲存]  。

    ![醒目提示 [開啟] 的 [設定] 區段螢幕擷取畫面](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory]   > [使用者]   > [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   
   a. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   b. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。   
   c. 選取 [顯示密碼]  ，並將它寫下來。 然後，選取 [Create]  \(建立\)。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 AWS 的存取權授與 B.Simon，讓其能夠使用 Azure SSO。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Amazon Web Services (AWS)]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[管理] 區段的螢幕擷取畫面，其中已醒目提示 [使用者和群組]](common/users-groups-blade.png)

1. 選取 [新增使用者]  。 在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增使用者] 的螢幕擷取畫面](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊中，選取 [B.Simon]  。 然後選擇 [選取]  。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當的使用者角色。 然後選擇 [選取]  。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="test-single-sign-on"></a>測試單一登入

當您在存取面板中選取 [AWS] 圖格時，應該會自動登入您已設定 SSO 的 AWS。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="known-issues"></a>已知問題

 * 在 [佈建]  區段中，[對應]  子區段會顯示「正在載入...」訊息且永遠不會顯示屬性對應。 目前支援的唯一佈建工作流程是在使用者或群組指派期間，將角色從 AWS 匯入 Azure AD 以供選取。 此屬性對應是預先決定的，無法加以設定。
 
 * [佈建]  區段只支援一次輸入一個 AWS 租用戶的一組認證。 所有匯入的角色都會針對 AWS 租用戶寫入 Azure AD [`servicePrincipal` 物件](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)的 `appRoles` 屬性。 
 
   您可以從資源庫中，將多個 AWS 租用戶 (以 `servicePrincipals` 表示) 新增至 Azure AD 以進行佈建。 不過，有一個已知的問題，就是無法自動從用於佈建的多個 AWS `servicePrincipals` 中，將所有匯入的角色寫入用於 SSO 的單一 `servicePrincipal`。 
   
   因應措施如下：您可以使用 [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) 來擷取所有匯入每個 AWS `servicePrincipal` (已設定佈建) 的 `appRoles`。 接著，您可以將這些角色字串新增至已設定 SSO 的 AWS `servicePrincipal`。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

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
