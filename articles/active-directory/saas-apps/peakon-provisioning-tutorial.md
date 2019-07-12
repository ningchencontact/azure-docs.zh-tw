---
title: 教學課程：設定 Peakon 自動使用者佈建與 Azure Active Directory |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到 Peakon 的使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 2547f34432ca1b4b52f34c343bb4aad2f2407f53
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673141"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 Peakon

本教學課程的目的是要示範 Peakon 和 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建及取消佈建使用者和/或群組，以 Peakon 中執行的步驟。

> [!NOTE]
>  本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
>
> 此連接器目前處於預覽狀態。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
## <a name="prerequisites"></a>先決條件

在本教學課程中所述的案例假設您已經具備下列必要條件

* Azure AD 租用戶。
* [Peakon 租用戶](https://peakon.com/us/pricing/)。
* 中的使用者帳戶 Peakon 具有管理員權限。

## <a name="assigning-users-to-peakon"></a>將使用者指派給 Peakon

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，只有使用者和/或已指派給 Azure AD 中的應用程式的群組同步處理。

之前設定，並啟用自動使用者佈建，您應先決定哪些使用者和/或 Azure AD 中的群組需要存取 Peakon。 一旦決定後，您可以指派這些使用者和/或群組至 Peakon 依照此處的指示：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>將使用者指派給 Peakon 的重要秘訣 

* 建議將單一 Azure AD 使用者指派給 Peakon，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 當將使用者指派給 Peakon，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="set-up-peakon-for-provisioning"></a>設定 Peakon 佈建

1.  登入您[Peakon Admin Console](https://app.Peakon.com/login)。 按一下 **組態**。 

    ![Peakon 系統管理員主控台](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  選取 **整合**。
    
    ![Peakon 員工-佈建](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  啟用**佈建的員工**。

    ![Peakon 員工-佈建](media/Peakon-provisioning-tutorial/peakon05.png)

4.  值複製**SCIM 2.0 URL**並**OAuth 持有人權杖**。 這些值將會進入**租用戶 URL**並**祕密權杖**Peakon 應用程式在 Azure 入口網站中的 [佈建] 索引標籤中的欄位。

    ![Peakon 建立權杖](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>從資源庫新增 Peakon

若要設定自動使用者佈建與 Azure AD Peakon，您需要從 Azure AD 應用程式資源庫將 Peakon 新增到受控 SaaS 應用程式清單。

1. 在   **[Azure 入口網站](https://portal.azure.com)** ，在左側的導覽窗格中，選取**Azure Active Directory**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取**新的應用程式**窗格頂端的按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**Peakon**，選取**Peakon**在結果窗格中，然後按一下**新增**按鈕以新增應用程式。

    ![結果清單中的 Peakon](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>設定自動使用者佈建至 Peakon 

本節將引導您逐步設定 Azure AD 佈建服務來建立、 更新和停用使用者和/或群組 Peakon 中的根據 Azure AD 中使用者和/或群組指派。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入的 Peakon，下列指示中提供[Peakon 單一登入教學課程](peakon-tutorial.md)。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建的 Peakon:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式**，然後選取**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取[Peakon]  。

    ![應用程式清單中的 [Peakon] 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![佈建 索引標籤](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![佈建 索引標籤](common/provisioning-automatic.png)

5. 底下**系統管理員認證**區段中，輸入**SCIM 2.0 URL**並**OAuth 持有人權杖**中稍早擷取的值**租用戶 URL**並**祕密權杖**分別。 按一下 **測試連接**以確保 Azure AD 可以連線到 Peakon。 如果連線失敗，請確定 Peakon 帳戶具有管理員權限，並再試一次。

    ![租用戶 URL + 語彙基元](common/provisioning-testconnection-tenanturltoken.png)

7. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

8. 按一下 [儲存]  。

9. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 Peakon**。

    ![Peakon 使用者對應](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. 檢閱從 Azure AD 同步至 Peakon 中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對 Peakon 中的使用者帳戶進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Peakon 使用者屬性](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. 若要設定範圍篩選器，請參閱下列指示中提供[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)。
    
    ![佈建的範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]  。

    ![正在儲存佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建服務 Peakon 上的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Peakon 中的所有自訂使用者屬性具有從 Peakon 的自訂 SCIM 使用者延伸模組的延伸`urn:ietf:params:scim:schemas:extension:peakon:2.0:User`。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)