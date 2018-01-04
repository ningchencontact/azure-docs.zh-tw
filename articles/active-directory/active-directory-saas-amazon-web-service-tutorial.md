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
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: bc04f4c632daef99a4f12e237dfe395040039afe
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>教學課程：Azure Active Directory 與 Amazon Web Services (AWS) 整合

在本教學課程中，您將了解如何整合 Amazon Web Services (AWS) 與 Azure Active Directory (Azure AD)。

Amazon Web Services (AWS) 與 Azure AD 整合提供下列優點：

- 您可以控制可以存取 Amazon Web Services (AWS) 的 Azure AD 中。
- 您可以啟用自動取得登入到 Amazon Web Services (AWS) （單一登入） 讓使用者使用其 Azure AD 帳戶。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Amazon Web Services (AWS) 整合，您需要下列項目：

- Azure AD 訂用帳戶
- Amazon Web Services (AWS) 單一登入啟用的訂閱

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Amazon Web Services (AWS)
2. 設定並測試 Azure AD 單一登入

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>從資源庫新增 Amazon Web Services (AWS)
若要設定 Amazon Web Services (AWS) 與 Azure AD 整合，您需要從資源庫將 Amazon Web Services (AWS) 新增到受控 SaaS App 清單。

**若要從資源庫新增 Amazon Web Services (AWS)，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在 搜尋 方塊中，輸入**Amazon Web Services (AWS)**，選取**Amazon Web Services (AWS)**然後按一下 從結果面板**新增**按鈕以加入應用程式。

    ![Amazon Web Services (AWS) 在 [結果] 清單中](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Amazon Web Services (AWS) 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Amazon Web Services (AWS) 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Amazon Web Services (AWS) 中的相關使用者之間建立連結關聯性。

在 Amazon Web Services (AWS)，來指定的值**使用者名**做為值的 Azure AD 中**Username**建立的連結關聯性。

若要使用 Amazon Web Services (AWS) 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Amazon Web Services (AWS) 的測試使用者](#create-an-amazon-web-services-aws-test-user)** -若要在 Amazon Web Services (AWS) 連結至使用者的 Azure AD 表示有許 Simon 對應項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Amazon Web Services (AWS) 應用程式中設定單一登入。

**若要使用 Amazon Web Services (AWS) 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站上**Amazon Web Services (AWS)**應用程式整合頁面上，按一下 **單一登入**。

    ![設定單一登入連結][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. 在 [Amazon Web Services (AWS) 網域和 URL] 區段中，使用者不需要執行任何步驟，因為應用程式已經與 Azure 預先整合。

    ![Amazon Web Services (AWS) 網域和 Url 的單一登入資訊](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Amazon Web Services (AWS) 軟體應用程式預期要有特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性] 區段中管理這些屬性的值。 以下螢幕擷取畫面顯示上述的範例。

    ![設定單一登入屬性](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_attribute.png)  

5. 在 [單一登入] 對話方塊的 [使用者屬性] 區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：
    
    | 屬性名稱  | 屬性值 | 命名空間 |
    | --------------- | --------------- | --------------- |
    | rolesessionname | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 角色            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >您需要設定在 Azure AD 中的使用者佈建，以從 AWS 主控台擷取所有角色。 請參閱以下的佈建步驟。

    a. 按一下 [新增屬性] 來開啟 [新增屬性] 對話方塊。

    ![設定單一登入的新增](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![設定單一登入屬性](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 在 [值] 清單中，選取該列所顯示的值。
    
    d. 按一下 [確定] 。

6. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 Amazon Web Services (AWS) 公司網站。

9. 按一下 [主控台首頁] 。
   
    ![設定單一登入首頁][11]

10. 按一下 [身分識別與存取管理] 。 
   
    ![設定單一登入身分識別][12]

11. 按一下 [識別提供者]，然後按一下 [建立提供者]。 
   
    ![設定單一登入提供者][13]

12. 在 [設定提供者]  對話方塊頁面上，執行下列步驟： 
   
    ![設定單一登入對話方塊][14]
 
    a. 針對 [提供者類型]，選取 [SAML]。

    b. 在**提供者名稱**文字方塊中，輸入提供者名稱 (例如： *WAAD*)。

    c. 若要上傳您下載**中繼資料檔案**從 Azure 入口網站中，按一下 **選擇檔案**。

    d. 按一下頁面底部的 [新增] 。

13. 在 [驗證提供者資訊] 對話方塊頁面上，按一下 [建立]。 
    
    ![設定單一登入確認][15]

14. 按一下 [角色]，然後按一下 [建立新角色]。 
    
    ![設定單一登入的角色][16]

15. 在 [設定角色名稱]  對話方塊上，執行下列步驟： 
    
    ![設定單一登入名稱][17] 

    a. 在**角色名稱**文字方塊中，輸入角色名稱 (例如： *TestUser*)。 

    b. 按一下頁面底部的 [新增] 。

16. 在 [選取角色類型]  對話方塊上，執行下列步驟： 
    
    ![設定單一登入的角色類型][18] 

    a. 選取 [識別提供者存取的角色]。 

    b. 在 [授與 SAML 提供者的 Web 單一登入 (WebSSO) 存取] 區段中，按一下 [選取]。

17. 在 [建立信任]  對話方塊上，執行下列步驟：  
    
    ![設定單一登入的信任][19] 

    a. 為 SAML 提供者中，選取您先前建立的 SAML 提供者 (例如： *WAAD*) 
  
    b. 按一下頁面底部的 [新增] 。

18. 在 [確認角色信任] 對話方塊上，按 [下一步]。 
    
    ![設定單一登入角色的信任][32]

19. 在 [附加原則] 對話方塊上，按 [下一步]。  
    
    ![設定單一登入原則][33]

20. 在 [檢閱]  對話方塊上，執行下列步驟：   
    
    ![設定單一登入檢閱][34] 

    a. 按一下 [建立角色]。

    b. 建立所需數量的角色，並將它們對應至識別提供者。

21. 從 Azure AD 使用者佈建在 AWS 帳戶擷取角色使用 AWS 服務帳戶認證。 這麼做，開啟 AWS 主控台主。

22. 按一下**服務** -> **安全性、 身分識別與相容性** -> **IAM**。

    ![擷取從 AWS 帳戶的角色](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. 選取**原則**IAM 區段中的索引標籤。

    ![擷取從 AWS 帳戶的角色](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. 建立新的原則上的 **建立原則**。

    ![建立新的原則](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. 建立您自己的原則，以擷取 AWS 帳戶中的所有角色。 在**建立您自己的原則**區段中，按一下**選取** 按鈕。
    
    ![建立新的原則](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

26. 定義新的原則，藉由執行下列步驟：

    ![定義新的原則](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. 提供**原則名稱**為**AzureAD_SSOUserRole_Policy**。

    b. 您可以提供**描述**做為原則**此原則可讓從 AWS 帳戶擷取角色**。

    c. 在該原則文件中，加入下列 JSON。
    
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

    d. 請確定您已勾選**編輯原則時，用於自動格式設定**。

    e. 按一下**驗證原則**底部的按鈕。

    f. 一旦原則已正確驗證，然後您可以按一下**建立原則** 按鈕。

    ![建立新原則](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)
    
27. 在 AWS IAM 服務中建立新的使用者帳戶，藉由執行下列步驟：

    a. 按一下**使用者**AWS IAM 主控台中的導覽。

    ![定義新的原則](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. 按一下**新增使用者**按鈕以建立新的使用者。

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. 在**新增使用者**區段中，執行下列步驟：
    
    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * 輸入使用者名稱，做為**AzureADRoleManager**。
    
    * 在 存取類型中，選取**以程式設計方式存取**選項。 如此一來使用者可以叫用應用程式開發介面，和擷取 AWS 帳戶的角色。
    
    * 按一下**下一步 權限**在右下角的按鈕。

28. 現在建立新的原則，此使用者藉由執行下列步驟：

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/policy6.png)
    
    a. 按一下**直接附加現有的原則** 按鈕。

    b. 搜尋 [篩選] 區段中新建立的原則**AzureAD_SSOUserRole_Policy**。
    
    c. 選取**原則**，然後按一下**下一步： 檢閱** 按鈕。

29. 藉由執行下列步驟檢閱附加使用者原則：

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. 檢閱使用者名稱、 存取類型，以及對應至使用者的原則。
    
    b. 按一下**建立使用者**在建立使用者右下角的按鈕。

30. 下載使用者的使用者認證執行下列步驟：

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. 複製使用者**存取金鑰識別碼**和**秘密便捷鍵**。
    
    b. 輸入 Azure AD 的使用者佈建 > 一節來擷取 AWS 主控台中的角色中的這些認證。
    
    c. 按一下**關閉**底部的按鈕。

31. 瀏覽至**使用者佈建**Amazon Web Services 應用程式在 Azure AD 管理入口網站中的區段。

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. 輸入**便捷鍵**和**密碼**中**用戶端密碼**和**密碼語彙基元**分別欄位。

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. 輸入在 AWS 使用者存取金鑰**clientsecret**欄位。
    
    b. 輸入在 AWS 使用者密碼**密碼語彙基元**欄位。
    
    c. 按一下**測試連接**按鈕，您應該能夠成功測試此連接。

    d. 上的 儲存設定**儲存**頂端的按鈕。
 
33. 請確定您啟用佈建狀態**上**的交換器上，然後按一下 [設定] 區段中**儲存**頂端的按鈕。

    ![新增使用者](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，按一下 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>建立 Amazon Web Services (AWS) 的測試使用者

本節目標是在 Amazon Web Services (AWS) 中建立名為 Britta Simon 的使用者。 Amazon Web Services (AWS) 不需要使用者建立其系統中 SSO，因此您不需要執行以下任何動作。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您可以啟用許 Simon 只要授與存取 Amazon Web Services (AWS) 使用 Azure 單一登入。

![指派使用者角色][200] 

**若要將 Britta Simon 指派給 Amazon Web Services (AWS)，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Amazon Web Services (AWS)] 。

    ![Amazon Web Services (AWS) 中的連結應用程式清單](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Amazon Web Services (AWS)] 圖格時，應該會自動登入您的 Amazon Web 服務 (AWS) 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="known-issues"></a>已知問題

 * 在**佈建** 區段中，**對應**子區段會顯示 「 正在載入...」 訊息，並永遠不會顯示屬性對應。 目前支援的只有佈建工作流程是從 AWS 角色將匯入到 Azure AD 使用者/群組指派期間選擇。 這個屬性對應會預先決定的而且不可設定。
 
 * **佈建**區段只支援一次輸入一個 AWS 租用戶的一組認證。 所有匯入的角色會寫入至 Azure AD 的 appRoles 屬性[servicePrincipal 物件](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/serviceprincipal)AWS 租用戶。 （由服務主體表示） 的多個 AWS 租用戶可以從資源庫佈建，但是沒有已知的問題與不能夠自動為您撰寫的所有匯入角色從用於多個 AWS 服務主體加入至 Azure AD佈建到單一的 servicePrincipal 用於 單一登入。 因應措施， [Microsoft Graph API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/serviceprincipal)可用來擷取所有匯入每個 AWS servicePrincipal appRoles 其中設定佈建。 這些角色字串可以隨後加入 AWS servicePrincipal，其中設定單一登入。


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

