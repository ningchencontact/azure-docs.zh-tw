---
title: 教學課程：自動使用者佈建與 Azure Active Directory 設定 Dropbox for Business |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建至 Dropbox for Business 的使用者帳戶。
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
ms.date: 05/20/2019
ms.author: zchia
ms.openlocfilehash: c95346ff9026b7fc8c9141234fb3cde9c0809d23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053427"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>教學課程：設定 Dropbox for Business 來自動佈建使用者

本教學課程的目的是要示範執行在 Dropbox for Business 與 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建及取消佈建使用者和/或群組至 Dropbox for Business 中的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Dropbox for Business 租用戶](https://www.dropbox.com/business/pricing)
* 系統管理員權限與 Dropbox for Business 中使用者帳戶。

## <a name="add-dropbox-for-business-from-the-gallery"></a>從資源庫新增 Dropbox for Business

自動化使用者佈建與 Azure AD 中設定 Dropbox for Business 之前，您必須從 Azure AD 應用程式資源庫將 Dropbox for Business 新增到受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增 Dropbox for Business，請執行下列步驟：**

1. 在   **[Azure 入口網站](https://portal.azure.com)** ，在左側的導覽窗格中，選取**Azure Active Directory**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取**新的應用程式**窗格頂端的按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**Dropbox for Business**，選取**Dropbox for Business**在結果窗格中，然後按一下**新增**按鈕以新增應用程式。

    ![結果清單中的商務用 Dropbox](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>將使用者指派至 Dropbox for Business

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，只有使用者和/或已指派給 Azure AD 中的應用程式的群組同步處理。

在設定並啟用自動使用者佈建之前, 您應該決定哪些使用者和/或 Azure AD 中的群組需要存取 Dropbox for Business。 一旦決定後，您可以指派這些使用者和/或群組至 Dropbox for Business 依照此處的指示：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>將使用者指派給 Dropbox for Business 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Dropbox for Business，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 指派使用者給 Dropbox for Business 時，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>設定自動使用者佈建至 Dropbox for Business 

本節將引導您逐步設定 Azure AD 佈建服務來建立、 更新和停用使用者和/或 Dropbox for Business 中的群組根據 Azure AD 中使用者和/或群組指派。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入 dropbox for Business，下列指示中提供[Dropbox for Business 單一登入教學課程](dropboxforbusiness-tutorial.md)。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建 dropbox for Business:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式**，然後選取**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Dropbox for Business]  。

    ![應用程式清單中的商務用 Dropbox](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![佈建 索引標籤](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![佈建 索引標籤](common/provisioning-automatic.png)

5. 在 [系統管理員認證]  區段下，按一下 [授權]  。 會在新的瀏覽器視窗中開啟 Dropbox for Business 對話方塊。

    ![佈建 ](common/provisioning-oauth.png)

6. 在 [**登入 Dropbox for Business 與 Azure AD 連結**] 對話方塊中，登入您的 Dropbox for Business 租用戶，並確認您的身分識別。

    ![Dropbox for Business 登入](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. 完成步驟 5 和 6，按一下**測試連接**以確保 Azure AD 可以連線至 Dropbox for Business。 如果連線失敗，請確定您的 Dropbox for Business 帳戶具有系統管理員權限並再試一次。

    ![token](common/provisioning-testconnection-oauth.png)

8. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [檔案]  。

10. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 Dropbox**。

    ![Dropbox 使用者對應](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. 檢閱從 Azure AD 同步至 Dropbox 中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對 Dropbox 中的使用者帳戶進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Dropbox 使用者屬性](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. 底下**對應**區段中，選取**同步處理 Azure Active Directory 群組至 Dropbox**。

    ![Dropbox 群組對應](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. 檢閱從 Azure AD 同步至 Dropbox 中的群組屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對 Dropbox 中的群組進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Dropbox 群組屬性](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

15. 若要啟用 Azure AD 佈建服務 Dropbox，變更**佈建狀態**要**上**中**設定**一節。

    ![佈建狀態開啟](common/provisioning-toggle-on.png)

16. 藉由選擇需要的值中定義的使用者和/或您想要的群組要佈建到 Dropbox**領域**中**設定**一節。

    ![佈建的範圍](common/provisioning-scope.png)

17. 當您準備好要佈建時，按一下 [儲存]  。

    ![正在儲存佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建服務在 Dropbox 上的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制
 
* Dropbox 不支援暫停受邀的使用者。 如果受邀的使用者已暫停，將會刪除該使用者。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

