---
title: 教學課程：Azure Active Directory 與 Costpoint 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Costpoint 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1a8b916feb2ad67623434f2b63468be72bf1aa
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879630"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>教學課程：整合 Costpoint 與 Azure Active Directory

在本教學課程中，您會了解如何將 Costpoint 與 Azure Active Directory (Azure AD) 整合。 在整合 Costpoint 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Costpoint 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Costpoint。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Costpoint 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您將在測試環境中設定和測試 Azure AD SSO。 Costpoint 支援由 **SP 和 IDP** 起始的 SSO。

## <a name="adding-costpoint-from-the-gallery"></a>從資源庫新增 Costpoint

如要設定將 Costpoint 整合到 Azure AD 中，您需要從資源庫把 Costpoint 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Costpoint**。
1. 從結果面板選取 [Costpoint]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Costpoint 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Costpoint 中相關使用者之間的連結關聯性。

若要設定及測試與 Costpoint 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
2. **[設定 Costpoint](#configure-costpoint)** 以在應用程式端設定 SSO 設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B.Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Costpoint 測試使用者](#create-costpoint-test-user)** 在 Costpoint 中建立一個與 Azure AD 中代表使用者的項目連結的 B.Simon 對應項目。
6. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Costpoint]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 如果您有**服務提供者中繼資料檔案**，請在 [基本 SAML 設定]  區段上執行下列步驟：

    > [!NOTE]
    > 您將會從 [產生 Costpoint 中繼資料]  區段取得服務提供者中繼資料檔案，本教學課程稍後會加以說明。
 
    1. 按一下 [上傳中繼資料檔案]  。
    
    1. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]  。
    
    1. 在成功上傳中繼資料檔案之後，會自動在 [Costpoint] 區段文字方塊中填入 [識別碼]  和 [回覆 URL]  值

        > [!Note]
        > 如果 [識別碼]  和 [回覆 URL]  值未自動填入，請根據您的需求手動填入這些值。 確認 [識別碼 (實體識別碼)]  和 [回覆 URL (判斷提示取用者服務 URL)]  皆已正確設定，且 [ACS URL]  是以 **/LoginServlet.cps** 結尾的有效 Costpoint URL。

    1. 按一下 [設定額外的 URL]  。

    1. 在 [轉送狀態]  文字方塊中，輸入使用下列模式的值：`system=[your system], (for example, **system=DELTEKCP**)`

1. 如果您想要以 **SP** 起始模式設定應用程式，請執行下列步驟：
    
    在 [登入 URL]  文字方塊中，輸入 URL：`https://costpointteea.deltek.com/cpweb/cploginform.htm`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「識別碼」、「回覆 URL」及「轉送狀態」來更新這些值。 請連絡 [Costpoint 用戶端支援小組](https://www.deltek.com/about/contact-us)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 圖示以複製**應用程式同盟中繼資料 URL**，並儲存在記事本中。

   ![憑證下載連結](common/copy-metadataurl.png)

### <a name="generate-costpoint-metadata"></a>產生 Costpoint 中繼資料

**DeltekCostpoint711Security.pdf** 指南提供了 Costpoint SAML SSO 組態的說明。 從該處參考 [SAML 單一登入設定] -> [設定 Costpoint 與 Azure AD 之間的 SAML 單一登入]  區段。 依照指示產生 **Costpoint SP 同盟中繼資料 XML** 檔案。 在 Azure 入口網站的 [基本 SAML 組態]  中使用此檔案。

![Costpoint 組態公用程式](./media/costpoint-tutorial/config02.png)

> [!NOTE]
> 您將取得 [Costpoint 用戶端支援小組](https://www.deltek.com/about/contact-us)提供的 **DeltekCostpoint711Security.pdf** 指南。 如果您沒有此檔案，請洽詢該小組以取得此檔案。

### <a name="configure-costpoint"></a>設定 Costpoint

返回 **Costpoint 組態公用程式**，並將 [應用程式同盟中繼資料 URL]  貼到 [IdP 同盟中繼資料 XML]  文字方塊中，然後依照 **DeltekCostpoint711Security.pdf** 指南中的指示完成 Costpoint SAML 設定。 

![Costpoint 組態公用程式](./media/costpoint-tutorial/config01.png)

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

在本節中，您會將 Costpoint 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]   > [所有應用程式]  。
1. 在應用程式清單中，選取 [Costpoint]  。
1. 在應用程式概觀頁面的 [管理]  區段中，選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊中，從使用者清單中選取 **Britta Simon**，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-costpoint-test-user"></a>建立 Costpoint 測試使用者

在本節中，您將在 Costpoint 中建立一個使用者。 假設**使用者識別碼**為 **B.SIMON**，名稱為 **B.Simon**。 請與 [Costpoint 用戶端支援小組](https://www.deltek.com/about/contact-us)合作，在 Costpoint 平台中新增使用者。 您必須先建立和啟用使用者，然後才能使用單一登入。
 
建立之後，使用者的 [驗證方法]  選取項目必須是 [Active Directory]  、必須選取 [SAML 單一登入]  核取方塊，且 Azure Active Directory 中的使用者名稱必須是 [Active Directory 或憑證識別碼]  (如下所示)。

![Costpoint 使用者](./media/costpoint-tutorial/user01.png)

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [Costpoint] 圖格時，應該會自動登入您已設定 SSO 的 Costpoint。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)