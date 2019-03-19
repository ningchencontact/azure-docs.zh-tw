---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的 Zscaler Two |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到 Zscaler Two 兩個使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2019
ms.author: v-ant-msft
ms.openlocfilehash: 797804be2588fb5c04c052c6f14c5b2b51146c32
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58106503"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 Zscaler Two

本教學課程的目的是要示範在 Zscaler Two 與 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建和取消佈建使用者和/或群組至 Zscaler Two 中執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../active-directory-saas-app-provisioning.md)。
> 
> 此連接器目前處於公開預覽狀態。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

*   Azure AD 租用戶
*   Zscaler Two 租用戶
*   在 Zscaler Two 使用者帳戶具有管理員權限

> [!NOTE]
> Azure AD 佈建整合依賴 Zscaler 兩個 SCIM API，這是提供給 Zscaler Two 的開發人員與企業封裝的帳戶。

## <a name="adding-zscaler-two-from-the-gallery"></a>從資源庫新增 Zscaler Two
設定自動化使用者佈建 Azure AD 與 Zscaler Two 時，您需要 Zscaler Two 從 Azure AD 應用程式庫新增到受控 SaaS 應用程式清單。

**新增 Zscaler Two 的 Azure AD 應用程式庫，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式] > [所有應用程式]。

    ![企業應用程式區段][2]

3. 若要新增 Zscaler Two，請按一下**新的應用程式**對話方塊頂端的按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Zscaler Two**。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/app-search.png)

5. 在結果窗格中，選取**Zscaler Two**，然後按一下**新增**按鈕以新增至您的 SaaS 應用程式清單的 Zscaler Two。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/app-search-results.png)

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/app-creation.png)

## <a name="assigning-users-to-zscaler-two"></a>將使用者指派給 Zscaler Two

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。

之前設定，並啟用自動使用者佈建，您應該決定哪些使用者和/或 Azure AD 中的群組需要存取 Zscaler Two。 一旦決定後，您可以指派這些使用者和/或群組給 Zscaler Two 依照此處的指示：

*   [將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>將使用者指派給 Zscaler Two 的重要秘訣

*   建議將單一 Azure AD 使用者指派給 Zscaler Two，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

*   當將使用者指派給 Zscaler Two，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-zscaler-two"></a>設定自動使用者佈建到 Zscaler Two

本節會引導您完成設定 Azure AD 佈建服務以建立、 更新和停用使用者和/或群組，在 Zscaler Two 根據使用者和/或群組指派 Azure AD 中的步驟。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入 Zscaler two，下列指示中提供[Zscaler Two 單一登入教學課程](zscaler-two-tutorial.md)。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-two-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建的 Zscaler Two:

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 [Azure Active Directory] > [企業應用程式] > [所有應用程式]。

2. Zscaler Two 從清單中選取您的 SaaS 應用程式。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/app-instance-search.png)

3. 選取 [佈建] 索引標籤。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. 底下**系統管理員認證**區段中，輸入**租用戶 URL**並**祕密權杖**您的 Zscaler Two 帳戶步驟 6 中所述。

6. 若要取得**租用戶 URL**並**祕密權杖**，瀏覽至**管理 > 驗證設定**在 Zscaler Two 的入口網站使用者介面以及中的，按一下  **SAML**底下**驗證類型**。 

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)
    
    按一下 **設定 SAML**來開啟**設定 SAML**選項。 

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)
    
    選取  **Enable SCIM-Based 佈建**來擷取**基底 URL**並**持有人權杖**，然後儲存該設定。 複製**基底 URL**要**租用戶 URL**並**持有人權杖**至**祕密權杖**在 Azure 入口網站中。

7. 填寫後在步驟 5 中所顯示的欄位，按一下**測試連接**以確保 Azure AD 可以連線到 Zscaler Two。 如果連線失敗，請確定您 Zscaler Two 的帳戶具有系統管理員權限並再試一次。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/test-connection.png)
    
8. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/notification.png)

9. 按一下 [檔案] 。

10. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 Zscaler Two**。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. 檢閱從 Azure AD 同步至 Zscaler Two 中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對的使用者帳戶在 Zscaler Two，進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. 底下**對應**區段中，選取**同步處理 Azure Active Directory 群組至 Zscaler Two**。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. 檢閱從 Azure AD 同步至 Zscaler Two 中的群組屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對的 Zscaler Two 中進行更新作業的群組。 選取 [儲存] 按鈕以認可所有變更。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](./../active-directory-saas-scoping-filters.md)中提供的下列指示。

15. 若要啟用 Azure AD 佈建服務的 Zscaler Two，請變更**佈建狀態**要**上**中**設定**一節。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. 藉由選擇需要的值中定義的使用者和/或您想要的群組要佈建到 Zscaler Two**領域**中**設定**一節。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. 當您準備好要佈建時，按一下 [儲存]。

    ![Zscaler Two 佈建](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建服務在 Zscaler Two 的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png
