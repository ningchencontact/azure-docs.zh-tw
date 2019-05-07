---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的 Comeet 招募軟體 |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到 Comeet 招募軟體的使用者帳戶。
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
ms.date: 04/26/2019
ms.author: zchia
ms.openlocfilehash: 0f1b5f424a71aeccd4b1e57129c0f5b22ff158af
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159386"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 Comeet 招募軟體

本教學課程的目的是要示範 Comeet 招募軟體和 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建和取消佈建使用者和/或群組，以 Comeet 招募軟體中執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Comeet 招募軟體租用戶](https://www.comeet.co/)
* 中的使用者帳戶 Comeet 招募軟體與系統管理員權限。

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>從資源庫新增 Comeet 招募 Software

設定之前 Comeet 招募軟體自動使用者佈建與 Azure AD，您必須從 Azure AD 應用程式資源庫將 Comeet 招募 Software 新增到受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增 Comeet 招募軟體，請執行下列步驟：**

1. 在   **[Azure 入口網站](https://portal.azure.com)**，在左側的導覽窗格中，選取**Azure Active Directory**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取**新的應用程式**窗格頂端的按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**Comeet 招募軟體**，選取**Comeet 招募軟體**在結果窗格中，然後按一下**新增**按鈕以新增應用程式。

    ![結果清單中的 Comeet Recruiting Software](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>將使用者指派給 Comeet 招募軟體

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，只有使用者和/或已指派給 Azure AD 中的應用程式的群組同步處理。

在設定並啟用自動使用者佈建之前, 中，您應先決定哪些使用者和/或 Azure AD 中的群組需要存取 Comeet 招募軟體。 一旦決定後，您可以指派這些使用者和/或群組 Comeet 招募軟體依照此處的指示：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>將使用者指派給 Comeet 招募軟體的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Comeet 招募軟體，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 當將使用者指派給 Comeet 招募軟體，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>設定自動使用者佈建至 Comeet 招募軟體 

本節會引導您逐步設定 Azure AD 佈建服務來建立、 更新和停用使用者和/或 Comeet 招募軟體中的群組依據使用者和/或群組指派 Azure AD 中。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入 Comeet 招募軟體中的指示提供[Comeet 招募軟體單一登入教學課程](comeetrecruitingsoftware-tutorial.md)。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建 Comeet 招募軟體：

1. 登入[Azure 入口網站](https://portal.azure.com)，然後選取**企業應用程式**，選取**所有應用程式**，然後選取**Comeet 招募軟體**.

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Comeet Recruiting Software]。

    ![應用程式清單中的 Comeet Recruiting Software 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建 索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![佈建 索引標籤](common/provisioning-automatic.png)

5. 底下**系統管理員認證**區段中，輸入**租用戶 URL**並**祕密權杖**Comeet 招募軟體的帳戶在步驟 6 中所述。

6. 在  [Comeet 招募軟體系統管理員主控台](https://app.comeet.co/)，瀏覽至**Comeet > 設定 > 驗證 > Microsoft Azure**，並複製**祕密權杖為您的公司**值**祕密權杖**在 Azure AD 中的欄位。

    ![Comeet 徵才及用人軟體佈建](./media/comeetrecruitingsoftware-provisioning-tutorial/secret-token-1.png)
    

7. 填寫後在步驟 5 中所顯示的欄位，按一下**測試連接**以確保 Azure AD 可以連線到 Comeet 招募軟體。 如果連線失敗，請確定您 Comeet 招募軟體的帳戶具有系統管理員權限並再試一次。

    ![token](common/provisioning-testconnection-token.png)

8. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [檔案] 。

10. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 Comeet**。

    ![Comeet 徵才及用人軟體佈建](./media/comeetrecruitingsoftware-provisioning-tutorial/user-mappings.png)

11. 檢閱從 Azure AD 同步至 Comeet 招募軟體中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對 Comeet 招募軟體中的使用者帳戶進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Comeet 徵才及用人軟體佈建](./media/comeetrecruitingsoftware-provisioning-tutorial/user-mapping-attributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Azure AD 佈建服務 Comeet 招募軟體，請變更**佈建狀態**要**上**中**設定**一節。

    ![佈建狀態開啟](common/provisioning-toggle-on.png)

14. 要佈建到 Comeet 招募軟體定義的使用者和/或您想要的群組，選擇所需的值，在**領域**中**設定**一節。

    ![佈建的範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![正在儲存佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建 Comeet 招募軟體服務的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Comeet 招募軟體目前不支援群組。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
