---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的 Zscaler 三個 |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到 Zscaler 三個使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: ed158ae825ec8aac24a57eb0f5a986b2124b66fb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273680"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 Zscaler 三個

本教學課程的目的是要示範執行在 Zscaler 三個和 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建和取消佈建使用者和/或群組至 Zscaler 三個步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../active-directory-saas-app-provisioning.md)。
>
> 此連接器目前處於公開預覽狀態。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

* Azure AD 租用戶
* Zscaler 三個租用戶
* 在 Zscaler 三個使用者帳戶具有管理員權限

> [!NOTE]
> Azure AD 佈建整合依賴 Zscaler 三個 SCIM API，這是具有企業套件之帳戶的 Zscaler 三個開發人員可使用。

## <a name="adding-zscaler-three-from-the-gallery"></a>從資源庫新增 Zscaler Three

設定 Zscaler 三個為之前自動使用者佈建與 Azure AD，您需要從將新增 Zscaler 三個 Azure AD 應用程式庫至您的受控 SaaS 應用程式清單。

**新增三個 Zscaler 從 Azure AD 應用程式庫，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Zscaler Three**，並從結果面板中選取 [Zscaler Three]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Zscaler Three](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-three"></a>將使用者指派給 Zscaler 三個

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。

在設定並啟用自動使用者佈建之前, 您應該決定哪些使用者和/或 Azure AD 中的群組需要存取 Zscaler 三個。 一旦決定後，您可以指派這些使用者和/或群組至 Zscaler 三個依照此處的指示：

* [將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>將使用者指派給 Zscaler 三個重要的秘訣

* 建議將單一 Azure AD 使用者指派給 Zscaler 三個，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 當將使用者指派給 Zscaler 三個，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-zscaler-three"></a>設定自動使用者佈建到 Zscaler 三個

本節會引導您完成設定 Azure AD 佈建服務以建立、 更新和停用使用者和/或群組，在 Zscaler 三種根據使用者和/或群組指派 Azure AD 中的步驟。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入的 Zscaler 三個，下列指示中提供[三個 Zscaler 單一登入教學課程](zscaler-three-tutorial.md)。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-three-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建的 Zscaler 三種：

1. 登入[Azure 入口網站](https://portal.azure.com)，然後選取**企業應用程式**，選取**所有應用程式**，然後選取**Zscaler 三個**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zscaler Three]。

    ![應用程式清單中的 Zscaler 三個連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. 底下**系統管理員認證**區段中，輸入**租用戶 URL**並**祕密權杖**Zscaler 三個帳戶步驟 6 中所述。

6. 若要取得**租用戶 URL**並**祕密權杖**，瀏覽至**管理 > 驗證設定**Zscaler 三個入口網站使用者介面和中的，按一下  **SAML**底下**驗證類型**。 

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    按一下 **設定 SAML**來開啟**設定 SAML**選項。

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    選取  **Enable SCIM-Based 佈建**來擷取**基底 URL**並**持有人權杖**，然後儲存該設定。 複製**基底 URL**要**租用戶 URL**並**持有人權杖**至**祕密權杖**在 Azure 入口網站中。

7. 填寫後在步驟 5 中所顯示的欄位，按一下**測試連接**以確保 Azure AD 可以連線到 Zscaler 三個。 如果連線失敗，請確定您的 Zscaler 三個帳戶具有系統管理員權限並再試一次。

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/notification.png)

9. 按一下 [檔案] 。

10. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 Zscaler 三個**。

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. 檢閱從 Azure AD 同步至 Zscaler 三個中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對的使用者帳戶在 Zscaler 三個，進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. 底下**對應**區段中，選取**同步處理 Azure Active Directory 群組至 Zscaler 三個**。

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. 檢閱從 Azure AD 同步至 Zscaler 三個中的群組屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對三個 Zscaler 中進行更新作業的群組。 選取 [儲存] 按鈕以認可所有變更。

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](./../active-directory-saas-scoping-filters.md)中提供的下列指示。

15. 若要啟用 Azure AD 佈建 Zscaler 三個服務，變更**佈建狀態**要**上**中**設定**一節。

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. 藉由選擇需要的值，在要佈建到 Zscaler 三個定義的使用者和/或您想要的群組**領域**中**設定**一節。

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. 當您準備好要佈建時，按一下 [儲存]。

    ![Zscaler 三佈建](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建在 Zscaler 三個服務的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的佈建的使用者帳戶](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [了解如何檢閱記錄檔，並取得有關佈建活動報告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
