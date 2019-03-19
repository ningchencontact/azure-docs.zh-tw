---
title: 教學課程：Azure Active Directory 與 Salesforce Sandbox 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Salesforce Sandbox 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6794d7eaccb488bb345227161f0bca02f14bc518
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852547"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>教學課程：Azure Active Directory 與 Salesforce Sandbox 整合

在本教學課程中，您會了解如何整合 Salesforce Sandbox 與 Azure Active Directory (Azure AD)。

沙箱讓您能夠針對不同用途 (例如開發、測試和訓練) 在個別環境中建立貴組織的多個複本，而不會危害 Salesforce 生產環境組織中的資料和應用程式。
如需更多詳細資料，請參閱 [Sandbox 概觀](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5)。

Salesforce Sandbox 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Salesforce Sandbox 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Salesforce Sandbox (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Salesforce Sandbox 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Salesforce Sandbox 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Salesforce Sandbox
2. 設定並測試 Azure AD 單一登入

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>從資源庫新增 Salesforce Sandbox

若要設定將 Salesforce Sandbox 整合到 Azure AD 中，您需要從資源庫將 Salesforce Sandbox 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Salesforce Sandbox，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Salesforce Sandbox**，從結果面板中選取 [Salesforce Sandbox]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 Salesforce Sandbox 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Salesforce Sandbox 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Salesforce Sandbox 中的相關使用者之間，建立連結關聯性。

在 Salesforce Sandbox 中，將 Azure AD 中**使用者名稱**的值，指派為 **Username** 的值，以建立連結關聯性。

若要設定及測試與 Salesforce Sandbox 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Salesforce Sandbox 測試使用者](#create-a-salesforce-sandbox-test-user)** - 使 Salesforce Sandbox 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，並在您的 Salesforce Sandbox 應用程式中設定單一登入。

**若要與 Salesforce Sandbox 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Salesforce Sandbox] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

2. 按一下畫面頂端的 [變更單一登入模式]，選取 [SAML] 模式。

      ![設定單一登入連結](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![設定單一登入連結](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [基本 SAML 組態] 對話方塊。
   
    ![設定單一登入連結](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. 若您想要以 **IDP** 起始模式設定應用程式，請在 [基本 SAML 組態] 區段執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]。

    ![上傳中繼資料檔案](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]。

    ![選擇中繼資料檔案](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > 您會收到來自 Salesforce Sandbox 管理入口網站的服務提供者中繼資料檔案，稍後會在本教學課程中加以說明。

    c. 成功上傳中繼資料檔案之後，[回覆 URL] 文字方塊會自動填入 [回覆 URL] 值。

    ![Salesforce Sandbox 網域和 URL 單一登入資訊](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. 在 [SAML 簽署憑證] 區段上，按一下 [下載] 以下載 [同盟中繼資料 XML]，然後將 XML 檔案儲存在您的電腦上。

    ![憑證下載連結](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. 在瀏覽器中開啟新索引標籤，登入您的 Salesforce Sandbox 系統管理員帳戶。

8. 按一下分頁右上角**設定圖示**底下的 [設定]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/configure1.png)

9. 在左側瀏覽窗格中向下捲動至 [設定]，按一下 [識別] 以展開相關的區段。 然後按一下 [單一登入設定]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. 在 [單一登入設定] 頁面上，按一下 [編輯] 按鈕。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/configure3.png)

11. 選取 [啟用 SAML]，然後按一下 [儲存]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. 若要設定 SAML 單一登入設定，請按一下 [從中繼資料檔案新增]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. 按一下 [**選擇檔案**] 以上傳您從 Azure 入口網站下載的中繼資料 XML 檔案，然後按一下 [**建立**]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. 在 [SAML 單一登入設定] 頁面上，欄位會自動填入，然後按一下 [儲存]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. 在 [單一登入設定] 頁面上，按一下 [下載中繼資料] 按鈕以下載服務提供者中繼資料檔案。 在 Azure 入口網站的 [基本 SAML 組態] 區段中使用此檔案，以便如上所述設定必要的 URL。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/configure4.png)

16. 如果您想要以 **SP** 起始模式設定應用程式，下列是此動作的必要條件：

    a. 應具有已驗證的網域。

    b. 您需要設定在 Salesforce 沙箱中設定並啟用網域，稍後會在本教學課程中說明此動作的步驟。

    c. 在 Azure 入口網站的 [基本 SAML 組態] 區段上，按一下 [設定額外 URL] 並執行下列步驟：
  
    ![Salesforce Sandbox 網域和 URL 單一登入資訊](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入值：`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > 啟用網域之後，應從 Salesforce 沙箱入口網站複製此值。

17. 在 [SAML 簽署憑證] 區段上，按一下 [同盟中繼資料 XML]，然後將 XML 檔案儲存在您的電腦上。

    ![憑證下載連結](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. 在瀏覽器中開啟新索引標籤，登入您的 Salesforce Sandbox 系統管理員帳戶。

19. 按一下分頁右上角**設定圖示**底下的 [設定]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/configure1.png)

20. 在左側瀏覽窗格中向下捲動至 [設定]，按一下 [識別] 以展開相關的區段。 然後按一下 [單一登入設定]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. 在 [單一登入設定] 頁面上，按一下 [編輯] 按鈕。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/configure3.png)

22. 選取 [啟用 SAML]，然後按一下 [儲存]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. 若要設定 SAML 單一登入設定，請按一下 [從中繼資料檔案新增]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. 按一下 [選擇檔案] 來上傳中繼資料 XML 檔案，然後按一下 [建立]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. 在 [SAML 單一登入設定] 頁面上，欄位會自動填入資料，請在 [名稱] 文字方塊中輸入設定的名稱 (例如：*SPSSOWAAD_Test*)，然後按一下 [儲存]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. 若要在 Salesforce 沙箱上啟用網域，請執行下列步驟：

    > [!NOTE]
    > 啟用網域之前，您必須在 Salesforce 沙箱上建立相同網域。 如需詳細資訊，請參閱[定義您的網域名稱](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)。 建好網域之後，請確定已正確設定網域。

    * 在 Salesforce 沙箱的左方導覽窗格中，按一下 [公司設定] 以展開相關的區段，然後按一下 [我的網域]。

         ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * 在 [驗證組態] 區段中，按一下 [編輯]。

        ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * 在 [驗證設定] 區段中，針對 [驗證服務]，選取在 Salesforce 沙箱中設定 SSO 期間所設定的 SAML 單一登入設定名稱，然後按一下 [儲存]。

        ![設定單一登入](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![建立 Azure AD 使用者][100]

2. 在畫面頂端選取 [新增使用者]。

    ![建立 Azure AD 測試使用者](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. 在 [使用者] 屬性中，執行下列步驟。

    ![建立 Azure AD 測試使用者](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 **使用者名**欄位中輸入**brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性] [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="create-a-salesforce-sandbox-test-user"></a>建立 Salesforce Sandbox 測試使用者

本節會在 Salesforce Sandbox 中建立名為 Britta Simon 的使用者。 Salesforce Sandbox 支援預設啟用的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 如果 Salesforce Sandbox 中還沒有使用者，當您嘗試存取 Salesforce Sandbox 時，就會建立新的使用者。 Salesforce Sandbox 也支援自動使用者佈建，您可以在[這裡](salesforce-sandbox-provisioning-tutorial.md)找到關於如何設定自動使用者佈建的更多詳細資料。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Salesforce Sandbox 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者角色][200]

**若要將 Britta Simon 指派給 Salesforce Sandbox，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Salesforce Sandbox]。

    ![應用程式清單中的 Salesforce Sandbox 連結](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 按一下 [新增使用者] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Salesforce Sandbox] 圖格時，應該會自動登入您的 Salesforce Sandbox 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
* [設定使用者佈建](salesforce-sandbox-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png
