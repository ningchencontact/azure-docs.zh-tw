---
title: 設定單一登入 - Azure Active Directory | Microsoft Docs
description: 本教學課程會使用 Azure 入口網站為應用程式設定透過 Azure Active Directory (Azure AD) 進行的 SAML 型單一登入。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 634c1d05847f3d4d7b7168d484cd16bf8e351b27
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723932"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>教學課程：為應用程式設定透過 Azure Active Directory 進行的 SAML 型單一登入

本教學課程會使用 [Azure 入口網站](https://portal.azure.com)為應用程式設定透過 Azure Active Directory (Azure AD) 進行的 SAML 型單一登入。 當沒有[應用程式特定教學課程](../saas-apps/tutorial-list.md)可用時，請使用此教學課程。

本教學課程會使用 Azure 入口網站執行下列動作：

> [!div class="checklist"]
> * 選取 SAML 型單一登入模式
> * 設定應用程式特定網域和 URL
> * 設定使用者屬性
> * 建立 SAML 簽署憑證
> * 將使用者指派給應用程式
> * 設定應用程式使用 SAML 型單一登入
> * 測試 SAML 設定

## <a name="before-you-begin"></a>開始之前

1. 如果尚未將應用程式新增至您的 Azure AD 租用戶，請參閱[快速入門：將應用程式新增至 Azure AD 租用戶](add-application-portal.md)。
1. 要求您的應用程式廠商提供[設定基本 SAML 選項](#configure-basic-saml-options)中所說明的資訊。
1. 請使用非生產環境來測試本教學課程中的步驟。 如果您沒有 Azure AD 的非生產環境，您可以[取得為期一個月的試用版](https://azure.microsoft.com/pricing/free-trial/)。
1. 以雲端應用程式系統管理員或 Azure AD 租用戶的應用程式系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="select-a-single-sign-on-mode"></a>選取單一登入模式

將應用程式新增至 Azure AD 租用戶之後，您即可為應用程式設定單一登入。

若要開啟單一登入設定：

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，選取 [Azure Active Directory]  。
1. 在所顯示 [Azure Active Directory]  導覽面板的 [管理]  之下，選取 [企業應用程式]  。 Azure AD 租用戶中應用程式的隨機樣本隨即出現。
1. 在 [應用程式類型]  功能表中，選取 [所有應用程式]  ，然後選取 [套用]  。
1. 輸入要設定單一登入的應用程式名稱。 例如，您可以輸入 **GitHub-test**，以設定您在[新增應用程式](add-application-portal.md)快速入門中新增的應用程式。  

   ![顯示應用程式搜尋列的螢幕擷取畫面](media/configure-single-sign-on-portal/azure-portal-application-search.png)

1. 選擇您要設定單一登入的應用程式。
1. 在 [管理]  區段中，選取 [單一登入]  。
1. 選取 [SAML]  以設定單一登入。 [設定使用 SAML 的單一登入 - 預覽]  夜面隨即出現。

## <a name="configure-basic-saml-options"></a>設定 SAML 基本選項

若要設定網域和 URL：

1. 與應用程式廠商連絡，以取得下列設定的正確資訊：

    | 組態設定 | SP 起始 | idP 起始 | 說明 |
    |:--|:--|:--|:--|
    | 識別碼 (實體識別碼) | 某些應用程式需要 | 某些應用程式需要 | 可唯一識別要設定單一登入的應用程式。 Azure AD 會將識別碼傳送給應用程式作為 SAML 權杖的 Audience 參數。 應用程式應該會對其進行驗證。 在應用程式中提供的任何 SAML 中繼資料中，此值也會顯示為實體識別碼。|
    | 回覆 URL | 選用 | 必要 | 指定應用程式預期要接收 SAML 權杖的位置。 此回覆 URL 也稱為判斷提示取用者服務 (ACS) URL。 |
    | 登入 URL | 必要 | 請勿指定 | 當使用者開啟此 URL 時，服務提供者會重新導向至 Azure AD 以進行驗證，並將使用者登入。 Azure AD 會使用此 URL 從 Office 365 和 Azure AD「存取面板」中啟動應用程式。 如果空白，當使用者啟動應用程式時，Azure AD 會倚賴識別提供者來開始單一登入。|
    | 轉送狀態 | 選用 | 選用 | 對應用程式指定在驗證完成後應將使用者重新導向到的位置。 此值通常是應用程式有效的 URL。 不過，有些應用程式會以不同的方式使用此欄位。 如需詳細資訊，請詢問應用程式廠商。
    | 登出 URL | 選用 | 選用 | 用於將 SAML 登出回應傳回應用程式。

1. 若要編輯基本 SAML 組態選項，請選取 [基本 SAML 組態]  區段右上角的 [編輯]  圖示 (鉛筆)。

     ![編輯基本 SAML 設定選項](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. 在頁面上的適當欄位中，輸入應用程式廠商在步驟 1 所提供的資訊。
1. 在頁面上方選取 [儲存]  。

## <a name="configure-user-attributes-and-claims"></a>設定使用者屬性和宣告

您可以控制當使用者登入時，Azure AD 會在 SAML 權杖中傳送給應用程式的資訊。 您可以設定使用者屬性來控制此資訊。 例如，您可以將 Azure AD 設定為當使用者登入時，將使用者的名稱、電子郵件和員工識別碼傳送至應用程式。

這些屬性可能是單一登入正常運作所需的必要或選擇性屬性。 如需詳細資訊，請參閱[應用程式專用教學課程](../saas-apps/tutorial-list.md)，或洽詢應用程式廠商。

1. 若要編輯使用者屬性和宣告，請選取 [使用者屬性和宣告]  區段右上角的 [編輯]  圖示 (鉛筆)。

   [名稱識別碼值]  是以 *user.principalname* 的預設值設定。 使用者識別碼可唯一識別應用程式內的每個使用者。 例如，如果電子郵件地址同時是使用者名稱和唯一識別碼，請將此值設定為 *user.mail*。

1. 若要修改 [名稱識別碼值]  ，請選取 [名稱識別碼值]  欄位的 [編輯]  圖示 (鉛筆)。 視需要，對識別碼格式和來源進行適當的變更。 在完成時儲存變更。 如需如何自訂宣告的詳細資訊，請參閱[針對企業應用程式自訂在 SAML 權杖中發出的宣告](../develop/active-directory-saml-claims-customization.md)操作說明文章。
1. 若要新增宣告，請選取頁面頂端的 [新增宣告]  。 輸入 [名稱]  並選取適當的來源。 如果您選取 [屬性]  來源，則必須選擇您要使用的 [來源屬性]  。 如果您選取 [翻譯]  來源，則必須選擇您要使用的 [轉換]  和 [參數 1]  。
1. 選取 [ **儲存**]。 新的宣告會出現在資料表中。

## <a name="generate-a-saml-signing-certificate"></a>建立 SAML 簽署憑證

Azure AD 會使用憑證來簽署它傳送至應用程式的 SAML 權杖。

1. 若要產生新的憑證，請選取 [SAML 簽署憑證]  區段右上角的 [編輯]  圖示 (鉛筆)。
1. 在 [SAML 簽署憑證]  區段中，選取 [新增憑證]  。
1. 在顯示的新憑證資料列中，設定 [到期日]  。 如需有關可用組態選項的詳細資訊，請參閱[ 進階憑證簽署選項](certificate-signing-options.md)文章。
1. 選取 [SAML 簽署憑證]  區段頂端的 [儲存]  。

## <a name="assign-users-to-the-application"></a>將使用者指派給應用程式

建議先對數個使用者或群組測試單一登入，再對您的組織推出應用程式。

> [!NOTE]
> 這些步驟會帶您前往入口網站中的 [使用者和群組]  組態區段。 當您完成時，您必須瀏覽回到 [單一登入]  區段以完成本教學課程。

若要將使用者或群組指派給應用程式：

1. 在入口網站中開啟應用程式 (如果尚未開啟)。
1. 在應用程式的左側導覽窗格中，選取 [使用者和群組]  。
1. 選取 [新增使用者]  。
1. 在 [新增指派]  區段中，選取 [使用者和群組]  。
1. 若要尋找特定使用者，請在 [選取成員或邀請外部使用者]  方塊中輸入使用者名稱。 然後，選取使用者的設定檔照片或標誌，然後選擇 [選取]  。
1. 在 [新增指派]  區段中，選取 [指派]  。 作業完成後，選取的使用者即會出現在 [使用者和群組]  清單中。

## <a name="set-up-the-application-to-use-azure-ad"></a>將應用程式設定為使用 Azure AD

就快要完成了。  在最後一個步驟中，您必須將應用程式設定成使用 Azure AD 作為 SAML 身分識別提供者。 

1. 向下捲動至 [設定 \<applicationName>]  區段。 在本教學課程中，此區段稱為 [設定 GitHub-test]  。
1. 從此區段中的每個資料列複製值。 然後，將每個值貼到 [基本 SAML 組態]  區段中的適當資料列。 例如，從 [設定 GitHub-test]  區段複製 [登入 URL]  值，並將它貼到 [基本 SAML 組態]  區段的 [登入 URL]  欄位中，依此類推。
1. 當您將所有值貼入適當的欄位後，請選取 [儲存]  。

## <a name="test-single-sign-on"></a>測試單一登入

您已準備就緒，可開始測試設定。  

1. 開啟應用程式的單一登入設定。
1. 捲動到 [驗證 \<applicationName> 單一登入]  區段。 在本教學課程中，此區段稱為 [設定 GitHub-test]  。
1. 選取 [測試]  。 測試選項隨即出現。
1. 選取 [以目前使用者身分登入]  。 此測試可讓您先看看單一登入對您這個系統管理員來說是否有效。

如果發生錯誤，將會出現錯誤訊息。 完成下列步驟：

1. 請將訊息內容複製並貼到 [錯誤的描述為何?]  方塊中。

    ![使用 [錯誤的描述為何] 方塊取得解決方案指引](media/configure-single-sign-on-portal/error-guidance.png)

1. 選取 [取得解決方案指引]  。 隨即會顯示根本原因和解決方法指引。  在此範例中，未將使用者指派給應用程式。
1. 閱讀解決方案指引，然後修正此問題 (如果可能的話)。
1. 再次執行測試，直到順利完成。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已為應用程式設定單一登入設定。 完成設定後，您將使用者指派給應用程式，並將應用程式設定為使用 SAML 型單一登入。 當這項工作全部完成後，您驗證了 SAML 登入可正常運作。

您進行了下列事項：
> [!div class="checklist"]
> * 選取 SAML 作為單一登入模式
> * 連絡應用程式廠商以設定網域和 URL
> * 設定使用者屬性
> * 建立 SAML 簽署憑證
> * 手動將使用者或群組指派給應用程式
> * 將應用程式設定成使用 Azure AD 作為 SAML 識別提供者
> * 測試 SAML 型單一登入

若要對組織中的更多使用者推出應用程式，請使用自動使用者佈建。

> [!div class="nextstepaction"]
> [了解如何使用自動佈建指派使用者](configure-automatic-user-provisioning-portal.md)
