---
title: 教學課程：Azure Active Directory 與 ArcGIS Online 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 ArcGIS Online 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 12ab224481c519db36ae21dd11916649ff0bfbe3
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269029"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>教學課程：Azure Active Directory 與 ArcGIS Online 整合

在此教學課程中，您會了解如何將 ArcGIS Online 與 Azure Active Directory (Azure AD) 整合。

將 ArcGIS Online 與 Azure AD 整合可提供下列優點：

- 您可以在 Azure AD 中控制可存取 ArcGIS Online 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 ArcGIS Online (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先決條件

若要設定 Azure AD 與 ArcGIS Online 的整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 ArcGIS Online 單一登入的訂用帳戶

> [!NOTE]
> 若要測試此教學課程中的步驟，我們不建議使用生產環境。

若要測試此教學課程中的步驟，您應該依照這些建議執行：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在此教學課程中，您會在測試環境中測試 Azure AD 單一登入。
此教學課程中說明的案例由二個主要的基本工作組成：

1. 從資源庫新增 ArcGIS Online
2. 設定並測試 Azure AD 單一登入

## <a name="adding-arcgis-online-from-the-gallery"></a>從資源庫新增 ArcGIS Online

若要設定將 ArcGIS Online 整合到 Azure AD 中，您需要從資源庫將 ArcGIS Online 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 ArcGIS Online，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![映像](./media/arcgis-tutorial/selectazuread.png)

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![映像](./media/arcgis-tutorial/a_select_app.png)
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![映像](./media/arcgis-tutorial/a_new_app.png)

4. 在搜尋方塊中，輸入 **ArcGIS Online**，從結果面板中選取 [ArcGIS Online]，然後按一下 [新增] 按鈕以新增應用程式。

     ![映像](./media/arcgis-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在此節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 ArcGIS Online 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 ArcGIS Online 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 ArcGIS Online 中的相關使用者之間建立連結關聯性。

在 ArcGIS Online 中，指派 Azure AD 中的**使用者名稱**值作為 [Username] \(使用者名稱\) 的值，以建立連結關聯性。

若要設定及測試與 ArcGIS Online 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 ArcGIS Online 測試使用者](#create-an-arcgis-online-test-user)** - 在 ArcGIS Online 中建立一個與 Azure AD 中代表 Britta Simon 之項目連結的對應項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在此節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 ArcGIS Online 應用程式中設定單一登入。

**若要設定與 ArcGIS Online 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [ArcGIS Online] 應用程式整合頁面上，選取 [單一登入]。

    ![映像](./media/arcgis-tutorial/b1_b2_select_sso.png)

2. 按一下畫面頂端的 [變更單一登入模式] 以選取 [SAML] 模式。

      ![映像](./media/arcgis-tutorial/b1_b2_saml_ssso.png)

3. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取] 以啟用單一登入。

    ![映像](./media/arcgis-tutorial/b1_b2_saml_sso.png)

4. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [基本 SAML 設定] 對話方塊。

    ![映像](./media/arcgis-tutorial/b1-domains_and_urlsedit.png)

5. 在 [基本 SAML 設定] 區段上，執行下列步驟：

    a. 在 [登入 URL] 文字方塊中，使用下列模式來輸入 URL︰`https://<companyname>.maps.arcgis.com`。

    b. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`<companyname>.maps.arcgis.com`。

    ![映像](./media/arcgis-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [ArcGIS Online 用戶端支援小組](http://support.esri.com/en/)以取得這些值。

6. 在 [SAML 簽署憑證] 區段上，按一下 [下載] 以下載 [同盟中繼資料 XML]，然後將 XML 檔案儲存在您的電腦上。

    ![映像](./media/arcgis-tutorial/federationxml.png)

7. 若要自動執行 **ArcGIS Online** 內的設定，您必須按一下 [安裝延伸模組] 來安裝「我的應用程式安全登入瀏覽器延伸模組」。

    ![映像](./media/arcgis-tutorial/install_extension.png)

8. 將延伸模組新增至瀏覽器之後，按一下 [安裝 ArcGIS Online] 便會將您導向到 ArcGIS Online 應用程式。 請從該處提供用以登入 ArcGIS Online 的管理員認證。 瀏覽器延伸模組將會自動為您設定應用程式，並自動執行步驟 9 到 13。

9. 如果您想要手動設定 ArcGIS Online，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 ArcGIS Online 公司網站，然後執行下列步驟：

10. 按一下 [編輯設定]。

    ![編輯設定](./media/arcgis-tutorial/ic784742.png "編輯設定")

11. 按一下 [安全性] 。

    ![安全性](./media/arcgis-tutorial/ic784743.png "安全性")

12. 在 [企業登入] 下方，按一下 [設定識別提供者]。

    ![企業登入](./media/arcgis-tutorial/ic784744.png "企業登入")

13. 在 [設定識別提供者]  組態頁面上，執行下列步驟：

    ![設定識別提供者](./media/arcgis-tutorial/ic784745.png "設定識別提供者")

    a. 在 [名稱] 文字方塊中，輸入您的組織名稱。

    b. 針對 [用來提供企業識別提供者之中繼資料的方法]，選取 [檔案]。

    c. 若要上傳您下載的中繼資料檔，請按一下 [選擇檔案] 。

    d. 按一下 [設定識別提供者]。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

此節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者]，然後選取 [所有使用者]。

    ![映像](./media/arcgis-tutorial/d_users_and_groups.png)

2. 選取畫面頂端的 [新增使用者]。

    ![映像](./media/arcgis-tutorial/d_adduser.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![映像](./media/arcgis-tutorial/d_userproperties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性]、[顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="create-an-arcgis-online-test-user"></a>建立 ArcGIS Online 測試使用者

若要讓 Azure AD 使用者能夠登入 ArcGIS Online，必須將他們佈建到 ArcGIS Online。  
就 ArcGIS Online 而言，需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 登入您的 **ArcGIS** 租用戶。

2. 按一下 [邀請成員]。
   
    ![邀請成員](./media/arcgis-tutorial/ic784747.png "邀請成員")

3. 選取 [自動新增成員而不傳送電子郵件]，然後按 [下一步]。
   
    ![自動新增成員](./media/arcgis-tutorial/ic784748.png "自動新增成員")

4. 在 [成員]  對話頁面上，執行下列步驟：
   
     ![新增並檢閱](./media/arcgis-tutorial/ic784749.png "新增並檢閱")
    
     a. 輸入您想要佈建之有效 AAD 帳戶的 [電子郵件]、[名字] 和 [姓氏]。
  
     b. 按一下 [新增並檢閱]。
5. 檢閱您已輸入的資料，然後按一下 [新增成員]。
   
    ![新增成員](./media/arcgis-tutorial/ic784750.png "新增成員")
        
    > [!NOTE]
    > Azure Active Directory 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此節中，您會將 ArcGIS Online 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式]。

    ![映像](./media/arcgis-tutorial/d_all_applications.png)

2. 在應用程式清單中，選取 [ArcGIS Online]。

    ![映像](./media/arcgis-tutorial/d_all_application.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![映像](./media/arcgis-tutorial/d_leftpaneusers.png)

4. 選取 [新增] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![映像](./media/arcgis-tutorial/d_assign_user.png)

4. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

5. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="test-single-sign-on"></a>測試單一登入

在此節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [ArcGIS Online] 圖格時，應該會自動登入您的 ArcGIS Online 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)



