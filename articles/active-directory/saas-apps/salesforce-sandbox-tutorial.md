---
title: 教學課程：Azure Active Directory 與 Salesforce Sandbox 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Salesforce Sandbox 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: 1e303485a03edcd9ba3d3e7380aa4c7ae8b1a4b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092608"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>教學課程：Azure Active Directory 與 Salesforce Sandbox 整合

在本教學課程中，您會了解如何整合 Salesforce Sandbox 與 Azure Active Directory (Azure AD)。

沙箱讓您能夠針對不同用途 (例如開發、測試和訓練) 在個別環境中建立貴組織的多個複本，而不會危害 Salesforce 生產環境組織中的資料和應用程式。
如需詳細資訊，請參閱[沙箱概觀](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5)。

Salesforce Sandbox 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Salesforce Sandbox 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Salesforce Sandbox (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Salesforce Sandbox 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 Salesforce Sandbox 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Salesforce Sandbox 支援由 **SP 和 IDP** 起始的 SSO
* Salesforce Sandbox 支援 **Just In Time** 使用者佈建
* Salesforce Sandbox 支援[**自動**使用者佈建](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>從資源庫新增 Salesforce Sandbox

若要設定將 Salesforce Sandbox 整合到 Azure AD 中，您需要從資源庫將 Salesforce Sandbox 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Salesforce Sandbox，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Salesforce Sandbox**，從結果面板中選取 [Salesforce Sandbox]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 Salesforce Sandbox](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Salesforce Sandbox 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Salesforce Sandbox 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Salesforce Sandbox 中的相關使用者之間，建立連結關聯性。

若要設定及測試與 Salesforce Sandbox 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Salesforce Sandbox 單一登入](#configure-salesforce-sandbox-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Salesforce Sandbox 測試使用者](#create-salesforce-sandbox-test-user)** - 使 Salesforce Sandbox 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Salesforce Sandbox 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Salesforce Sandbox]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定]  區段上，如果您有**服務提供者中繼資料檔案**，而想要以 **IDP** 起始模式進行設定，請執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]  。

    ![上傳中繼資料檔案](common/upload-metadata.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]  。

    ![選擇中繼資料檔案](common/browse-upload-metadata.png)

    > [!NOTE]
    > 您會收到來自 Salesforce Sandbox 管理入口網站的服務提供者中繼資料檔案，稍後會在本教學課程中加以說明。

    c. 成功上傳中繼資料檔案之後，[回覆 URL]  文字方塊會自動填入 [回覆 URL]  值。

    ![image](common/both-replyurl.png)

    > [!Note]
    > 如果 [回覆 URL]  值未自動填入，請根據您的需求手動填入此值。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  以依據您的需求從指定選項下載 [中繼資料 XML]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 Salesforce Sandbox]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-salesforce-sandbox-single-sign-on"></a>設定 Salesforce Sandbox 單一登入

1. 在瀏覽器中開啟新索引標籤，登入您的 Salesforce Sandbox 系統管理員帳戶。

2. 按一下分頁右上角**設定圖示**底下的 [設定]  。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/configure1.png)

3. 在左側瀏覽窗格中向下捲動至 [設定]  ，按一下 [識別]  以展開相關的區段。 然後按一下 [單一登入設定]  。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. 在 [單一登入設定]  頁面上，按一下 [編輯]  按鈕。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/configure3.png)

5. 選取 [啟用 SAML]  ，然後按一下 [儲存]  。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. 若要設定 SAML 單一登入設定，請按一下 [從中繼資料檔案新增]  。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. 按一下 [**選擇檔案**] 以上傳您從 Azure 入口網站下載的中繼資料 XML 檔案，然後按一下 [**建立**]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. 在 [SAML 單一登入設定]  頁面上，欄位會自動填入，然後按一下 [儲存]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. 在 [單一登入設定]  頁面上，按一下 [下載中繼資料]  按鈕以下載服務提供者中繼資料檔案。 在 Azure 入口網站的 [基本 SAML 組態]  區段中使用此檔案，以便如上所述設定必要的 URL。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/configure4.png)

10. 如果您想要以 **SP** 起始模式設定應用程式，下列是此動作的必要條件：

    a. 應具有已驗證的網域。

    b. 您需要設定在 Salesforce 沙箱中設定並啟用網域，稍後會在本教學課程中說明此動作的步驟。

    c. 在 Azure 入口網站的 [基本 SAML 組態]  區段上，按一下 [設定額外 URL]  並執行下列步驟：
  
    ![Salesforce Sandbox 網域和 URL 單一登入資訊](common/both-signonurl.png)

    在 [登入 URL]  文字方塊中，以下列模式輸入值：`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > 啟用網域之後，應從 Salesforce 沙箱入口網站複製此值。

11. 在 [SAML 簽署憑證]  區段上，按一下 [同盟中繼資料 XML]  ，然後將 XML 檔案儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

12. 在瀏覽器中開啟新索引標籤，登入您的 Salesforce Sandbox 系統管理員帳戶。

13. 按一下分頁右上角**設定圖示**底下的 [設定]  。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/configure1.png)

14. 在左側瀏覽窗格中向下捲動至 [設定]  ，按一下 [識別]  以展開相關的區段。 然後按一下 [單一登入設定]  。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. 在 [單一登入設定]  頁面上，按一下 [編輯]  按鈕。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/configure3.png)

16. 選取 [啟用 SAML]  ，然後按一下 [儲存]  。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. 若要設定 SAML 單一登入設定，請按一下 [從中繼資料檔案新增]  。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. 按一下 [選擇檔案]  來上傳中繼資料 XML 檔案，然後按一下 [建立]  。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. 在 [SAML 單一登入設定]  頁面上，欄位會自動填入資料，請在 [名稱]  文字方塊中輸入設定的名稱 (例如：*SPSSOWAAD_Test*)，然後按一下 [儲存]。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. 若要在 Salesforce 沙箱上啟用網域，請執行下列步驟：

    > [!NOTE]
    > 啟用網域之前，您必須在 Salesforce 沙箱上建立相同網域。 如需詳細資訊，請參閱[定義您的網域名稱](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)。 建好網域之後，請確定已正確設定網域。

21. 在 Salesforce 沙箱的左方導覽窗格中，按一下 [公司設定]  以展開相關的區段，然後按一下 [我的網域]  。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. 在 [驗證組態]  區段中，按一下 [編輯]  。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. 在 [驗證設定]  區段中，針對 [驗證服務]  ，選取在 Salesforce 沙箱中設定 SSO 期間所設定的 SAML 單一登入設定名稱，然後按一下 [儲存]  。

    ![設定單一登入](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`。 例如： BrittaSimon@contoso.com 。

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Salesforce Sandbox 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Salesforce Sandbox]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Salesforce Sandbox]  。

    ![應用程式清單中的 Salesforce Sandbox 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-salesforce-sandbox-test-user"></a>建立 Salesforce Sandbox 測試使用者

本節會在 Salesforce Sandbox 中建立名為 Britta Simon 的使用者。 Salesforce Sandbox 支援預設啟用的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 如果 Salesforce Sandbox 中還沒有使用者，當您嘗試存取 Salesforce Sandbox 時，就會建立新的使用者。 Salesforce Sandbox 也支援自動使用者佈建，您可以在[這裡](salesforce-sandbox-provisioning-tutorial.md)找到關於如何設定自動使用者佈建的更多詳細資料。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Salesforce Sandbox] 圖格時，應該會自動登入您已設定 SSO 的 Salesforce Sandbox。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [設定使用者佈建](salesforce-sandbox-provisioning-tutorial.md)
