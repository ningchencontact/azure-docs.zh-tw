---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的 Leapsome |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到 Leapsome 的使用者帳戶。
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
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: e02deaa29b40e64b63d9afb471717feef78b3cee
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672687"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 Leapsome

本教學課程的目的是要示範 Leapsome 和 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建及取消佈建使用者和/或群組，以 Leapsome 中執行的步驟。

> [!NOTE]
>  本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
>
> 此連接器目前處於預覽狀態。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先決條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* A [Leapsome](https://www.Leapsome.com/en/pricing)租用戶。
* 中的使用者帳戶 Leapsome 具有管理員權限。

## <a name="assigning-users-to-leapsome"></a>將使用者指派給 Leapsome

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，只有使用者和/或已指派給 Azure AD 中的應用程式的群組同步處理。

之前設定，並啟用自動使用者佈建，您應先決定哪些使用者和/或 Azure AD 中的群組需要存取 Leapsome。 一旦決定後，您可以指派這些使用者和/或群組至 Leapsome 依照此處的指示：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>將使用者指派給 Leapsome 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Leapsome，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 當將使用者指派給 Leapsome，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。


## <a name="setup-leapsome-for-provisioning"></a>安裝程式 Leapsome 佈建

1. 登入您[Leapsome Admin Console](https://www.Leapsome.com/app/#/login)。 瀏覽至**設定 > 系統管理員設定**。

    ![Leapsome 系統管理員主控台](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  瀏覽至**整合 > SCIM 使用者佈建**。

    ![Leapsome 新增 SCIM](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  複製**SCIM 驗證語彙基元**。 這個值就 Leapsome 應用程式在 Azure 入口網站中的 [佈建] 索引標籤中的 [祕密權杖] 欄位中輸入。

    ![Leapsome 建立權杖](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>從資源庫新增 Leapsome

設定之前 Leapsome 自動化使用者佈建與 Azure AD，您必須從 Azure AD 應用程式資源庫將 Leapsome 新增到受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增 Leapsome，請執行下列步驟：**

1. 在   **[Azure 入口網站](https://portal.azure.com)** ，在左側的導覽窗格中，選取**Azure Active Directory**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取**新的應用程式**窗格頂端的按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**Leapsome**，選取**Leapsome**在結果窗格中，然後按一下**新增**按鈕以新增應用程式。

    ![結果清單中的 Leapsome](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>設定自動使用者佈建至 Leapsome 

本節將引導您逐步設定 Azure AD 佈建服務來建立、 更新和停用使用者和/或群組 Leapsome 中的根據 Azure AD 中使用者和/或群組指派。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入的 Leapsome，下列指示中提供[Leapsome 單一登入教學課程](Leapsome-tutorial.md)。 單一登入可以獨立設定自動使用者佈建，雖然這兩個功能彼此補充

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建的 Leapsome:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式**，然後選取**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Leapsome]  。

    ![應用程式清單中的 Leapsome 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![佈建 索引標籤](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![佈建 索引標籤](common/provisioning-automatic.png)

5. 底下**系統管理員認證**區段中，輸入`https://www.leapsome.com/api/scim`中**租用戶 URL**。 輸入**SCIM 驗證語彙基元**稍早擷取的值**祕密權杖**。 按一下 **測試連接**以確保 Azure AD 可以連線到 Leapsome。 如果連線失敗，請確定 Leapsome 帳戶具有管理員權限，並再試一次。

    ![租用戶 URL + 語彙基元](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]  。

8. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 Leapsome**。

    ![Leapsome 使用者對應](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. 檢閱從 Azure AD 同步至 Leapsome 中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對 Leapsome 中的使用者帳戶進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Leapsome 使用者屬性](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. 底下**對應**區段中，選取**同步處理 Azure Active Directory 群組至 Leapsome**。

    ![Leapsome 群組對應](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. 檢閱從 Azure AD 同步至 Leapsome 中的群組屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對中 Leapsome 進行更新作業的群組。 選取 [儲存]  按鈕以認可所有變更。

    ![Leapsome 群組屬性](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Azure AD 佈建服務 Leapsome，變更**佈建狀態**要**上**中**設定**一節。

    ![佈建狀態開啟](common/provisioning-toggle-on.png)

14. 藉由選擇需要的值中定義的使用者和/或您想要的群組要佈建到 Leapsome**領域**中**設定**一節。

    ![佈建的範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]  。

    ![正在儲存佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建服務 Leapsome 上的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* 需要 Leapsome **userName**是唯一的。
* Leapsome 只允許儲存的工作電子郵件地址。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
