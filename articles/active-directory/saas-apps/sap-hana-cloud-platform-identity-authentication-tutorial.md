---
title: 教學課程：Azure Active Directory 與 SAP Cloud Platform Identity Authentication 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAP Cloud Platform Identity Authentication 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8e14d54ca30c0e46d3bef0202784a35493663ef
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198179"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>教學課程：Azure Active Directory 與 SAP Cloud Platform Identity Authentication 整合

在本教學課程中，您會了解如何整合 SAP Cloud Platform Identity Authentication 與 Azure Active Directory (Azure AD)。
整合 SAP Cloud Platform Identity Authentication 與 Azure AD 提供下列優點：

* 您可以在 Azure AD 中控制可存取 SAP Cloud Platform Identity Authentication 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 SAP Cloud Platform Identity Authentication (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 SAP Cloud Platform Identity Authentication 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 SAP Cloud Platform Identity Authentication 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* SAP Cloud Platform Identity Authentication 支援由 **SP** 和 **IDP** 起始的 SSO

深入探討技術細節之前，一定要了解您即將查看的概念。 SAP Cloud Platform Identity Authentication 和 Active Directory 同盟服務可讓您利用 SAP Cloud Platform Identity Authentication 所保護的 SAP 應用程式和服務，跨越 Azure AD (如 IdP) 所保護的應用程式或服務實作 SSO。

SAP Cloud Platform Identity Authentication 目前作為 SAP 應用程式的領導 Proxy 識別提供者。 而 Azure Active Directory 做為此設定中的識別提供者。 

下圖說明此關聯性：

![建立 Azure AD 測試使用者](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

透過此設定，您的 SAP Cloud Platform Identity Authentication 租用戶會設定為 Azure Active Directory 中信任的應用程式。

接著會在 SAP Cloud Platform Identity Authentication 管理主控台中設定您想以此方式保護的所有 SAP 應用程式和服務。

因此，必須在 SAP Cloud Platform Identity Authentication 中進行授與 SAP 應用程式和服務存取權的授權作業 (相對於在 Azure Active Directory 中設定授權)。

藉由透過 Azure Active Directory Marketplace 將 SAP Cloud Platform Identity Authentication 設定為應用程式，您不需要設定個別宣告或 SAML 判斷提示。

> [!NOTE]
> 目前只有 Web SSO 經過這兩個合作夥伴的測試。 應用程式對 API 或 API 對 API 通訊所需的流程應能運作，但尚未經過測試。 它們會在後續活動期間進行測試。

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>從資源庫新增 SAP Cloud Platform Identity Authentication

若要設定將 SAP Cloud Platform Identity Authentication 整合到 Azure AD 中，您需要將 SAP Cloud Platform Identity Authentication 從資源庫新增到受控 SaaS 應用程式清單中。

**若要從資源庫新增 SAP Cloud Platform Identity Authentication，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **SAP Cloud Platform Identity Authentication**，從結果面板中選取 [SAP Cloud Platform Identity Authentication]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 SAP Cloud Platform Identity Authentication](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 [應用程式名稱] 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 [應用程式名稱] 中相關使用者之間的連結關聯性。

若要設定及測試與 [應用程式名稱] 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 SAP Cloud Platform Identity Authentication 單一登入](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 SAP Cloud Platform Identity Authentication 測試使用者](#create-sap-cloud-platform-identity-authentication-test-user)** - 在 SAP Cloud Platform Identity Authentication 中建立一個與 Azure AD 中代表 Britta Simon 之使用者連結的 Britta Simon 對應項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 [應用程式名稱] 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SAP Cloud Platform Identity Authentication] 應用程式整合分頁上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 區段上，若您想要以 **IDP** 起始模式進行設定，請執行下列步驟：

    ![SAP Cloud Platform Identity Authentication 網域及 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`<IAS-tenant-id>.accounts.ondemand.com`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [SAP Cloud Platform Identity Authentication 客戶支援小組](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)以取得這些值。 如果您不知道此識別碼值，請參閱關於[租用戶 SAML 2.0 設定](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)的 SAP Cloud Platform Identity Authentication 文件。

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![SAP Cloud Platform Identity Authentication 網域及 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > 這不是真實的值。 請使用實際的登入 URL 來更新此值。 請使用您特有的商務應用程式登入 URL。 如有任何疑問，請連絡 [SAP Cloud Platform Identity Authentication 客戶支援小組](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)。

6. SAP Cloud Platform Identity Authentication 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性] 區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [使用者屬性] 對話方塊。

    ![映像](common/edit-attribute.png)

7. 如果您的 SAP 應用程式預期要有 **firstName** 之類的屬性，請在 [使用者屬性] 對話方塊上的 [使用者宣告] 區段中新增 **firstName**屬性，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱] 文字方塊中，輸入屬性名稱 firstName。

    c. 讓 [命名空間] 保持空白。

    d. 選取 [來源] 作為 [屬性]。

    e. 從 [來源屬性] 清單中，選取 user.givenname 屬性值。

    f. 按一下 [確定]。

    g. 按一下 [檔案] 。

8. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載] 以依據您的需求從指定選項下載 [中繼資料 XML]，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

9. 在 [設定 SAP Cloud Platform Identity Authentication] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>設定 SAP Cloud Platform Identity Authentication 單一登入

1. 若要為您的應用程式設定 SSO，請移至 SAP Cloud Platform Identity Authentication 管理主控台。 URL 具有下列模式：`https://<tenant-id>.accounts.ondemand.com/admin`。 然後參閱位於[與 Microsoft Azure AD 整合](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)的 SAP Cloud Platform Identity Authentication 相關文件。

2. 在 Azure 入口網站中，選取 [儲存] 按鈕。

3. 只有當您想要為另一個 SAP 應用程式新增和啟用 SSO 時，才繼續以下步驟。 重複**從資源庫新增 SAP Cloud Platform Identity Authentication**一節底下的步驟。

4. 在 Azure 入口網站的 [SAP Cloud Platform Identity Authentication] 應用程式整合分頁上，選取 [連結的登入]。

    ![設定連結的登入](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. 儲存組態。

> [!NOTE]
> 新的應用程式會利用先前 SAP 應用程式的單一登入設定。 請確定您在 SAP Cloud Platform Identity Authentication 管理主控台中使用相同的公司識別提供者。

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

在本節中，您可將 SAP Cloud Platform Identity Authentication 的存取權授予 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [SAP Cloud Platform Identity Authentication]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 **SAP Cloud Platform Identity Authentication**。

    ![應用程式清單中的 SAP Cloud Platform Identity Authentication 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>建立 SAP Cloud Platform Identity Authentication 測試使用者

您不需要在 SAP Cloud Platform Identity Authentication 中建立使用者。 Azure AD 使用者存放區中的使用者可以使用 SSO 功能。

SAP Cloud Platform Identity Authentication 支援 [識別身分同盟] 選項。 此選項可讓應用程式檢查公司識別提供者所驗證的使用者是否存在於 SAP Cloud Platform Identity Authentication 的使用者存放區中。

[識別身分同盟] 選項預設為停用。 如果已啟用 [識別身分同盟]，則只有匯入 SAP Cloud Platform Identity Authentication 的使用者可以存取應用程式。

如需如何啟用或停用與 SAP Cloud Platform Identity Authentication 之識別身分同盟的詳細資訊，請參閱[設定與 SAP Cloud Platform Identity Authentication 之使用者存放區的識別身分同盟](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)中的＜啟用與 SAP Cloud Platform Identity Authentication 的識別身分同盟＞。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [SAP Cloud Platform Identity Authentication] 圖格時，應該會自動登入您已設定 SSO 的 SAP Cloud Platform Identity Authentication。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
