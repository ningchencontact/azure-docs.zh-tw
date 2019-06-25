---
title: 教學課程：Azure Active Directory 與 Druva 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Druva 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf359f38bd7032b4de48a17b91ba1c10b165c91
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807783"
---
# <a name="tutorial-integrate-druva-with-azure-active-directory"></a>教學課程：整合 Druva 與 Azure Active Directory

在本教學課程中，您會了解如何將 Druva 與 Azure Active Directory (Azure AD) 進行整合。 在整合 Druva 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Druva 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Druva。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 Druva 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 Druva 支援由 **SP** 和 **IDP** 起始的 SSO

## <a name="adding-druva-from-the-gallery"></a>從資源庫新增 Druva

若要設定將 Druva 整合到 Azure AD 中，您需要從資源庫將 Druva 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Druva**。
1. 從結果面板選取 [Druva]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **Britta Simon** 的測試使用者，設定及測試與 Druva 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Druva 中相關使用者之間的連結關聯性。

若要設定及測試與 Druva 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
2. **[設定 Druva SSO](#configure-druva-sso)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Druva 測試使用者](#create-druva-test-user)** - 使 Druva 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Druva]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，則使用者不需要執行任何步驟，因為應用程式已預先與 Azure 整合。

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，輸入 URL：`https://login.druva.com/login`

1. Druva 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性]  區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [使用者屬性]  對話方塊。

    ![image](common/edit-attribute.png)

1. 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，使用 [編輯]  圖示來編輯宣告或使用 [新增宣告]  來新增宣告，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    | Name | 來源屬性|
    | ------------------- | -------------------- |
    | insync\_auth\_token |輸入權杖產生的值 |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

   ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Druva]  區段上，根據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-druva-sso"></a>設定 Druva 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Druva 公司網站。

1. 移至 [管理] \> [設定]  。

    ![設定](./media/druva-tutorial/ic795091.png "設定")

1. 在 [單一登入設定] 對話方塊中，執行下列步驟：

    ![單一登入設定](./media/druva-tutorial/ic795092.png "單一登入設定")

    a. 在 [識別碼提供者登入 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    b. 在 [識別碼提供者登出 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值

    c. 在記事本中開啟您的 base-64 編碼的憑證，將其內容複製到您的剪貼簿，然後貼到 [識別提供者憑證]  文字方塊中。

    d. 按一下 [儲存]  來開啟 [設定]  頁面。

1. 在 [設定]  頁面上，按一下 [產生 SSO Token]  。

    ![設定](./media/druva-tutorial/ic795093.png "設定")

1. 在 [單一登入驗證 Token]  對話方塊中，執行下列步驟：

    ![SSO 權杖](./media/druva-tutorial/ic795094.png "SSO 權杖")

    a. 按一下 [複製]  ，在 Azure 入口網站之 [新增屬性]  區段的 [值]  文字方塊中貼上複製的值。

    b. 按一下 [關閉]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `Britta Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `BrittaSimon@contoso.com`。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Druva 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Druva]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊中，從使用者清單選取 **Britta Simon**，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-druva-test-user"></a>建立 Druva 測試使用者

若要讓 Azure AD 使用者可以登入 Druva，則必須將他們佈建到 Druva。 Druva 需以手動的方式佈建。

**若要設定使用者佈建，請執行下列步驟：**

1. 以系統管理員身分登入您的 **Druva** 公司網站。

1. 移至 [管理] \> [使用者]  。

    ![管理使用者](./media/druva-tutorial/ic795097.png "管理使用者")

1. 按一下 [新建]  。

    ![管理使用者](./media/druva-tutorial/ic795098.png "管理使用者")

1. 在 [建立新的使用者] 對話方塊中，執行下列步驟：

    ![建立新的使用者](./media/druva-tutorial/ic795099.png "建立新的使用者")

    a. 在 [Email address] \(電子郵件地址\)  文字方塊中，輸入使用者的電子郵件地址，例如 **brittasimon\@contoso.com**。

    b. 在 [名稱]  文字方塊中，輸入使用者的名稱，例如 **BrittaSimon**。

    c. 按一下 [建立使用者]  。

> [!NOTE]
> 您可以使用任何其他的 Druva 使用者帳戶建立工具，或是使用 Druva 提供的 API 來佈建 Azure AD 使用者帳戶。

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 Druva 圖格時，應該會自動登入您已設定 SSO 的 Druva。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
