---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的 Zscaler |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到 Zscaler 使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 3ea502477cc5b380c99a183d9270c2b2e94375a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67049201"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 Zscaler

本教學課程的目的是要示範在 Zscaler 與 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建及取消佈建使用者和/或群組至 Zscaler 中執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../active-directory-saas-app-provisioning.md)。
>

> 此連接器目前為公開預覽版。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

* Azure AD 租用戶
* Zscaler 租用戶
* Zscaler 中具有系統管理員權限的使用者帳戶

> [!NOTE]
> Azure AD 佈建整合依賴 Zscaler SCIM API，這是具有企業套件之帳戶的 Zscaler 開發人員可使用。

## <a name="adding-zscaler-from-the-gallery"></a>從資源庫新增 Zscaler

設定 Zscaler 為之前自動使用者佈建與 Azure AD，您必須從 Azure AD 應用程式資源庫將 Zscaler 新增到受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增 Zscaler，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Zscaler**，並從結果面板中選取 [Zscaler]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 Zscaler](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>將使用者指派給 Zscaler

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。

之前設定，並啟用自動使用者佈建，您應先決定哪些使用者和/或 Azure AD 中的群組需要存取 Zscaler。 一旦決定後，您可以指派這些使用者和/或群組至 Zscaler 依照此處的指示：

* [將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>將使用者指派給 Zscaler 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Zscaler，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 當將使用者指派給 Zscaler，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>設定自動使用者佈建到 Zscaler

本節將引導您逐步設定 Azure AD 佈建服務來建立、 更新和停用使用者和/或群組 Zscaler 中的根據 Azure AD 中使用者和/或群組指派。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入 zscaler，下列指示中提供[Zscaler 單一登入教學課程](zscaler-tutorial.md)。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建 zscaler:

1. 登入[Azure 入口網站](https://portal.azure.com)，然後選取**企業應用程式**，選取**所有應用程式**，然後選取**Zscaler**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zscaler]  。

    ![應用程式清單中的 Zscaler 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. 底下**系統管理員認證**區段中，輸入**租用戶 URL**並**祕密權杖**Zscaler 帳戶步驟 6 中所述。

6. 若要取得**租用戶 URL**並**祕密權杖**，瀏覽至**管理 > 驗證設定**Zscaler 入口網站使用者介面和中的，按一下  **SAML**底下**驗證類型**。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    按一下 **設定 SAML**來開啟**設定 SAML**選項。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    選取  **Enable SCIM-Based 佈建**來擷取**基底 URL**並**持有人權杖**，然後儲存該設定。 複製**基底 URL**要**租用戶 URL**，並**持有人權杖**至**祕密權杖**在 Azure 入口網站中。

7. 填寫後在步驟 5 中所顯示的欄位，按一下**測試連接**以確保 Azure AD 可以連線至 Zscaler。 如果連線失敗，請確定您的 Zscaler 帳戶具有系統管理員權限並再試一次。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/test-connection.png)

8. 在 [通知電子郵件]  欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/notification.png)

9. 按一下 [檔案]  。

10. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 Zscaler**。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. 檢閱從 Azure AD 同步至 Zscaler 中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對的使用者帳戶在 Zscaler 中，進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. 底下**對應**區段中，選取**同步處理 Azure Active Directory 群組至 Zscaler**。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. 檢閱從 Azure AD 同步至 Zscaler 中的群組屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對在 Zscaler 中進行更新作業的群組。 選取 [儲存]  按鈕以認可所有變更。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](./../active-directory-saas-scoping-filters.md)中提供的下列指示。

15. 若要啟用 Azure AD 佈建服務 Zscaler，請變更**佈建狀態**要**上**中**設定**一節。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. 藉由選擇需要的值中定義的使用者和/或您想要的群組要佈建到 Zscaler**領域**中**設定**一節。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/scoping.png)

17. 當您準備好要佈建時，按一下 [儲存]  。

    ![Zscaler 佈建](./media/zscaler-provisioning-tutorial/save-provisioning.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建服務，在 Zscaler 上的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
