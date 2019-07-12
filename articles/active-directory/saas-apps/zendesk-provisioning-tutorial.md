---
title: 教學課程：以 Azure Active Directory 設定 Zendesk 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建至 Zendesk 的使用者帳戶。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 166b7727362549aaf054e3f0282c564eca687eb9
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672869"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>教學課程：設定 Zendesk 來自動佈建使用者

本教學課程會示範在 Zendesk 和 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建和取消佈建使用者和群組至 Zendesk 中執行的步驟。

> [!NOTE]
> 本教學課程說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、 運作方式和常見問題集，請參閱 <<c0> [ 自動化使用者佈建和解除佈建軟體做為服務 (SaaS) 應用程式與 Azure Active Directory](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>必要條件

本教學課程中所述的案例假設您有：

* Azure AD 租用戶。
* Zendesk 租用戶[企業](https://www.zendesk.com/product/pricing/)計劃，或進一步啟用。
* 使用者帳戶在 Zendesk 中具有管理員權限。

> [!NOTE]
> Azure AD 佈建整合仰賴[Zendesk Rest API](https://developer.zendesk.com/rest_api/docs/core/introduction)。 此 API 可供 Zendesk 小組在 「 企業 」 方案或更高。

## <a name="add-zendesk-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Zendesk

Zendesk 設定自動使用者佈建與 Azure AD 之前，請從 Azure Marketplace 中將 Zendesk 新增到受控 SaaS 應用程式清單。

若要從 Marketplace 新增 Zendesk，請遵循下列步驟。

1. 在  [Azure 入口網站](https://portal.azure.com)，在左側瀏覽窗格中選取**Azure Active Directory**。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]  。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**Zendesk** ，然後選取**Zendesk**從結果面板中。 若要新增應用程式，請選取**新增**。

    ![結果清單中的 Zendesk](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>將使用者指派給 Zendesk

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，會同步處理只的使用者或群組指派給 Azure AD 中的應用程式。

您設定並啟用自動使用者佈建之前，決定哪些使用者或群組在 Azure AD 中的需要存取 Zendesk。 若要將這些使用者或群組指派給 Zendesk，請遵循[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)。

### <a name="important-tips-for-assigning-users-to-zendesk"></a>將使用者指派給 Zendesk 的重要秘訣

* 現在，Zendesk 角色會自動與動態填入 Azure 入口網站 UI 中。 您將 Zendesk 角色指派給使用者之前，請確定完成初始同步處理時，會針對要擷取的最新的角色，您的 Zendesk 租用戶中的 Zendesk 項目。

* 我們建議您指派單一 Azure AD 使用者至 Zendesk，若要測試您初始的自動使用者佈建的設定。 您可以指派額外的使用者或群組更新版本之後測試都成功。

* 當您將使用者指派給 Zendesk 時時，請選取任何有效的應用程式特有角色，如果有的話，在指派對話方塊中。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>設定自動使用者佈建至 Zendesk 

本節會引導您完成設定 Azure AD 佈建服務的步驟。 您可以使用它來建立、 更新和停用根據使用者或群組指派 Azure AD 中的 Zendesk 的使用者或群組。

> [!TIP]
> 您也可以啟用 SAML 型單一登入 zendesk。 請依照下列中的指示[Zendesk 單一登入教學課程](zendesk-tutorial.md)。 單一登入可以獨立設定自動使用者佈建，雖然這兩個功能彼此補充。

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>設定 Azure ad 自動使用者佈建的 Zendesk

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式** > **所有應用程式** > **Zendesk**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zendesk]  。

    ![應用程式清單中的 Zendesk 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![Zendesk 佈建](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![Zendesk 佈建模式](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. 底下**系統管理員認證**區段中，輸入系統管理員使用者名稱、 祕密的語彙基元和您的 Zendesk 帳戶的網域。 這些值的範例如下：

   * 在 **系統管理員使用者名稱**方塊中，填入 Zendesk 租用戶系統管理員帳戶的使用者名稱。 例如 admin@contoso.com。

   * 在 **祕密權杖**方塊中，填入秘密權杖，在步驟 6 中所述。

   * 在 **網域**方塊中，填寫您的 Zendesk 租用戶的子網域。 例如，對於帳戶的租用戶 url `https://my-tenant.zendesk.com`，為您的子網域**我租用戶**。

6. 您的 Zendesk 帳戶的祕密權杖位於**系統管理員** > **API** > **設定**。 請確定**權杖存取**設為**已啟用**。

    ![Zendesk 系統管理員設定](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk 祕密權杖](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. 您在步驟 5 所示的方塊中，填滿之後，請選取**測試連接**以確定 Azure AD 可以連線到 Zendesk。 如果連線失敗，請確定您的 Zendesk 帳戶具有管理員權限，並再試一次。

    ![Zendesk 測試連接](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. 在 **通知電子郵件**方塊中，輸入人員的電子郵件地址或群組以接收佈建錯誤通知。 選取 **發生失敗時傳送電子郵件通知**核取方塊。

    ![Zendesk 通知電子郵件](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. 選取 [ **儲存**]。

10. 在 [對應]  區段中，選取 [同步處理 Azure Active Directory 使用者至 Zendesk]  。

    ![Zendesk 的使用者同步處理](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. 檢閱從 Azure AD 同步至 Zendesk 中的使用者屬性**屬性對應**一節。 選取為 [比對]  屬性的屬性會用來比對 Zendesk 中的使用者帳戶以進行更新作業。 若要儲存任何變更，請選取**儲存**。

    ![Zendesk 相符的使用者屬性](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. 底下**對應**區段中，選取**同步處理 Azure Active Directory 群組至 Zendesk**。

    ![Zendesk 群組同步處理](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. 檢閱從 Azure AD 同步至 Zendesk 中的群組屬性**屬性對應**一節。 選取為 [比對]  屬性的屬性會用來比對 Zendesk 中的群組以進行更新作業。 若要儲存任何變更，請選取**儲存**。

    ![Zendesk 相符的群組屬性](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. 若要設定範圍篩選器，請依照下列中的指示[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)。

15. 若要啟用 Azure AD 中佈建服務 Zendesk**設定**區段中，變更**佈建狀態**來**上**。

    ![Zendesk 佈建狀態](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. 要佈建到 Zendesk 中定義的使用者或您想要的群組。 在 **設定**區段中，選取您想要在的值**範圍**。

    ![Zendesk 範圍](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. 當您準備好要佈建時時，請選取**儲存**。

    ![Zendesk 儲存](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

此作業會啟動初始同步處理所有的使用者或群組中定義**領域**中**設定**一節。 初始同步處理所花費的時間比更新的同步執行。 只要 Azure AD 佈建服務執行，它們就會發生大約每 40 分鐘。 

您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告。 報表會描述佈建服務在 Zendesk 的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Zendesk 的使用者支援使用群組**代理程式**僅限角色。 如需詳細資訊，請參閱 < [Zendesk 文件](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)。

* 當自訂角色指派給使用者或群組時，Azure AD 自動使用者佈建服務時，也會將指派的預設角色**代理程式**。 只有代理程式可以指派自訂角色。 如需詳細資訊，請參閱 < [Zendesk API 文件](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests)。 

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
