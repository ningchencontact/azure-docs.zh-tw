---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的 Smartsheet |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建至 Smartsheet 的使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: f0ca2dfa90e1312db664962e7ffbe6b3f4dd96e1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670942"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 Smartsheet

本教學課程的目的是要示範執行在 Smartsheet 和 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建及取消佈建使用者和/或群組至 Smartsheet 中的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先決條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Smartsheet 租用戶](https://www.smartsheet.com/pricing)
* Smartsheet Enterprise 或 Enterprise 頂級支援計劃，以系統管理員權限的使用者帳戶。

## <a name="assign-users-to-smartsheet"></a>將使用者指派到 Smartsheet

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，只有使用者和/或已指派給 Azure AD 中的應用程式的群組同步處理。

之前設定，並啟用自動使用者佈建，您應該決定哪些使用者和/或 Azure AD 中的群組需要存取 Smartsheet。 一旦決定後，您可以指派這些使用者和/或群組至 Smartsheet 依照此處的指示：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>將使用者指派至 Smartsheet 的重要秘訣

* 建議將單一 Azure AD 使用者指派至 Smartsheet，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 當將使用者指派給 Smartsheet，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

* 若要確保在 Smartsheet 與 Azure AD 之間的使用者角色指派中的同位檢查，建議使用完整的 Smartsheet 使用者清單中填入相同的角色指派。 若要從 Smartsheet 中擷取此使用者清單，請瀏覽至**帳戶系統管理員 > 使用者管理 > 其他動作 > 下載使用者清單 (csv)** 。

* 若要存取應用程式中的特定功能，Smartsheet 會要求使用者有多個角色。 若要深入了解使用者型別與 Smartsheet 中的權限，請前往[使用者類型和權限](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)。

*  如果使用者有多個角色指派 Smartsheet，在您**必須**確保這些角色指派會複寫在 Azure AD 以避免使用者可能遺失永久 Smartsheet 物件的存取權的案例中。 Smartsheet 中的每個唯一角色**必須**在 Azure AD 中指派給不同的群組。 使用者**必須**則加入至每個對應至所需的角色群組。 

## <a name="set-up-smartsheet-for-provisioning"></a>設定佈建的 Smartsheet

Smartsheet 設定自動化使用者佈建與 Azure AD 時，您必須啟用 SCIM 佈建 Smartsheet 上。

1. 以登入**SysAdmin**中 **[Smartsheet 入口網站](https://app.smartsheet.com/b/home)** 並瀏覽至**帳戶管理員**。

    ![Smartsheet 帳戶管理員](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. 移至**安全性控制項 > 使用者自動佈建 > 編輯**。

    ![Smartsheet 安全性控制項](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. 新增並驗證您打算從至 Smartsheet 的 Azure AD 佈建使用者的電子郵件網域。 選擇**未啟用**確保佈建的所有動作只都來自 Azure AD 中，並也確保您的 Smartsheet 使用者清單指派 Azure AD 同步。

    ![Smartsheet 使用者佈建](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. 驗證完成後，您必須啟用網域。 

    ![Smartsheet 啟用網域](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. 產生**祕密權杖**設定自動使用者佈建與 Azure AD 瀏覽至所需**應用程式和整合**。

    ![Smartsheet 安裝](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. 選擇**API 存取**。 按一下 **產生新的存取權杖**。

    ![Smartsheet 安裝](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. 定義 API 的存取權杖的名稱。 按一下 [確定 **Deploying Office Solutions**]。

    ![Smartsheet 安裝](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. 複製 API 存取權杖，並將它儲存，因為這是唯一的時候，您可以檢視它。 這需要**祕密權杖**在 Azure AD 中的欄位。

    ![Smartsheet 語彙基元](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>從資源庫新增 Smartsheet

若要設定自動使用者佈建與 Azure AD Smartsheet，您需要從 Azure AD 應用程式資源庫將 Smartsheet 新增到受控 SaaS 應用程式清單。

1. 在   **[Azure 入口網站](https://portal.azure.com)** ，在左側的導覽窗格中，選取**Azure Active Directory**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取**新的應用程式**窗格頂端的按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**Smartsheet**，選取**Smartsheet**在結果窗格中。 

    ![結果清單中的 Smartsheet](common/search-new-app.png)

5. 選取  **Smartsheet 註冊**按鈕會將您重新導向至 Smartsheet 的登入頁面。 

    ![Smartsheet OIDC 新增](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. 當 Smartsheet 是 OpenIDConnect 應用程式，選擇至 Smartsheet 使用您的 Microsoft 工作帳戶登入。

    ![Smartsheet OIDC 登入](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. 成功驗證之後接受同意提示同意頁面。 應用程式將會自動新增至您的租用戶，您會被重新導向至您的 Smartsheet 帳戶。

    ![Smartsheet OIDc 同意](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>設定自動使用者佈建至 Smartsheet 

本節將引導您逐步設定 Azure AD 佈建服務來建立、 更新和停用使用者和/或群組 Smartsheet 中的根據 Azure AD 中使用者和/或群組指派。

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建的 Smartsheet:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式**，然後選取**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取**Smartsheet**。

    ![應用程式清單中的 [Smartsheet] 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![佈建 索引標籤](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![佈建 索引標籤](common/provisioning-automatic.png)

5. 底下**系統管理員認證**區段中，輸入`https://scim.smartsheet.com/v2/`中**租用戶 URL**。 輸入您擷取，並從 Smartsheet 中稍早儲存的值**祕密權杖**。 按一下 **測試連接**以確保 Azure AD 可以連線至 Smartsheet。 如果連線失敗，請確定您的 Smartsheet 帳戶具有系統管理員權限並再試一次。

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]  。

8. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 Smartsheet**。

    ![Smartsheet 使用者對應](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. 檢閱從 Azure AD 同步至 Smartsheet 中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對 Smartsheet 中的使用者帳戶進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Smartsheet 使用者屬性](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Azure AD 佈建服務 Smartsheet，變更**佈建狀態**要**上**中**設定**一節。

    ![佈建狀態開啟](common/provisioning-toggle-on.png)

12. 藉由選擇需要的值中定義的使用者和/或您想要的群組要佈建到 Smartsheet**領域**中**設定**一節。

    ![佈建的範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]  。

    ![正在儲存佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建服務在 Smartsheet 的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Smartsheet 不支援虛刪除。 在使用者**active**屬性設定為 False，Smartsheet 會永久刪除使用者。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
