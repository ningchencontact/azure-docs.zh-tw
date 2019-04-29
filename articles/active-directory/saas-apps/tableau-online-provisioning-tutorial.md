---
title: 教學課程：設定 Tableau Online 來搭配 Azure Active Directory 進行自動使用者佈建 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建至 Tableau Online 的使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dbebfa5fa7d9b255cc685696bfe8b3f61d5cf6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123749"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>教學課程：設定 Tableau Online 來自動佈建使用者

本教學課程會示範在 Tableau Online 與 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建和取消佈建使用者和群組到 Tableau Online 中執行的步驟。

> [!NOTE]
> 本教學課程說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、 運作方式和常見問題集，請參閱 <<c0> [ 自動化使用者佈建和解除佈建軟體做為服務 (SaaS) 應用程式與 Azure Active Directory](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>必要條件

本教學課程中所述的案例假設您有：

*   Azure AD 租用戶。
*   A [Tableau Online 的租用戶](https://www.tableau.com/)。
*   使用者帳戶在 Tableau Online 中具有管理員權限。

> [!NOTE]
> Azure AD 佈建整合仰賴[Tableau Online 的 Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm)。 Tableau Online 開發人員可使用此 API。

## <a name="add-tableau-online-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Tableau Online
設定自動化使用者佈建與 Azure AD 的 Tableau Online 之前，將 Tableau Online 從 Azure Marketplace 加入受控 SaaS 應用程式清單。

從 Marketplace，將新增 Tableau Online，請遵循下列步驟。

1. 在  [Azure 入口網站](https://portal.azure.com)，在左側瀏覽窗格中選取**Azure Active Directory**。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**Tableau Online** ，然後選取**Tableau Online**從結果面板中。 若要新增應用程式，請選取**新增**。

    ![結果清單中的 Tableau Online](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>將使用者指派給 Tableau Online

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，會同步處理只的使用者或群組指派給 Azure AD 中的應用程式。

您設定並啟用自動使用者佈建之前，決定哪些使用者或群組在 Azure AD 中的需要 Tableau Online 的存取權。 若要將這些使用者或群組指派給 Tableau Online 中，請依照中的指示[將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)。

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>將使用者指派給 Tableau Online 的重要秘訣

*   我們建議您指派單一 Azure AD 使用者新增至 Tableau Online 以測試自動使用者佈建的設定。 稍後可以指派額外的使用者或群組。

*   當您將使用者指派給 Tableau Online 時，選取任何有效的應用程式特有角色，如果有的話，在指派對話方塊中。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>設定自動使用者佈建至 Tableau Online

本節會引導您完成設定 Azure AD 佈建服務的步驟。 您可以使用它來建立、 更新和停用的 Tableau Online 根據使用者或群組指派 Azure AD 中使用者或群組。

> [!TIP]
> 您也可以啟用 SAML 型單一登入 Tableau online。 請依照下列中的指示[Tableau Online 單一登入教學課程](tableauonline-tutorial.md)。 單一登入可以獨立設定自動使用者佈建，雖然這兩個功能彼此補充。

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>設定 Azure ad 自動使用者佈建的 Tableau Online

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式** > **所有應用程式** > **Tableau Online**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Tableau Online] 。

    ![應用程式清單中的 [Tableau Online] 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![Tableau Online 佈建](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Tableau Online 的佈建模式](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. 底下**系統管理員認證**區段中，輸入網域、 系統管理員使用者名稱、 系統管理員密碼和您的 Tableau Online 帳戶的內容 URL:

   * 在 **網域**方塊中，填入步驟 6 為基礎的子網域。

   * 在 **系統管理員使用者名稱**方塊中，填入 Clarizen 租用戶系統管理員帳戶的使用者名稱。 例如 admin@contoso.com。

   * 在 **管理員密碼**方塊中，填入與管理員使用者名稱對應的系統管理員帳戶的密碼。

   * 在 **內容的 URL**方塊中，填入步驟 6 為基礎的子網域。

6. 您登入您的系統管理帳戶的 [Tableau Online 之後，您可以取得的值**網域**並**內容 URL**從系統管理員] 頁面的 URL。

    * Tableau Online 帳戶的**網域**可以從 URL 的這個部分複製：

        ![Tableau Online 網域](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **內容的 URL**您 Tableau Online 的帳戶可以複製此區段中。 為帳戶設定期間定義的值。 在此範例中，值是 "contoso"：

        ![Tableau Online 的內容 URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > 您**網域**可能不同於此處所示。

7. 您在步驟 5 所示的方塊中，填滿之後，請選取**測試連接**以確定 Azure AD 可以連線到 Tableau Online。 如果連線失敗，請確定您的 Tableau Online 帳戶具有管理員權限，並再試一次。

    ![Tableau Online 測試連接](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. 在 **通知電子郵件**方塊中，輸入人員的電子郵件地址或群組以接收佈建錯誤通知。 選取 **發生失敗時傳送電子郵件通知**核取方塊。

    ![Tableau Online 的通知電子郵件](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. 選取 [ **儲存**]。

10. 在 [對應] 區段中，選取 [將 Azure Active Directory 使用者同步至 Tableau]。

    ![Tableau Online 的使用者同步處理](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. 檢閱從 Azure AD 同步至 Tableau Online 中的使用者屬性**屬性對應**一節。 選取為 [比對] 屬性的屬性會用來比對 Tableau Online 中的使用者帳戶以進行更新作業。 若要儲存任何變更，請選取**儲存**。

    ![Tableau Online 的比對使用者屬性](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 群組至 Tableau]。

    ![Tableau Online 的群組同步處理](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. 檢閱從 Azure AD 同步至 Tableau Online 中的群組屬性**屬性對應**一節。 選取為 [比對] 屬性的屬性會用來比對 Tableau Online 中的使用者帳戶以進行更新作業。 若要儲存任何變更，請選取**儲存**。

    ![Tableau Online 的比對群組屬性](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. 若要設定範圍篩選器，請依照下列中的指示[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)。

15. 若要啟用 Azure AD 中佈建服務 Tableau Online**設定**區段中，變更**佈建狀態**來**上**。

    ![Tableau Online 的佈建狀態](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. 要佈建到 Tableau Online 中定義的使用者或您想要的群組。 在 **設定**區段中，選取您想要在的值**範圍**。

    ![Tableau Online 範圍](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. 當您準備好要佈建時時，請選取**儲存**。

    ![Tableau Online 儲存](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

此作業會啟動初始同步處理所有的使用者或群組中定義**領域**中**設定**一節。 初始同步處理所花費的時間比更新的同步執行。 只要 Azure AD 佈建服務執行，它們就會發生大約每 40 分鐘。 

您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告。 報表會描述佈建服務在 Tableau Online 上的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
