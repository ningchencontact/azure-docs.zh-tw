---
title: 教學課程：以 Azure Active Directory 設定 Samanage 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建至 Samanage 的使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: d474d9bfd6016885eaa21afcea5d44d39c624084
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730596"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>教學課程：設定 Samanage 來自動佈建使用者

本教學課程會示範在 Samanage 和 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建和取消佈建使用者和群組時，應執行的步驟。

> [!NOTE]
> 本教學課程說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、 運作方式和常見問題集，請參閱 <<c0> [ 自動化使用者佈建和解除佈建軟體做為服務 (SaaS) 應用程式與 Azure Active Directory](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>必要條件

本教學課程中所述的案例假設您有：

* Azure AD 租用戶。
* A [Samanage 租用戶](https://www.samanage.com/pricing/)與專業的封裝。
* 使用者帳戶在 Samanage 中具有管理員權限。

> [!NOTE]
> Azure AD 佈建整合仰賴[Samanage Rest API](https://www.samanage.com/api/)。 此 API 可供 Samanage 開發人員與專業的封裝的帳戶。

## <a name="add-samanage-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Samanage

Samanage 設定自動使用者佈建與 Azure AD 之前，請從 Azure Marketplace 中將 Samanage 新增至受控 SaaS 應用程式清單。

若要從 Marketplace 新增 Samanage，請遵循下列步驟。

1. 在  [Azure 入口網站](https://portal.azure.com)，在左側瀏覽窗格中選取**Azure Active Directory**。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]  。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**Samanage** ，然後選取**Samanage**從結果面板中。 若要新增應用程式，請選取**新增**。

    ![結果清單中的 Samanage](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>將使用者指派給 Samanage

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，會同步處理只的使用者或群組指派給 Azure AD 中的應用程式。

您設定並啟用自動使用者佈建之前，決定哪些使用者或群組在 Azure AD 中的需要存取 Samanage。 若要將這些使用者或群組指派給 Samanage，請依照中的指示[將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)。

### <a name="important-tips-for-assigning-users-to-samanage"></a>將使用者指派給 Samanage 的重要秘訣

*    現在，Samanage 角色會自動與動態填入 Azure 入口網站 UI 中。 您將 Samanage 角色指派給使用者之前，請確定完成初始同步處理時，會對 Samanage 來擷取您的 Samanage 租用戶中的最新的角色。

*    我們建議您指派單一 Azure AD 使用者給 Samanage，以測試您初始的自動使用者佈建的設定。 您可以指派額外的使用者和群組更新版本之後測試都成功。

*    當您將使用者指派給 Samanage 時時，請選取任何有效的應用程式特有角色，如果有的話，在指派對話方塊中。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-samanage"></a>設定自動使用者佈建至 Samanage

本節會引導您完成設定 Azure AD 佈建服務的步驟。 您可以使用它來建立、 更新和停用根據使用者或群組指派 Azure AD 中的 Samanage 的使用者或群組。

> [!TIP]
> 您也可以啟用 SAML 型單一登入 samanage。 請依照下列中的指示[Samanage 單一登入教學課程](samanage-tutorial.md)。 單一登入可以獨立設定自動使用者佈建，雖然這兩個功能彼此補充。

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>設定 Azure ad 自動使用者佈建為 Samanage

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式** > **所有應用程式** > **Samanage**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Samanage]  。

    ![應用程式清單中的 [Samanage] 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![Samanage 佈建](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![Samanage 佈建模式](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. 底下**系統管理員認證**區段中，輸入系統管理員使用者名稱和您的 Samanage 帳戶的系統管理員密碼。 這些值的範例如下：

   * 在 **系統管理員使用者名稱**方塊中，填寫您的 Samanage 租用戶系統管理員帳戶的使用者名稱。 例如 admin@contoso.com。

   * 在 **管理員密碼**方塊中，填入與管理員使用者名稱對應的系統管理員帳戶的密碼。

6. 您在步驟 5 所示的方塊中，填滿之後，請選取**測試連接**以確定 Azure AD 可以連線至 Samanage。 如果連線失敗，請確定您的 Samanage 帳戶具有管理員權限，並再試一次。

    ![Samanage 測試連接](./media/samanage-provisioning-tutorial/TestConnection.png)

7. 在 **通知電子郵件**方塊中，輸入人員的電子郵件地址或群組以接收佈建錯誤通知。 選取 **發生失敗時傳送電子郵件通知**核取方塊。

    ![Samanage 通知電子郵件](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. 選取 [ **儲存**]。

9. 在 [對應]  區段下，選取 [將 Azure Active Directory 使用者同步處理至 Samanage]  。

    ![Samanage 的使用者同步處理](./media/samanage-provisioning-tutorial/UserMappings.png)

10. 在 [屬性對應]  區段中，檢閱從 Azure AD 同步至 Samanage 的使用者屬性。 選取為 [比對]  屬性的屬性會用來比對 Samanage 中的使用者帳戶，以進行更新作業。 若要儲存任何變更，請選取**儲存**。

    ![Samanage 相符的使用者屬性](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. 若要啟用群組對應，請在 [對應]  區段下方，選取 [將 Azure Active Directory 群組同步處理至 Samanage]  。

    ![Samanage 群組同步處理](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. 將 [已啟用]  設定為 [是]  ，以同步處理群組。 在 [屬性對應]  區段中，檢閱從 Azure AD 同步處理至 Samanage 的群組屬性。 選取為 [比對]  屬性的屬性會用來比對 Samanage 中的使用者帳戶，以進行更新作業。 若要儲存任何變更，請選取**儲存**。

    ![Samanage 相符的群組屬性](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. 若要設定範圍篩選器，請依照下列中的指示[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)。

14. 若要啟用 Azure AD 中佈建服務 Samanage 中，**設定**區段中，變更**佈建狀態**來**上**。

    ![Samanage 佈建狀態](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. 要佈建到 Samanage 中定義的使用者或您想要的群組。 在 **設定**區段中，選取您想要在的值**範圍**。 當您選取**同步處理所有使用者和群組**選項下, 一節 < 連接器限制 > 中所述，請考慮限制

    ![Samanage 範圍](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. 當您準備好要佈建時時，請選取**儲存**。

    ![Samanage 儲存](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


此作業會啟動初始同步處理所有的使用者或群組中定義**領域**中**設定**一節。 初始同步處理所花費的時間比更新的同步執行。 只要 Azure AD 佈建服務執行，它們就會發生大約每 40 分鐘。 

您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告。 報表會描述佈建服務在 Samanage 上的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

如果您選取**同步處理所有使用者和群組**選項，並設定值，為 Samanage**角色**屬性，底下的值**預設值，如果是 null （是選擇性的）** 方塊必須以下列格式來表示：

- {"displayName":"role"}，其中的角色是您想要的預設值。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
