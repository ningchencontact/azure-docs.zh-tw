---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 4me |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到 4me 的使用者帳戶。
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
ms.date: 06/3/2019
ms.author: zchia
ms.openlocfilehash: e2e7c27d8cfa79bc7a8f8462def4d46e598cb508
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595085"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 4me

本教學課程的目的是要示範在設定 Azure AD 自動佈建及取消佈建使用者和/或群組，以 4me 4me 和 Azure Active Directory (Azure AD) 中執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [4me 租用戶](https://www.4me.com/trial/)
* 中的使用者帳戶具有管理員權限的 4me。

## <a name="add-4me-from-the-gallery"></a>從資源庫新增 4me

設定自動使用者佈建與 Azure AD 的 4me 之前，您需要從 Azure AD 應用程式資源庫將 4me 新增到受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增 4me，請執行下列步驟：**

1. 在   **[Azure 入口網站](https://portal.azure.com)** ，在左側的導覽窗格中，選取**Azure Active Directory**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取**新的應用程式**窗格頂端的按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**4me**，選取**4me**在結果窗格中，然後按一下**新增**按鈕以新增應用程式。

    ![結果清單中的 4me](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>將使用者指派給 4me

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，只有使用者和/或已指派給 Azure AD 中的應用程式的群組同步處理。

之前設定，並啟用自動使用者佈建，您應先決定哪些使用者和/或 Azure AD 中的群組需要存取 4me。 一旦決定後，您可以指派這些使用者和/或群組至 4me 依照此處的指示：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>將使用者指派給 4me 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 4me，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 當將使用者指派給 4me，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-4me"></a>設定自動使用者佈建至 4me 

本節將引導您逐步設定 Azure AD 佈建服務來建立、 更新和停用使用者和/或 4me 根據使用者和/或群組指派 Azure ad 中的群組。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入 4me，如中所提供的指示[4me 單一登入教學課程](4me-tutorial.md)。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建的 4me:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式**，然後選取**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [4me]  。

    ![應用程式清單中的 4me 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![佈建 索引標籤](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![佈建 索引標籤](common/provisioning-automatic.png)

5. 若要擷取**租用戶 URL**並**祕密權杖**4me 帳戶、 遵循本逐步解說步驟 6 中所述。

6. 登入您 4me 系統管理員主控台。 瀏覽至**設定**。

    ![4me 設定](media/4me-provisioning-tutorial/4me01.png)

    在中輸入**應用程式**搜尋列中。

    ![4me 應用程式](media/4me-provisioning-tutorial/4me02.png)

    開啟**SCIM**下拉式清單，以擷取祕密權杖和 SCIM 端點。

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. 填寫後在步驟 5 中所顯示的欄位，按一下**測試連接**以確保 Azure AD 可以連線到 4me。 如果連線失敗，請確定 4me 帳戶具有管理員權限，並再試一次。

    ![token](common/provisioning-testconnection-tenanturltoken.png)

8. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [儲存]  。

10. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 4me**。

    ![4me 使用者對應](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. 檢閱從 Azure AD 同步至 4me 中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對 4me 進行更新作業中的使用者帳戶。 選取 [儲存]  按鈕以認可所有變更。

    ![4me 使用者對應](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. 底下**對應**區段中，選取**同步處理 Azure Active Directory 群組至 4me**。

    ![4me 使用者對應](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. 檢閱從 Azure AD 同步至 4me 中的群組屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對 4me 進行更新作業中的群組。 選取 [儲存]  按鈕以認可所有變更。

    ![4me 群組對應](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

15. 若要啟用 Azure AD 佈建服務 4me，變更**佈建狀態**要**上**中**設定**一節。

    ![佈建狀態開啟](common/provisioning-toggle-on.png)

16. 藉由選擇需要的值中定義的使用者和/或您想要的群組要佈建到 4me**領域**中**設定**一節。

    ![佈建的範圍](common/provisioning-scope.png)

17. 當您準備好要佈建時，按一下 [儲存]  。

    ![正在儲存佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建服務 4me 上的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* 4me 有不同的 SCIM 端點 Url，測試和生產環境。 先前的結尾 **.qa**雖然後者結尾 **.com**
* 4me 產生祕密權杖具有在當月產生的到期日。
* 不支援 4me**刪除**作業

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)