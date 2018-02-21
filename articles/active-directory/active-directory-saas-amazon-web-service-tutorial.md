---
title: "教學課程：Azure Active Directory 與 Amazon Web Services (AWS) 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Amazon Web Services (AWS) 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/16/2017
ms.author: jeedes
ms.openlocfilehash: 0ff14365323d66a101e5847d7959045c3f20dea2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>教學課程：Azure Active Directory 與 Amazon Web Services (AWS) 整合

在本教學課程中，您將了解如何整合 Amazon Web Services (AWS) 與 Azure Active Directory (Azure AD)。

將 Amazon Web Services (AWS) 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Amazon Web Services (AWS) 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Amazon Web Services (AWS)。
- 您可以在 Azure 入口網站集中管理您的帳戶。

如需 SaaS 應用程式與 Azure AD 整合的詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先決條件

若要設定 Azure AD 與 Amazon Web Services (AWS) 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Amazon Web Services (AWS) 單一登入的訂用帳戶

> [!NOTE]
> 我們不建議使用生產環境來測試本教學課程中的步驟。

若要測試本教學課程中的步驟，請遵循下列建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[申請一個月免費試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Amazon Web Services (AWS)
2. 設定並測試 Azure AD 單一登入

## <a name="add-amazon-web-services-aws-from-the-gallery"></a>從資源庫新增 Amazon Web Services (AWS)
若要設定將 Amazon Web Services (AWS) 整合到 Azure AD 中，您需要將 Amazon Web Services (AWS) 從資源庫新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Amazon Web Services (AWS)，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，選取 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 移至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請選取對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Amazon Web Services (AWS)**。 從結果窗格中選取 [Amazon Web Services (AWS)]，然後選取 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Amazon Web Services (AWS) 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Amazon Web Services (AWS) 與 Azure AD 中互相對應的使用者。 換句話說，您必須在 Azure AD 使用者與 Amazon Web Services (AWS) 中的相關使用者之間建立連結。

若要建立此連結，請在 Amazon Web Services (AWS) 中，為 [Username] \(使用者名稱\) 值提供一個與 Azure AD 中**使用者名稱**相同的值。 

若要設定及測試與 Amazon Web Services (AWS) 搭配運作的 Azure AD 單一登入，請完成下列構成要素：

1. [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
2. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 Britta Simon 測試 Azure AD 單一登入。
3. [建立 Amazon Web Services (AWS) 測試使用者](#create-an-amazon-web-services-aws-test-user)，以在 Amazon Web Services (AWS) 中建立一個與 Azure AD 中代表 Britta Simon 之項目連結的對應項目。
4. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. [測試單一登入](#test-single-sign-on)，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Amazon Web Services (AWS) 應用程式中設定單一登入。

**若要設定與 Amazon Web Services (AWS) 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Amazon Web Services (AWS)] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結][4]

2. 若要啟用單一登入，請在 [單一登入] 對話方塊的 [模式] 下拉式清單中，選取 [SAML 登入]。
 
    ![單一登入對話方塊](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. 在 [Amazon Web Services (AWS) 網域及 URL] 區段中，使用者不需要執行任何步驟，因為應用程式已經與 Azure 預先整合。

    ![Amazon Web Services (AWS) 網域及 URL 單一登入資訊](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Amazon Web Services (AWS) 軟體應用程式會預期要有特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性] 區段中管理這些屬性的值。 以下螢幕擷取畫面顯示了一個範例：

    ![設定單一登入屬性](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. 在 [單一登入] 方塊的 [使用者屬性] 區段中，設定 SAML 權杖屬性，如上圖所示，然後執行下列步驟：
    
    | 屬性名稱  | 屬性值 | 命名空間 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 角色            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >請將 Azure AD 中的使用者佈建設定為從 Amazon Web Services (AWS) 主控台擷取所有角色。 請參閱下列佈建步驟。

    a. 若要開啟 [新增屬性] 對話方塊，請選取 [新增屬性]。

    ![設定單一登入屬性](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![設定單一登入屬性](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. 在 [名稱] 方塊中，輸入該資料列所顯示的屬性名稱。

    c. 在 [值] 清單中，輸入針對該資料列顯示的屬性值。

    d. 在 [命名空間] 方塊中，輸入針對該資料列顯示的命名空間值。
    
    d. 選取 [確定]。

6. 在 [SAML 簽章憑證] 區段中，選取 [中繼資料 XML]。 然後，將中繼資料檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. 選取 [ **儲存**]。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 Amazon Web Services (AWS) 公司網站。

9. 選取 [Console Home] \(主控台首頁\)。
   
    ![設定單一登入首頁][11]

10. 選取 [Identity and Access Management] \(身分識別和存取管理\)。 
   
    ![設定單一登入身分識別][12]

11. 選取 [Identity Providers] (\身分識別提供者\)。 然後選取 [Create Provider] \(建立提供者\)。 
   
    ![設定單一登入提供者][13]

12. 在 [Configure Provider] \(設定提供者\) 對話方塊中，執行下列步驟： 
   
    ![設定單一登入對話方塊][14]
 
    a. 針對 [Provider Type] \(提供者類型\)，選取 [SAML]。

    b. 在 [Provider Name] \(提供者名稱\) 方塊中，輸入提供者名稱 (例如：WAAD)。

    c. 若要上傳從 Azure 入口網站下載的**中繼資料檔案**，請選取 [Choose File] \(選擇檔案\)。

    d. 選取 [Next Step] \(下一步\)。

13. 在 [Verify Provider Information] \(驗證提供者資訊\) 對話方塊中，選取 [Create] \(建立\)。 
    
    ![設定單一登入驗證][15]

14. 選取 [角色]。 然後選取 [Create New Role] \(建立新角色\) 。 
    
    ![設定單一登入角色][16]

15. 在 [Set Role Name] \(設定角色名稱\) 對話方塊中，執行下列步驟： 
    
    ![設定單一登入名稱][17] 

    a. 在 [Role Name] \(角色名稱\) 方塊中，輸入角色名稱 (例如：TestUser)。 

    b. 選取 [Next Step] \(下一步\)。

16. 在 [Select Role Type] \(選取角色類型\) 對話方塊中，執行下列步驟： 
    
    ![設定單一登入角色類型][18] 

    a. 選取 [識別提供者存取的角色]。 

    b. 在 [授與 SAML 提供者的 Web 單一登入 (WebSSO) 存取] 區段中，按一下 [選取]。

17. 在 [Establish Trust] \(建立信任\) 對話方塊中，執行下列步驟：  
    
    ![設定單一登入信任][19] 

    a. 選取您先前建立的 SAML 提供者 (例如：*WAAD*)。 
  
    b. 選取 [Next Step] \(下一步\)。

18. 在 [Verify Role Trust] \(確認角色信任\) 對話方塊中，選取 [Next Step] \(下一步\)。 
    
    ![設定單一登入角色信任][32]

19. 在 [Attach Policy] \(連結原則\) 對話方塊中，選取 [Next Step] \(下一步\)。  
    
    ![設定單一登入原則][33]

20. 在 [Review] \(檢閱\) 對話方塊中，執行下列步驟：   
    
    ![設定單一登入檢閱][34] 

    a. 選取 [Create Role] \(建立角色\)。

    b. 建立所需數量的角色，然後將它們與「識別提供者」對應。

21. 在 Azure AD 使用者佈建中，使用 Amazon Web Services (AWS) 服務帳戶認證從 Amazon Web Services (AWS) 帳戶擷取角色。 若要開始此工作，請開啟 Amazon Web Services (AWS) 主控台首頁。

22. 選取 [Services] \(服務\) > [Security,Identity& Compliance] \(安全性、身分識別和合規性\) > [IAM]。

    ![從 Amazon Web Services (AWS) 帳戶擷取角色](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. 在 [IAM] 區段中，選取 [Policies] \(原則\) 索引標籤。

    ![從 Amazon Web Services (AWS) 帳戶擷取角色](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. 若要建立新原則，請選取 [Create policy] \(建立原則\)。

    ![建立新原則](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. 若要建立您自己的原則以從 Amazon Web Services (AWS) 帳戶擷取所有角色，請執行下列步驟：

    ![建立新原則](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. 在 [Create policy] \(建立原則\) 區段中，選取 [JSON] 索引標籤。

    b. 在原則文件中，新增下列 JSON：
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam: ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    c. 若要驗證原則，請選取 [Review Policy] \(檢閱原則\) 按鈕。

    ![定義新的原則](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

26. 執行下列步驟來定義**新原則**：

    ![定義新的原則](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. 將 [原則名稱] 提供為 **AzureAD_SSOUserRole_Policy**。

    b. 您可以為原則提供下列 [Description] \(描述\)：**This policy will allow you to fetch the roles from AWS accounts** (此原則將允許您從 AWS 帳戶擷取角色)。
    
    c. 選取 [Create Policy] \(建立原則\) 按鈕。
        
27. 若要在 Amazon Web Services (AWS) IAM 服務中建立新的使用者帳戶，請執行下列步驟：

    a. 在 Amazon Web Services (AWS) IAM 主控台中，選取 [Users] \(使用者\)。

    ![定義新的原則](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. 若要建立新的使用者，請選取 [Add user] \(新增使用者\) 按鈕。

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. 在 [Add user] \(新增使用者\) 區段中，執行下列步驟：
    
    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * 在 [User name] \(使用者名稱\) 方塊中，輸入 **AzureADRoleManager**。
    
    * 針對 [Access type] \(存取類型\)，選取 [Programmatic access] \(以程式設計方式存取\) 選項。 如此一來，使用者便可以叫用 API，然後從 Amazon Web Services (AWS) 帳戶擷取角色。
    
    * 選取右下角的 [Next: Permissions] \(下一步: 權限\) 按鈕。

28. 執行下列步驟來為此使用者建立新原則：

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. 選取 [Attach existing policies directly] (直接連結現有的原則) 按鈕。

    b. 在篩選區段 **AzureAD_SSOUserRole_Policy** 中，搜尋新建立的原則。
    
    c. 選取 [原則]。 然後，選取 [Next: Review] \(下一步: 檢閱\) 按鈕。

29. 執行下列步驟來檢閱所連結使用者的原則：

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. 檢閱使用者名稱、存取類型，以及與使用者對應的原則。
    
    b. 若要建立使用者，請選取右下角的 [Create user] \(建立使用者\) 按鈕。

30. 執行下列步驟來下載使用者的認證：

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. 複製使用者**存取金鑰識別碼**和**祕密存取金鑰**。
    
    b. 在 Azure AD 使用者佈建區段中輸入這些認證，以從 Amazon Web Services (AWS) 主控台擷取角色。
    
    c. 選取右下角的 [Close] \(關閉\) 按鈕。

31. 瀏覽至「Azure AD 管理入口網站」中 Amazon Web Services (AWS) 應用程式的 [使用者佈建] 區段。

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. 在 [用戶端祕密] 和 [祕密權杖] 欄位中，分別輸入 [存取金鑰] 和 [祕密]。

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. 在 [clientsecret] 欄位中，輸入 Amazon Web Services (AWS) 使用者存取金鑰。
    
    b. 在 [祕密權杖] 欄位中，輸入 Amazon Web Services (AWS) 使用者祕密。
    
    c. 選取 [測試連接] 按鈕。 您應該能夠成功測試此連線。

    d. 選取頂端的 [儲存] 按鈕來儲存設定。
 
33. 確定在 [設定] 中將 [佈建狀態] 設定為 [開啟]。 做法是選取 [開啟]，然後選取頂端的 [儲存] 按鈕。

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本。 從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，請選取 [單一登入] 索引標籤。然後透過底部的 [設定] 區段來存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)。
> 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請採取下列步驟：**

1. 在 **Azure 入口網站**的左側窗格中，選取 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請移至 [使用者和群組]。 然後選取 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，請選取 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，採取下列步驟：

    ![[使用者] 對話方塊](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>建立 Amazon Web Services (AWS) 測試使用者

本節目標是在 Amazon Web Services (AWS) 中建立名為 Britta Simon 的使用者。 Amazon Web Services (AWS) 不需要您在其系統中建立用於單一登入的使用者，因此您不需要在這裡執行任何動作。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Amazon Web Services (AWS) 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者角色][200] 

**若要將 Britta Simon 指派給 Amazon Web Services (AWS)，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視。 然後移至目錄檢視並選取 [企業應用程式]。 接著，選取 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Amazon Web Services (AWS)] 。

    ![應用程式清單中的 [Amazon Web Services (AWS)] 連結](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 選取 [新增] 按鈕。 然後，在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊中，選取 [使用者] 清單中的 [Britta Simon]。

6. 在 [使用者和群組] 對話方塊中，按一下 [選取] 按鈕。 

7. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 [Amazon Web Services (AWS)] 圖格時，應該會自動登入您的 Amazon Web Services (AWS) 應用程式。 如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

