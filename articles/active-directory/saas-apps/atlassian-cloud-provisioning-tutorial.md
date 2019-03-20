---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的 Atlassian Cloud |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到 Atlassian Cloud 使用者帳戶。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2019
ms.author: v-ant
ms.openlocfilehash: ff6a111663df5d7e7f4f517446084ef75b009e59
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092696"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 Atlassian Cloud

本教學課程的目的是要示範的步驟，以在 Atlassian Cloud 與 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建和取消佈建使用者和/或群組將 Atlassian Cloud 中執行。 

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
> 
> 此連接器目前處於公開預覽狀態。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

*   Azure AD 租用戶
*   [Atlassian Cloud 租用戶](https://www.atlassian.com/licensing/cloud)
*   使用者帳戶在 Atlassian Cloud 中具有管理員權限。

> [!NOTE]
> Azure AD 佈建整合仰賴**Atlassian Cloud SCIM API**，這是適用於 Atlassian Cloud 的團隊。

## <a name="adding-atlassian-cloud-from-the-gallery"></a>從資源庫新增 Atlassian Cloud
在之前設定自動使用者佈建 Azure AD 與 Atlassian Cloud，您需要從 Azure AD 應用程式資源庫將 Atlassian Cloud 新增到受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增 Atlassian Cloud，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式] > [所有應用程式]。

    ![企業應用程式區段][2]
    
3. 若要新增 Atlassian Cloud，請按一下**新的應用程式**對話方塊頂端的按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Atlassian Cloud**。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/app-search.png)

5. 在結果窗格中，選取**Atlassian Cloud**，然後按一下**新增**按鈕將 Atlassian Cloud 新增至您的 SaaS 應用程式的清單。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/app-create.png)

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/app-instance.png)

## <a name="assigning-users-to-atlassian-cloud"></a>將使用者指派給 Atlassian Cloud

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。 

之前設定，並啟用自動使用者佈建，您應該決定哪些使用者和/或 Azure AD 中的群組需要存取 Atlassian Cloud。 一旦決定後，您可以指派這些使用者和/或群組將 Atlassian Cloud 依照此處的指示：

*   [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>將使用者指派給 Atlassian Cloud 的重要秘訣

*   建議將單一 Azure AD 使用者指派給 Atlassian Cloud，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

*   當將使用者指派給 Atlassian Cloud 中，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>設定自動使用者佈建到 Atlassian Cloud 

本節將引導您逐步設定 Azure AD 佈建服務來建立、 更新和停用使用者和/或群組在 Atlassian Cloud 中根據使用者和/或群組指派 Azure AD 中。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入 Atlassian Cloud 的下列指示中提供[Atlassian Cloud 單一登入教學課程](atlassian-cloud-tutorial.md)。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建的 Atlassian Cloud:

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 [Azure Active Directory] > [企業應用程式] > [所有應用程式]。

2. 您可以選取 Atlassian Cloud 從您的 SaaS 應用程式清單。
 
    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/application-instance-search.png)

3. 選取 [佈建] 索引標籤。
    
    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. 底下**系統管理員認證**區段中，輸入**租用戶 URL**並**祕密權杖**的 Atlassian Cloud 的帳戶。 這些值的範例如下：

   * 在 **租用戶 URL**欄位中，填滿您收到 Atlassian 的特定租用戶端點，如步驟 6 中所述。 例如： **https://api.atlassian.com/scim/directory/{directoryId}**

   * 在 [秘密權杖] 欄位中，填入秘密權杖，如同步驟 6 所述。

6. 瀏覽至[Atlassian 組織管理員](https://admin.atlassian.com) **> 使用者佈建**，然後按一下**建立語彙基元**。 複製**Directory 基底 URL**並**持有人權杖**來**租用戶 URL**並**祕密權杖**欄位中，分別。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)
    
    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. 填寫後在步驟 5 中所顯示的欄位，按一下**測試連接**以確保 Azure AD 可以連線到 Atlassian Cloud。 如果連線失敗，請確定您的 Atlassian Cloud 帳戶具有系統管理員權限並再試一次。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)
    
8. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. 按一下 [檔案] 。

10. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 Atlassian Cloud**。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. 檢閱從 Azure AD 同步至 Atlassian Cloud 中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對進行更新作業的 Atlassian Cloud 中的使用者帳戶。 選取 [儲存] 按鈕以認可所有變更。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. 底下**對應**區段中，選取**同步處理 Azure Active Directory 群組至 Atlassian Cloud**。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. 檢閱從 Azure AD 同步至 Atlassian Cloud 中的群組屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對在 Atlassian Cloud 中的群組進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

15. 若要啟用 Azure AD 佈建服務 Atlassian Cloud，請變更**佈建狀態**要**上**中**設定**一節。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. 藉由選擇需要的值中定義的使用者和/或您想要的群組要佈建到 Atlassian Cloud**領域**中**設定**一節。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. 當您準備好要佈建時，按一下 [儲存]。

    ![佈建的 Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/save.png)


此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建服務在 Atlassian Cloud 的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Atlassian Cloud 可讓使用者只能從佈建[已驗證網域](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)。
* Atlassian Cloud 不目前不支援群組重新命名。 這表示不會更新和反映在 Atlassian Cloud 中的 Azure AD 中的群組顯示名稱的任何變更。
* 值**郵件**如果使用者具有 Microsoft Exchange 信箱，才會填入 Azure AD 中的使用者屬性。 如果使用者沒有其中一個，則建議將對應至不同的所需的屬性**電子郵件**Atlassian Cloud 中的屬性。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
