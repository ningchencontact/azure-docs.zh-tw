---
title: 教學課程：Azure Active Directory 與 iProva 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 iProva 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa5b66b3cf2ec038107293da7a821fcb75273fc9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718349"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>教學課程：Azure Active Directory 與 iProva 整合

在本教學課程中，您將了解如何整合 iProva 與 Azure Active Directory (Azure AD)。
iProva 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 iProva 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 iProva (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 iProva 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 iProva 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* iProva 支援 **SP** 起始的 SSO

## <a name="adding-iprova-from-the-gallery"></a>從資源庫新增 iProva

若要進行將 iProva 整合到 Azure AD 中的設定，您必須從資源庫將 iProva 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 iProva，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **iProva**，並從結果面板中選取 [iProva]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 iProva](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 iProva 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 iProva 中相關使用者之間的連結關聯性。

若要設定及測試與 iProva 搭配運作的 Azure AD 單一登入，您必須完成下列建置組塊：

1. **[從 iProva 擷取組態資訊](#retrieve-configuration-information-from-iprova)** 以準備執行後續步驟。
2. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
3. **[設定 iProva 單一登入](#configure-iprova-single-sign-on)** - 在應用程式端設定單一登入設定。
4. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
5. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
6. **[建立 iProva 測試使用者](#create-iprova-test-user)** - 使 iProva 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
7. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="retrieve-configuration-information-from-iprova"></a>從 iProva 擷取組態資訊

在本節中，您會從 iProva 擷取資訊，以設定 Azure AD 單一登入。

1. 開啟網頁瀏覽器，並使用下列 URL 模式移至 iProva 中的 [SAML2 資訊]  頁面：

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

    ![檢視 iProva SAML2 資訊頁面](media/iprova-tutorial/iprova-saml2-info.png)

2. 當您在另一個瀏覽器索引標籤中繼續進行後續步驟時，請讓此瀏覽器索引標籤保持開啟。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 iProva 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/) 的 [iProva]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  圖示以開啟 [基本 SAML 設定]  對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![iProva 網域與 URL 單一登入資訊](common/sp-identifier-reply.png)

    a. 使用 [iProva SAML2 資訊]  頁面上的 [EntityID]  標籤後面所顯示的值，填入 [識別碼]  方塊。 此頁面仍在您的另一個瀏覽器索引標籤中開啟。

    b. 使用 [iProva SAML2 資訊]  頁面上的 [回覆 URL]  標籤後面所顯示的值，填入 [回覆 URL]  方塊。 此頁面仍在您的另一個瀏覽器索引標籤中開啟。

    c. 使用 [iProva SAML2 資訊]  頁面上的 [登入 URL]  標籤後面所顯示的值，填入 [登入 URL]  方塊。 此頁面仍在您的另一個瀏覽器索引標籤中開啟。

5. iProva 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性]  區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [使用者屬性]  對話方塊。

    ![image](common/edit-attribute.png)

6. 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，使用 [編輯]  圖示來編輯宣告或使用 [新增宣告]  來新增宣告，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    | Name | 來源屬性| 命名空間  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 在 [命名空間]  文字方塊中，輸入該資料列顯示的命名空間值。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

7. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將其儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>設定 iProva 單一登入

1. 使用**系統管理員**帳戶登入 iProva。

2. 開啟 [移至]  功能表。

3. 選取 [應用程式管理]  。

4. 選取 [系統設定]  面板中的 [一般]  。

5. 選取 [編輯]  。

6. 向下捲動至 [存取控制]  。

    ![iProva 存取控制設定](media/iprova-tutorial/iprova-accesscontrol.png)

7. 找出 [使用者會使用其網路帳戶自動登入]  設定，並將其變更為 [是，透過 SAML 進行驗證]  。 此時會出現其他選項。

8. 選取 [設定]  。

9. 選取 [下一步]  。

10. iProva 此時會詢問您是要從 URL 下載同盟資料，還是將從檔案加以上傳。 請選取 [從 URL]  選項。

    ![下載 Azure AD 中繼資料](media/iprova-tutorial/iprova-download-metadata.png)

11. 貼上您在 [設定 Azure AD 單一登入] 區段的最後一個步驟中儲存的中繼資料 URL。

12. 選取箭號按鈕，從 Azure AD 下載中繼資料。

13. 下載完成後，便會出現確認訊息**已下載有效的同盟資料檔案**。

14. 選取 [下一步]  。

15. 暫且略過 [測試登入]  選項，然後選取 [下一步]  。

16. 在 [要使用的宣告]  下拉式方塊中，選取 [windowsaccountname]  。

17. 選取 [完成]  。

18. 現在您會回到 [編輯一般設定]  畫面。 向下捲動至頁面底部，然後選取 [確定]  以儲存您的設定。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 iProva 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [iProva]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取[iProva]  。

    ![應用程式清單中的 iProva 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-iprova-test-user"></a>建立 iProva 測試使用者

1. 使用**系統管理員**帳戶登入 iProva。

2. 開啟 [移至]  功能表。

3. 選取 [應用程式管理]  。

4. 選取 [使用者和使用者群組]  面板中的 [使用者]  。

5. 選取 [新增]  。

6. 在 [使用者名稱]  方塊中，輸入使用者的使用者名稱，例如 `BrittaSimon@contoso.com`。

7. 在 [全名]  方塊中，輸入使用者的全名，例如 **BrittaSimon**。

8. 選取 [無密碼 (使用單一登入)]  選項。

9. 在 [電子郵件地址]  方塊中，輸入使用者的電子郵件地址，例如 `BrittaSimon@contoso.com`。

10. 向下捲動至頁面結尾處，然後選取 [完成]  。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [iProva] 圖格時，應該會自動登入您已設定 SSO 的 iProva。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)