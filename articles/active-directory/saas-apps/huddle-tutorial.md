---
title: 教學課程：Azure Active Directory 與 Huddle 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Huddle 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 3ec78c7dc3ac3ce9747a09218e1e35068c60b894
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187720"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>教學課程：Azure Active Directory 與 Huddle 整合

在本教學課程中，您將了解如何整合 Huddle 與 Azure Active Directory (Azure AD)。

Huddle 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Huddle 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Huddle (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定與 Huddle 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Huddle 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Huddle
2. 設定並測試 Azure AD 單一登入

## <a name="adding-huddle-from-the-gallery"></a>從資源庫新增 Huddle

若要設定將 Huddle 整合到 Azure AD 中，您需要從資源庫將 Huddle 新增到受控 SaaS app 清單。

**若要從資源庫新增 Huddle，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![[應用程式]][2]

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![[應用程式]][3]

4. 在搜尋方塊中，輸入 **Huddle**。 從結果面板選取 [Huddle]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Huddle 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Huddle 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 Huddle 中相關使用者之間的連結關聯性。

若要設定及測試與 Huddle 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Huddle 測試使用者](#creating-a-huddle-test-user)** - 使 Huddle 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Huddle 應用程式中設定單一登入。

**若要設定與 Huddle 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Huddle] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 按一下畫面頂端的 [變更單一登入模式]，選取 [SAML] 模式。

    ![設定單一登入](./media/huddle-tutorial/tutorial_general_300.png)

3. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![設定單一登入](./media/huddle-tutorial/tutorial_general_301.png)

4. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![設定單一登入](./media/huddle-tutorial/tutorial_general_302.png)

5. 若您想要在 [IDP] 起始模式中設定應用程式，請在 [基本 SAML 設定] 區段上執行下列步驟：

    > [!NOTE]
    > 系統會自動從您在下面輸入的網域中，偵測到 Huddle 執行個體。

    ![Huddle 網域與 URL 單一登入資訊](./media/huddle-tutorial/tutorial_huddle_url.png)

    a. 在 [識別碼] 文字方塊中輸入 URL：

    | | |
    |--|--|
    | `https://login.huddle.net`|
    | `https://login.huddle.com`|
    | |

    b. 在 [回覆 URL] 文字方塊中輸入 URL：

    | | |
    |--|--|
    | `https://login.huddle.net/saml/browser-sso`|
    | `https://login.huddle.com/saml/browser-sso`|
    | `https://login.huddle.com/saml/idp-initiated-sso`|
    | |

    c. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![Huddle 網域與 URL 單一登入資訊](./media/huddle-tutorial/tutorial_huddle_url1.png)

    在 [登入 URL] 文字方塊中，使用下列模式輸入任何一個 URL：

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.com`|
    | `https://us.huddle.com`|
    | |

    > [!NOTE]
    > [登入 URL] 的值不是真正的值。 請使用實際的登入 URL 來更新此值。 請連絡 [Huddle 用戶端支援小組](https://huddle.zendesk.com)以取得此值。

6. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載] 以依據您的需求下載適當的憑證，並儲存在電腦上。

    ![設定單一登入](./media/huddle-tutorial/tutorial_huddle_certificate.png)

7. 在 [安裝 Huddle] 區段上，依據您的需求複製適當的 URL。

    ![設定單一登入](./media/huddle-tutorial/tutorial_huddle_configure.png)

8. 若要在 **Huddle** 端設定單一登入，您必須將所下載的憑證以及從 Azure 入口網站 [設定 Huddle] 區段複製的 URL，傳送給 [Huddle 用戶端支援小組](https://huddle.zendesk.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

    >[!NOTE]
    > 單一登入必須由 Huddle 支援小組啟用。 設定完成後，您將會收到通知。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![建立 Azure AD 使用者][100]

2. 在畫面頂端選取 [新增使用者]。

    ![建立 Azure AD 測試使用者](./media/huddle-tutorial/create_aaduser_01.png) 

3. 在 [使用者] 屬性中，執行下列步驟。

    ![建立 Azure AD 測試使用者](./media/huddle-tutorial/create_aaduser_02.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性] [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="creating-a-huddle-test-user"></a>建立 Huddle 測試使用者

若要讓 Azure AD 使用者可以登入 Huddle，則必須將他們佈建至 Huddle。 在 Huddle 的情況下，需以手動的方式佈建。

**若要設定使用者佈建，請執行下列步驟：**

1. 以系統管理員身分登入您的 **Huddle** 公司網站。

2. 按一下 [工作區] 。

3. 按一下 [人員] \> [邀請人員]。

    ![People](./media/huddle-tutorial/IC787838.png "People")

4. 在 [建立新的邀請]  區段中，執行下列步驟：
  
    ![新的邀請](./media/huddle-tutorial/IC787839.png "新的邀請")
  
    a. 在 [選擇要邀請人員加入的小組] 清單中，選取 [小組]。

    b. 在 [輸入您想要邀請之人員的電子郵件地址] 文字方塊中，輸入您想要在其中佈建之有效 Azure AD 帳戶的**電子郵件地址**。

    c. 按一下 [邀請] 。

    >[!NOTE]
    > Azure AD 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。

>[!NOTE]
>您可以使用任何其他的 Huddle 使用者帳戶建立工具或 Huddle 提供的 API 來佈建 Azure AD 使用者帳戶。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Huddle 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![指派使用者][201]

2. 在應用程式清單中，選取[Huddle]。

    ![設定單一登入](./media/huddle-tutorial/tutorial_huddle_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Huddle ] 圖格時，您應該會取得 Huddle 應用程式的自動登入頁面。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png
