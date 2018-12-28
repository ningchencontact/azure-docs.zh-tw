---
title: 教學課程：Azure Active Directory 與 Infinite Campus 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Infinite Campus 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 9f4adbacf2749e8c8ff2da8f331a007e8dcaaea3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099947"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>教學課程：Azure Active Directory 與 Infinite Campus 整合

在本教學課程中，您將了解如何整合 Infinite Campus 與 Azure Active Directory (Azure AD)。

Infinite Campus 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Infinite Campus 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Infinite Campus (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Infinite Campus 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Infinite Campus 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。
- 您至少需成為 Azure Active Directory 系統管理員才能完成設定。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Infinite Campus
2. 設定並測試 Azure AD 單一登入

## <a name="adding-infinite-campus-from-the-gallery"></a>從資源庫新增 Infinite Campus

若要設定將 Infinite Campus 整合到 Azure AD 中，您需要從資源庫將 Infinite Campus 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Infinite Campus，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Infinite Campus**，從結果面板中選取 [Infinite Campus]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Infinite Campus](./media/infinitecampus-tutorial/tutorial_infinitecampus_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Infinite Campus 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Infinite Campus 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Infinite Campus 中的相關使用者之間建立連結關聯性。

若要設定及測試與 Infinite Campus 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Infinite Campus 測試使用者](#creating-a-infinite-campus-test-user)** - 使 Infinite Campus 中 Britta Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Infinite Campus 應用程式中設定單一登入。

**若要設定與 Infinite Campus 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Infinite Campus] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

2. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![設定單一登入](common/tutorial_general_301.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![設定單一登入](common/editconfigure.png)

5. 如果您有**服務提供者中繼資料檔案** (跳至步驟 **11.c**)，請在 [基本 SAML 設定] 區段上執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]。

        ![image](common/b9_saml.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]。

    ![映像](common/b9(1)_saml.png)

    c. 當中繼資料檔案成功上傳後，會自動在 [基本 SAML 設定] 區段文字方塊中填入 [識別碼] 及 [回覆 URL] 值，如下所示：

    ![映像](./media/infinitecampus-tutorial/tutorial_infinitecampus_url.png)

    d. 在 [登入 URL] 文字方塊中，使用下列模式來輸入 URL (網域會隨著主控模型而異)：`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    > [!NOTE]
    > 您會在 [Infinite Campus SSO 服務提供者設定] 頁面上取得**服務提供者中繼資料檔案**，本教學課程稍後會予以說明。 如果您開始在 Infinite Campus 中使用新的 SAML 服務提供者組態，請跳至**步驟 11** 以完成匯出服務提供者中繼資料檔案。

6. 如果您沒有**服務提供者中繼資料檔案**，請執行下列步驟 (請注意，網域會隨著裝載模型而有所不同)：

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL： `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. 在 **[回覆 URL]** 文字方塊中，以下列模式輸入 URL：`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Infinite Campus 網域及 URL 單一登入資訊](./media/infinitecampus-tutorial/tutorial_infinitecampus_url1.png)

6. 在 [SAML 簽署憑證] 頁面上的 [SAML 簽署憑證] 區段中，按一下複製**圖示**以複製 **應用程式同盟中繼資料 Url**  並貼到記事本。

    ![憑證下載連結](./media/infinitecampus-tutorial/tutorial_infinitecampus_certificate.png) 

7. 在 [設定 Infinite Campus] 區段中上傳或使用 Azure 中繼資料檔案/URL 時，使用下列各值來驗證。

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

    ![Infinite Campus 設定](common/configuresection.png)

8. 在不同的 Web 瀏覽器視窗中，以安全性系統管理員身分登入 Infinite Campus。

9. 在左側的功能表中，按一下 [系統管理]。

    ![系統管理員](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

10. 瀏覽至 [使用者安全性] > [SAML 管理] > [SSO 服務提供者組態]。

    ![saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

11. 在 [SSO 服務提供者組態] 頁面中，執行下列步驟：

    ![sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. 選取 [啟用 SAML 單一登入]。
    
    b. 在 [選取選項以擷取識別提供者 (IDP) 伺服器資料] 區段中，選取 [中繼資料 URL] 並在方塊中貼上**應用程式同盟中繼資料 Url**，然後按一下 [同步]。

    c. 按一下 [服務提供者中繼資料] 連結將**服務提供者中繼資料檔案**儲存至您的電腦，然後在 [基本 SAML 設定] 區段中上傳該檔案，以在 Azure 入口網站中自動填入 [識別碼] 和 [回覆 URL] 值 (請參閱步驟 4 以了解上傳及自動填入值，或者參閱步驟 5 以了解手動輸入)。

    d. 按一下 [同步] 後，取得的值會自動填入 [SSO 服務提供者組態] 頁面中。

    e. 按一下 [檔案] 。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立_一個_稱為 Britta Simon 的測試使用者。

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

### <a name="creating-a-infinite-campus-test-user"></a>建立 Infinite Campus 測試使用者

Infinite Campus 具備以人口統計為中心的架構。 請連絡 [Infinite Campus 支援小組](mailto:sales@infinitecampus.com) 以在 Infinite Campus 平台中新增使用者。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Infinite Campus 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![指派使用者][201]

2. 在應用程式清單中，選取 [Infinite Campus]。

    ![設定單一登入](./media/infinitecampus-tutorial/tutorial_infinitecampus_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 Infinite Campus 圖格時，應該會自動登入您的 Infinite Campus 應用程式。 如果您在管理 Azure AD 的同一個瀏覽器中登入 Infinite Camnpus 應用程式，請務必以測試使用者身分登入 Azure AD。 如需存取面板的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

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
