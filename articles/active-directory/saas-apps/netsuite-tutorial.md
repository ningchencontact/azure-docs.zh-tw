---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 NetSuite 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 NetSuite 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a9b834feacd6241e66e18a4f4e6aadc43e909c7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438504"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsuite"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 NetSuite 整合

在本教學課程中，您會了解如何整合 NetSuite 與 Azure Active Directory (Azure AD)。 在整合 NetSuite 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 NetSuite 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 NetSuite。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 NetSuite 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* NetSuite 支援由 **IDP** 起始的 SSO

* NetSuite 支援 **Just In Time** 使用者佈建

* NetSuite 支援[自動使用者佈建](NetSuite-provisioning-tutorial.md)

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-netsuite-from-the-gallery"></a>從資源庫新增 NetSuite

若要設定 NetSuite 與 Azure AD 整合，您需要從資源庫將 NetSuite 新增到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **NetSuite**。
1. 從結果面板選取 [NetSuite]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>設定及測試 NetSuite 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 NetSuite 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 NetSuite 中相關使用者之間的連結關聯性。

若要設定及測試與 NetSuite 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 NetSuite SSO](#configure-netsuite-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 NetSuite 測試使用者](#create-netsuite-test-user)** - 讓 NetSuite 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [NetSuite]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：

    | |
    |--|
    | `https://<tenant-name>.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs` |

    > [!NOTE]
    > 這不是真正的值。 請使用實際的「回覆 URL」來更新此值。 請連絡 [NetSuite 客戶支援小組](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml)以取得此值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. NetSuite 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應加入 SAML 權杖屬性設定中。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯]  圖示以開啟 [使用者屬性] 對話方塊。

    ![image](common/edit-attribute.png)

1. 除了以上屬性外，NetSuite 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | 名稱 | 來源屬性 | 
    | ---------------| --------------- |
    | 帳戶  | `account id` |

    1. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    1. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    1. 讓 [命名空間]  保持空白。

    1. 選取 [來源] 作為 [屬性]  。

    1. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    1. 按一下 [確定]  。

    1. 按一下 [檔案]  。

    >[!NOTE]
    >帳戶屬性值不是真實的值。 您將會更新此值，稍後會在本教學課程中說明。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 NetSuite]  區段上，根據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 NetSuite 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [NetSuite]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-netsuite-sso"></a>設定 NetSuite SSO

1. 在瀏覽器中開啟新索引標籤，以系統管理員的身分登入您的 NetSuite 公司網站。

2. 在頁面頂端的工具列中，按一下 [設定]  ，巡覽至 [公司]  ，然後按一下 [啟用功能]  。

    ![設定單一登入](./media/NetSuite-tutorial/ns-setupsaml.png)

3. 在頁面中間的工具列中，按一下 [SuiteCloud]  。

    ![設定單一登入](./media/NetSuite-tutorial/ns-suitecloud.png)

4. 在 [管理驗證]  區段底下，選取 [SAML 單一登入]  以在 NetSuite 中啟用 [SAML 單一登入] 選項。

    ![設定單一登入](./media/NetSuite-tutorial/ns-ticksaml.png)

5. 在頁面頂端的工具列上，按一下 [設定]  。

    ![設定單一登入](./media/NetSuite-tutorial/ns-setup.png)

6. 從 [設定工作]  清單中，按一下 [整合]  。

    ![設定單一登入](./media/NetSuite-tutorial/ns-integration.png)

7. 在 [管理驗證]  區段中，按一下 [SAML 單一登入]  。

    ![設定單一登入](./media/NetSuite-tutorial/ns-saml.png)

8. 在 [SAML 設定]  頁面上的 [NetSuite 組態]  區段底下，執行下列步驟：

    ![設定單一登入](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. 選取 [主要驗證方法]  。

    b. 對標示為 [SAMLV2 識別提供者中繼資料]  的欄位，選取 [上傳 IDP 中繼資料檔案]  。 然後按一下 [瀏覽]  ，上傳您從 Azure 入口網站下載的中繼資料檔案。

    c. 按一下 [提交]  。

9. 在 NetSuite 中，按一下 [設定]  ，巡覽至 [公司]  ，然後從頂端導覽功能表按一下 [公司資訊]  。

    ![設定單一登入](./media/NetSuite-tutorial/ns-com.png)

    ![設定單一登入](./media/NetSuite-tutorial/ns-account-id.png)

    b. 在右側資料行上的 [公司資訊]  頁面上，複製 [帳戶識別碼]  。

    c. 將您從 NetSuite 帳戶複製的 [帳戶識別碼]  貼至 Azure AD 中的 [屬性值]  欄位中。 

10. 在使用者可以執行單一登入 NetSuite 之前，必須先在 NetSuite 中指派適當的權限。 請依照下列指示來指派這些權限。

    a. 在頂端的導覽功能表上，按一下 [設定]  。

    ![設定單一登入](./media/NetSuite-tutorial/ns-setup.png)

    b. 在左方的瀏覽功能表上，選取 [使用者/角色]  ，然後按一下 [管理角色]  。

    ![設定單一登入](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. 按一下 [新角色]  。

    d. 針對新角色輸入**名稱**。

    ![設定單一登入](./media/NetSuite-tutorial/ns-new-role.png)

    e. 按一下 [檔案]  。

    f. 在頂端的功能表中，按一下 [權限]  。 然後按一下 [設定]  。

    ![設定單一登入](./media/NetSuite-tutorial/ns-sso.png)

    g. 選取 [SAML 單一登入]  ，然後按一下 [新增]  。

    h. 按一下 [檔案]  。

    i. 在頂端的瀏覽功能表上，按一下 [設定]  ，然後按一下 [設定管理員]  。

    ![設定單一登入](./media/NetSuite-tutorial/ns-setup.png)

    j. 在左方的瀏覽功能表上，選取 [使用者/角色]  ，然後按一下 [管理使用者]  。

    ![設定單一登入](./media/NetSuite-tutorial/ns-manage-users.png)

    k. 選取測試使用者。 按一下 [編輯]  ，然後巡覽至 [存取]  索引標籤。

    ![設定單一登入](./media/NetSuite-tutorial/ns-edit-user.png)

    l. 在 [角色] 對話方塊中，指派您已建立的適當角色。

    ![設定單一登入](./media/NetSuite-tutorial/ns-add-role.png)

    m. 按一下 [檔案]  。

### <a name="create-netsuite-test-user"></a>建立 NetSuite 測試使用者

本節會在 NetSuite 中建立名為 Britta Simon 的使用者。 NetSuite 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 NetSuite 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [NetSuite] 圖格時，應該會自動登入您設定 SSO 的 NetSuite。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 NetSuite](https://aad.portal.azure.com/)

