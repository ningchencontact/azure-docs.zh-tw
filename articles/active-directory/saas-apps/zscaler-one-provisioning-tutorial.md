---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的 Zscaler One |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到 Zscaler One 使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 5319b0ac06c4ddf1a7627a4e7fe0bfb2694f79f6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114595"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 Zscaler One

本教學課程會示範在 Zscaler One 與 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建和取消佈建使用者和群組給 Zscaler One 中執行的步驟。

> [!NOTE]
> 本教學課程說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、 運作方式和常見問題集，請參閱 <<c0> [ 自動化使用者佈建和解除佈建軟體做為服務 (SaaS) 應用程式與 Azure Active Directory](../active-directory-saas-app-provisioning.md)。
>
> 此連接器是目前以預覽形式提供。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 使用 Microsoft Azure 預覽補充規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中所述的案例假設您有：

* Azure AD 租用戶。
* Zscaler One 租用戶。
* 使用者帳戶在 Zscaler One 中具有管理員權限。

> [!NOTE]
> Azure AD 佈建整合仰賴 Zscaler One SCIM API。 此 API 是提供給 Zscaler One 的開發人員與企業封裝的帳戶。

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Zscaler One

設定自動化使用者佈建與 Azure AD 的 Zscaler One 之前，將 Zscaler One 從 Azure Marketplace 加入受控 SaaS 應用程式清單。

從 Marketplace，將新增 Zscaler One，請遵循下列步驟。

1. 在  [Azure 入口網站](https://portal.azure.com)，在左側瀏覽窗格中選取**Azure Active Directory**。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**Zscaler One** ，然後選取**Zscaler One**從結果面板中。 若要新增應用程式，請選取**新增**。

    ![結果清單中的 Zscaler One](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>將使用者指派給 Zscaler One

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，會同步處理只的使用者或群組指派給 Azure AD 中的應用程式。

您設定並啟用自動使用者佈建之前，決定哪些使用者或群組在 Azure AD 中的需要存取 Zscaler One。 若要將這些使用者或群組指派給 Zscaler One，請遵循[將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)。

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>將使用者指派給 Zscaler One 的重要秘訣

* 我們建議您指派單一 Azure AD 使用者給 Zscaler One 測試自動使用者佈建的設定。 稍後可以指派額外的使用者或群組。

* 當您將使用者指派給 Zscaler One 時，請選取任何有效的應用程式特有角色，如果有的話，在指派對話方塊中。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>設定自動使用者佈建到 Zscaler One

本節會引導您完成設定 Azure AD 佈建服務的步驟。 您可以使用它來建立、 更新和停用的 Zscaler One 根據使用者或群組指派 Azure AD 中使用者或群組。

> [!TIP]
> 您也可以啟用 SAML 型單一登入 Zscaler one。 請依照下列中的指示[Zscaler One 單一登入教學課程](zscaler-One-tutorial.md)。 單一登入可以獨立設定自動使用者佈建，雖然這兩個功能彼此補充。

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>設定 Azure ad 自動使用者佈建的 Zscaler One

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式** > **所有應用程式** > **Zscaler One**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zscaler One]。

    ![應用程式清單中的 [Zscaler One] 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![Zscaler One 佈建](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Zscaler 的一種佈建模式](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. 底下**系統管理員認證**區段中，填寫**租用戶 URL**並**祕密權杖**方塊以設定您 Zscaler one 帳戶步驟 6 中所述。

6. 若要取得租用戶 URL 」 及 「 祕密權杖，請前往**Administration** > **驗證設定**Zscaler One 的入口網站 UI 中。 底下**驗證類型**，選取**SAML**。

    ![Zscaler One 的驗證設定](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. 選取 **設定 SAML**來開啟**設定 SAML**選項。

    ![Zscaler One 設定 SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. 選取  **Enable SCIM-Based 佈建**取得設定**基底 URL**並**持有人權杖**。 然後儲存設定。 複製**基底 URL**設為**租用戶 URL**在 Azure 入口網站中。 複製**持有人權杖**設為**祕密權杖**在 Azure 入口網站中。

7. 您在步驟 5 所示的方塊中，填滿之後，請選取**測試連接**以確定 Azure AD 可以連線到 Zscaler One。 如果連線失敗，請確定您的 Zscaler One 帳戶具有管理員權限，並再試一次。

    ![Zscaler 一項測試連接](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. 在 **通知電子郵件**方塊中，輸入人員的電子郵件地址或群組以接收佈建錯誤通知。 選取 **發生失敗時傳送電子郵件通知**核取方塊。

    ![Zscaler One 通知電子郵件](./media/zscaler-one-provisioning-tutorial/notification.png)

9. 選取 [ **儲存**]。

10. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 Zscaler One**。

    ![Zscaler One 使用者同步處理](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. 檢閱從 Azure AD 同步至 Zscaler One 中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對的使用者帳戶在 Zscaler One，進行更新作業。 若要儲存任何變更，請選取**儲存**。

    ![Zscaler One 相符的使用者屬性](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. 底下**對應**區段中，選取**同步處理 Azure Active Directory 群組至 Zscaler One**。

    ![Zscaler One 的群組同步處理](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. 檢閱從 Azure AD 同步至 Zscaler One 中的群組屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對的 Zscaler One 中進行更新作業的群組。 若要儲存任何變更，請選取**儲存**。

    ![Zscaler One 相符的群組屬性](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. 若要設定範圍篩選器，請依照下列中的指示[範圍篩選教學課程](./../active-directory-saas-scoping-filters.md)。

15. 若要啟用 Azure AD 中佈建服務 Zscaler One**設定**區段中，變更**佈建狀態**來**上**。

    ![Zscaler 的一種佈建狀態](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. 要佈建到 Zscaler One 中定義的使用者或您想要的群組。 在 **設定**區段中，選取您想要在的值**範圍**。

    ![Zscaler One Scope](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. 當您準備好要佈建時時，請選取**儲存**。

    ![其中一個儲存 Zscaler](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

此作業會啟動初始同步處理所有的使用者或群組中定義**領域**中**設定**一節。 初始同步處理所花費的時間比更新的同步執行。 只要 Azure AD 佈建服務執行，它們就會發生大約每 40 分鐘。 

您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告。 報表會描述佈建服務在 Zscaler One 的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
