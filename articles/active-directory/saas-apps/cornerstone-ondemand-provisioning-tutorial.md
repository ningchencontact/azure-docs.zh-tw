---
title: 教學課程：以 Azure Active Directory 設定 Cornerstone OnDemand 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到 Cornerstone OnDemand 的使用者帳戶。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85ddcf3aff7d15c946230cedb0da190bca6aeab7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127492"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>教學課程：設定 Cornerstone OnDemand 來自動佈建使用者

本教學課程會示範在 Cornerstone OnDemand 與 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建和取消佈建使用者或群組對 Cornerstone OnDemand 中執行的步驟。

> [!NOTE]
> 本教學課程說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、 運作方式和常見問題集，請參閱 <<c0> [ 自動化使用者佈建和解除佈建軟體做為服務 (SaaS) 應用程式與 Azure Active Directory](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>必要條件

本教學課程中所述的案例假設您有：

* Azure AD 租用戶。
* Cornerstone OnDemand 租用戶。
* 使用者帳戶在 Cornerstone OnDemand 中具有管理員權限。

> [!NOTE]
> Azure AD 佈建整合仰賴[Cornerstone OnDemand web 服務](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)。 此服務是可供 Cornerstone OnDemand 小組使用。

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Cornerstone OnDemand

Cornerstone OnDemand 設定自動使用者佈建與 Azure AD 之前，請從 Marketplace 新增 Cornerstone OnDemand，到受控 SaaS 應用程式清單。

若要從 Marketplace 新增 Cornerstone OnDemand，請遵循下列步驟。

1. 在  [Azure 入口網站](https://portal.azure.com)，在左側瀏覽窗格中選取**Azure Active Directory**。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**Cornerstone OnDemand** ，然後選取**Cornerstone OnDemand**從結果面板中。 若要新增應用程式，請選取**新增**。

    ![結果清單中的 Cornerstone OnDemand](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>將使用者指派給 Cornerstone OnDemand

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，會同步處理只的使用者或群組指派給 Azure AD 中的應用程式。

您設定並啟用自動使用者佈建之前，決定哪些使用者或群組在 Azure AD 中的需要存取 Cornerstone OnDemand。 若要將這些使用者或群組指派給 Cornerstone OnDemand，請遵循[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)。

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>將使用者指派給 Cornerstone OnDemand 的重要秘訣

* 我們建議您指派單一 Azure AD 使用者給 Cornerstone OnDemand，以測試自動使用者佈建的設定。 稍後可以指派額外的使用者或群組。

* 當您將使用者指派給 Cornerstone OnDemand 時，請選取任何有效的應用程式特有角色，如果有的話，在指派對話方塊中。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>設定自動使用者佈建到 Cornerstone OnDemand

本節會引導您完成設定 Azure AD 佈建服務的步驟。 您可以使用它來建立、 更新和停用根據使用者或群組指派 Azure AD 中的 Cornerstone OnDemand 的使用者或群組。

若要設定 Azure ad 自動使用者佈建 Cornerstone ondemand，請遵循下列步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式** > **所有應用程式** > **Cornerstone OnDemand**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 **Cornerstone OnDemand**。

    ![應用程式清單中的 [Cornerstone OnDemand] 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![Cornerstone OnDemand 佈建](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Cornerstone OnDemand 佈建模式](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 底下**系統管理員認證**區段中，輸入系統管理員使用者名稱、 系統管理員密碼和您的 Cornerstone OnDemand 帳戶的網域：

    * 在 **系統管理員使用者名稱**方塊中，填入 Cornerstone OnDemand 租用戶系統管理員帳戶的使用者名稱的網域。 例如，contoso\admin。

    * 在 **管理員密碼**方塊中，填入與管理員使用者名稱對應的密碼。

    * 在 **網域**方塊中，填入 Cornerstone OnDemand 租用戶的 web 服務 URL。 例如，服務是位於`https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`，且網域為 contoso `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`。 如需有關如何擷取 web 服務 URL 的詳細資訊，請參閱 <<c0> [ 此 pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)。

6. 您在步驟 5 所示的方塊中，填滿之後，請選取**測試連接**以確定 Azure AD 可以連線到 Cornerstone OnDemand。 如果連線失敗，請確定您的 Cornerstone OnDemand 帳戶具有管理員權限，並再試一次。

    ![Cornerstone OnDemand 測試連接](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. 在 **通知電子郵件**方塊中，輸入人員的電子郵件地址或群組以接收佈建錯誤通知。 選取 **發生失敗時傳送電子郵件通知**核取方塊。

    ![Cornerstone OnDemand 通知電子郵件](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. 選取 [ **儲存**]。

9. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 Cornerstone OnDemand]。

    ![Cornerstone OnDemand 同步處理](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. 檢閱從 Azure AD 同步至 Cornerstone OnDemand 中的使用者屬性**屬性對應**一節。 選取為 [比對] 屬性的屬性會用來比對 Cornerstone OnDemand 中的使用者帳戶以進行更新作業。 若要儲存任何變更，請選取**儲存**。

    ![Cornerstone OnDemand 屬性對應](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. 若要設定範圍篩選器，請依照下列中的指示[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)。

12. 若要啟用 Azure AD 中佈建服務 Cornerstone OnDemand**設定**區段中，變更**佈建狀態**來**上**。

    ![Cornerstone OnDemand 佈建狀態](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. 定義要佈建到 Cornerstone OnDemand 的使用者或您想要的群組。 在 **設定**區段中，選取您想要在的值**範圍**。

    ![Cornerstone OnDemand 範圍](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. 當您準備好要佈建時時，請選取**儲存**。

    ![Cornerstone OnDemand 儲存](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

此作業會啟動初始同步處理所有的使用者或群組中定義**領域**中**設定**一節。 初始同步處理所花費的時間比更新的同步執行。 只要 Azure AD 佈建服務執行，它們就會發生大約每 40 分鐘。 

您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告。 報表會描述佈建服務在 Cornerstone OnDemand 的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

Cornerstone OnDemand **Position** 屬性預期的值是對應至 Cornerstone OnDemand 入口網站上的角色。 若要取得一份有效**位置**值，請移至**編輯使用者記錄 > 組織結構 > 位置**Cornerstone OnDemand 入口網站中。

![Cornerstone OnDemand 佈建編輯使用者記錄](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Cornerstone OnDemand 佈建的位置](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Cornerstone OnDemand 佈建的位置清單](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
