---
title: 教學課程：Azure Active Directory 與 Peakon 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Peakon 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: af3402aab6e4a3a1b0401d66d42e82e449552867
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977997"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>教學課程：Azure Active Directory 與 Peakon 整合

在本教學課程中，您將了解如何整合 Peakon 與 Azure Active Directory (Azure AD)。

Peakon 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Peakon 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Peakon (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Peakon 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Peakon 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Peakon
2. 設定並測試 Azure AD 單一登入

## <a name="adding-peakon-from-the-gallery"></a>從資源庫新增 Peakon

若要設定 Peakon 與 Azure AD 整合，您需要從資源庫將 Peakon 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增Peakon，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Peakon**，從結果面板中選取 [Peakon]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Peakon](./media/peakon-tutorial/tutorial_peakon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Peakon 設定和測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Peakon 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 Peakon 中相關使用者之間的連結關聯性。

若要使用 Peakon 來設定和測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Peakon 測試使用者](#creating-a-peakon-test-user)** - 使 Peakon 中 Britta Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Peakon 應用程式中設定單一登入。

**若要使用 Peakon 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Peakon] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

2. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![設定單一登入](common/tutorial_general_301.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![設定單一登入](common/editconfigure.png)

4. 若您想要以 **IDP** 起始模式設定應用程式，請在 [基本 SAML 組態] 區段執行下列步驟：

    ![Peakon 網域及 URL 單一登入資訊](./media/peakon-tutorial/tutorial_peakon_url.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：`https://app.peakon.com/saml/<companyid>/metadata`

    b. 在 **[回覆 URL]** 文字方塊中，以下列模式輸入 URL：`https://app.peakon.com/saml/<companyid>/assert`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![Peakon 網域及 URL 單一登入資訊](./media/peakon-tutorial/tutorial_peakon_url1.png)

    在 [登入 URL] 文字方塊中，輸入 URL：`https://app.peakon.com/login`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼及回覆 URL 來更新這些值 (本教學課程稍後會說明)。

6. 在 [SAML 簽署憑證] 區段中，按一下 [下載] 以下載**憑證 (原始)**，然後將憑證檔案儲存在您的電腦上。

    ![憑證下載連結](./media/peakon-tutorial/tutorial_peakon_certificate.png) 

7. 在 [安裝 Peakon] 區段上，依據您的需求複製適當的 URL。

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

    ![Peakon 設定](common/configuresection.png)

8. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 Peakon。

9. 在頁面左側的功能表列中，按一下 [ **Configuration**] \(設定\)，然後瀏覽至 [ **Integrations**] \(整合\)。

    ![設定](./media/peakon-tutorial/tutorial_peakon_config.png)

10. 在 [Integrations] \(整合\) 頁面上，按一下 [Single Sign-On] \(單一登入\)。

    ![單一](./media/peakon-tutorial/tutorial_peakon_single.png)

11. 在 [Single Sign-On] \(單一登入\) 區段下方，按一下 [Enable] \(啟用\)。

    ![啟用](./media/peakon-tutorial/tutorial_peakon_enable.png)

12. 在 [Single sign-on for employees using SAML] \(使用 SAML 針對員工進行單一登入\) 區段上，執行下列步驟：

    ![saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. 在 [SSO Login URL] \(SSO 登入 URL\) 文字方塊中，貼上您從 Azure 入口網站複製的**登入 URL** 值。

    b. 在 [SSO Logout URL] \(SSO 登出 URL\) 文字方塊中，貼上您從 Azure 入口網站複製的**登出 URL** 值。

    c. 按一下 [Choose file] \(選擇檔案\)，將您從 Azure 入口網站下載的憑證上傳至 [憑證] 方塊。

    d. 按一下 **圖示**以複製**實體識別碼**，並在 Azure 入口網站上，貼至 [ **基本 SAML 設定**] 區段的 [ **識別碼**] 文字方塊中。

    e. 按一下 **圖示**以複製**回覆 URL (ACS)**，並在 Azure 入口網站上，貼至 [ **基本 SAML 設定**] 區段的 [ **回覆 URL**] 文字方塊中。

    f. 按一下 [儲存] 

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![建立 Azure AD 使用者][100]

2. 在畫面頂端選取 [新增使用者]。

    ![建立 Azure AD 測試使用者](common/create_aaduser_01.png) 

3. 在 [使用者] 屬性中，執行下列步驟。

    ![建立 Azure AD 測試使用者](common/create_aaduser_02.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性] [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="creating-a-peakon-test-user"></a>建立 Peakon 測試使用者

若要讓 Azure AD 使用者能夠登入 Peakon，必須將這些使用者佈建到 Peakon。  
Peakon 需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Peakon 公司網站。

2. 在頁面左側的功能表列中，按一下 [ **Configuration**]\(設定\)，然後瀏覽至 [ **Employees**]\(員工\)。 

    ![員工](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. 按一下頁面右上方的 [ **Add employee**]\(新增員工\)。

      ![新增員工](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. 在 [New employee] \(新增員工\) 對話方塊頁面上，執行下列步驟：

     ![新增員工](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. 在 [Name] \(名稱\) 文字方塊中，輸入 **Britta** 作為名字，並輸入 **simon** 作為姓氏。

    b. 在 [Email] \(電子郵件\) 文字方塊中，輸入電子郵件地址，例如 **Brittasimon@contoso.com**。

    c. 按一下 [Create employee] \(建立員工\)。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Peakon 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![指派使用者][201]

2. 在應用程式清單中，選取[Peakon]。

    ![設定單一登入](./media/peakon-tutorial/tutorial_peakon_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Peakon 圖格時，應該會自動登入您的 Peakon 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
