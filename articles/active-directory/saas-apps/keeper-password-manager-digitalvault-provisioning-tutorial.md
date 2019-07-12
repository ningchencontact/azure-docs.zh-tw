---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的 Keeper Password Manager & Digital Vault |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到 Keeper Password Manager & Digital Vault 的使用者帳戶。
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 74bfe37323a17bde19e4a9bf4ec28c9c3910b37f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666238"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 Keeper Password Manager & Digital Vault

本教學課程的目的是要示範 Keeper Password Manager & Digital Vault 與 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建和取消佈建使用者和/或群組為 Keeper Password 中執行的步驟Manager & Digital Vault。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先決條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Keeper Password Manager & Digital Vault 的租用戶](https://keepersecurity.com/pricing.html?t=e)
* 中的使用者帳戶 Keeper Password Manager & Digital Vault 與系統管理員權限。

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>從資源庫新增 Keeper Password Manager & Digital Vault

設定 Keeper Password Manager & Digital Vault 與 Azure AD 的自動使用者佈建時，您需要從 Azure AD 應用程式資源庫新增 Keeper Password Manager & Digital Vault，到受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增 Keeper Password Manager & Digital Vault，請執行下列步驟：**

1. 在   **[Azure 入口網站](https://portal.azure.com)** ，在左側的導覽窗格中，選取**Azure Active Directory**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取**新的應用程式**窗格頂端的按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**Keeper Password Manager & Digital Vault**，選取**Keeper Password Manager & Digital Vault**在結果窗格中，然後按一下**新增**按鈕以新增應用程式。

    ![結果清單中的 Keeper Password Manager & Digital Vault](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>將使用者指派給 Keeper Password Manager & Digital Vault

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，只有使用者和/或已指派給 Azure AD 中的應用程式的群組同步處理。

在設定並啟用自動使用者佈建之前, 中，您應先決定哪些使用者和/或 Azure AD 中的群組需要 Keeper Password Manager & Digital Vault 的存取。 一旦決定後，您可以指派這些使用者和/或群組 Keeper Password Manager & Digital Vault 依照此處的指示：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>將使用者指派給 Keeper Password Manager & Digital Vault 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Keeper Password Manager & Digital Vault，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 當將使用者指派給 Keeper Password Manager & Digital Vault，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>設定自動使用者佈建到 Keeper Password Manager & Digital Vault 

本節將引導您逐步設定 Azure AD 佈建服務來建立、 更新和停用使用者和/或群組中 Keeper Password Manager & Digital Vault 根據使用者和/或群組指派 Azure AD 中。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入 Keeper Password Manager & Digital Vault，下列指示中提供[Keeper Password Manager & Digital Vault 單一登入教學課程](keeperpasswordmanager-tutorial.md)。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建 Keeper Password Manager & Digital Vault:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式**，然後選取**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 **Keeper Password Manager & Digital Vault**。

    ![應用程式清單中的 Keeper Password Manager & Digital Vault 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![佈建 索引標籤](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![佈建 索引標籤](common/provisioning-automatic.png)

5. 底下**系統管理員認證**區段中，輸入**租用戶 URL**並**祕密權杖**您 Keeper Password Manager & Digital Vault 帳戶步驟 6 中所述。

6. 登入您[Keeper Admin Console](https://keepersecurity.com/console/#login)。 按一下  **Admin**並選取現有的節點，或建立新的。 瀏覽至**佈建**索引標籤，然後選取**加入方法**。

    ![Keeper 系統管理員主控台](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    選取  **SCIM (System for Cross-domain Identity Management**。

    ![Keeper 新增 SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    按一下 **建立佈建設定的語彙基元**。

    ![Keeper 建立端點](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    複製的值**URL**並**語彙基元**，然後貼到**租用戶 URL**並**祕密權杖**在 Azure AD 中。 按一下 **儲存**Keeper 上佈建的安裝程式。

    ![Keeper 建立權杖](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. 填寫後在步驟 5 中所顯示的欄位，按一下**測試連接**以確保 Azure AD 可以連線到 Keeper Password Manager & Digital Vault。 如果連線失敗，請確定您的 Keeper Password Manager & Digital Vault 帳戶具有系統管理員權限並再試一次。

    ![租用戶 URL + 語彙基元](common/provisioning-testconnection-tenanturltoken.png)

8. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [儲存]  。

10. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 Keeper Password Manager & Digital Vault**。

    ![Keeper 使用者對應](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. 檢閱從 Azure AD 同步至 Keeper Password Manager & Digital Vault 中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對 Keeper Password Manager & Digital Vault 中的使用者帳戶進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Keeper 使用者屬性](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. 底下**對應**區段中，選取**同步處理 Azure Active Directory 群組 Keeper Password Manager & Digital Vault**。

    ![Keeper 群組對應](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. 檢閱從 Azure AD 同步至 Keeper Password Manager & Digital Vault 中的群組屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對 Keeper Password Manager & Digital Vault 中的群組進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Keeper 群組屬性](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

15. 若要啟用 Azure AD 佈建 Keeper Password Manager & Digital Vault 的服務，變更**佈建狀態**要**上**中**設定**一節。

    ![佈建狀態開啟](common/provisioning-toggle-on.png)

16. 藉由選擇需要的值中定義的使用者和/或您想要的群組要佈建到 Keeper Password Manager & Digital Vault**領域**中**設定**一節。

    ![佈建的範圍](common/provisioning-scope.png)

17. 當您準備好要佈建時，按一下 [儲存]  。

    ![正在儲存佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建服務在 Keeper Password Manager 的 Azure AD 所執行的所有動作 （& s)數位的保存庫。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Keeper Password Manager & Digital Vault 需要**電子郵件**並**userName**擁有相同的來源值，其中一個屬性的任何更新將會修改其他的值。
* Keeper Password Manager & Digital Vault 中不支援使用者刪除，只停用。 停用的使用者會顯示為已鎖定在 Keeper 系統管理主控台 UI。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

