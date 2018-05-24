---
title: 教學課程︰設定 xMatters OnDemand 以使用 Azure Active Directory 自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 以對 xMatters OnDemand 自動佈建及取消佈建使用者帳戶。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: v-ant-msft
ms.openlocfilehash: 0f1cadfb7c78c32071083d5e2efa3299ba08fb3a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32161381"
---
# <a name="tutorial-configure-xmatters-ondemand-for-automatic-user-provisioning"></a>教學課程︰設定 xMatters OnDemand 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 xMatters OnDemand 自動佈建及取消佈建使用者和/或群組時，要在 xMatters OnDemand 與 Azure Active Directory (Azure AD) 中執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](./active-directory-saas-app-provisioning.md)。

## <a name="prerequisites"></a>先決條件

本教學課程中說明的案例假設您已經具有下列項目：

*   Azure AD 租用戶
*   已啟用[入門](https://www.xmatters.com/pricing) \(英文\) 方案或更好方案的 xMatters OnDemand 租用戶 
*   xMatters OnDemand 中具有系統管理員權限的使用者帳戶

> [!NOTE]
> Azure AD 佈建整合仰賴 [xMatters OnDemand API](https://help.xmatters.com/xmAPI) \(英文\)，此 API 僅提供給入門方案或更好方案中的 xMatters OnDemand 小組使用。

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>從資源庫新增 xMatters OnDemand

將 xMatters OnDemand 設定為使用 Azure AD 自動佈建使用者之前，您必須將 xMatters OnDemand 從 Azure AD 應用程式庫新增到您的受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 xMatters OnDemand，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式] > [所有應用程式]。

    ![企業應用程式區段][2]
    
3. 若要新增 xMatters OnDemand，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **xMatters OnDemand**。

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearch.png)

5. 在結果窗格中，選取 [xMatters OnDemand]，然後按一下 [新增] 按鈕，將 xMatters OnDemand 新增到您的 SaaS 應用程式清單。

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-xmatters-ondemand"></a>將使用者指派給 xMatters OnDemand

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應該先決定 Azure AD 中的哪些使用者和/或群組需要存取 xMatters OnDemand。 一旦決定後，您就能依照此處的指示，將這些使用者和/或群組指派給 xMatters OnDemand：

*   [將使用者或群組指派給企業應用程式](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-xmatters-ondemand"></a>將使用者指派給 xMatters OnDemand 的重要祕訣

*   建議將單一 Azure AD 使用者指派給 xMatters OnDemand，以測試自動使用者佈建設定。 其他使用者及/或群組可能會稍後再指派。

*   將使用者指派給 xMatters OnDemand 時，您必須在指派對話方塊中選取任何有效的應用程式特定角色 (如果有的話)。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-xmatters-ondemand"></a>對 xMatters OnDemand 設定自動使用者佈建 

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 xMatters OnDemand 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以選擇為 xMatters OnDemand 啟用 SAML 型單一登入，請遵循 [xMatters OnDemand 單一登入教學課程](active-directory-saas-xmatters-ondemand-tutorial.md)中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-xmatters-ondemand-in-azure-ad"></a>在 Azure AD 中為 xMatters OnDemand 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 [Azure Active Directory] > [企業應用程式] > [所有應用程式]。

2. 從您的 SaaS 應用程式清單選取 xMatters OnDemand。
 
    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ApplicationInstanceSearch.png)

3. 選取 [佈建] 索引標籤。
    
    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 [管理員認證] 區段下，輸入 xMatters OnDemand 帳戶的**管理員使用者名稱**、**管理員密碼**和**網域**。

    *   在 [管理員使用者名稱] 欄位中，填入 xMatters OnDemand 租用戶中系統管理帳戶的使用者名稱。 範例：admin@contoso.com。

    *   在 [管理員密碼] 欄位中，填入與管理員使用者名稱相對應的密碼。

    *   在 [網域] 欄位中，填入您的 xMatters OnDemand 租用戶的子網域。
    範例：針對租用戶 URL 為 https://my-tenant.xmatters.com 的帳戶，您的子網域會是 **my-tenant**。

6. 如步驟 5 所示填寫欄位後，按一下 [測試連線]，以確保 Azure AD 可以連線到 xMatters OnDemand。 如果連線失敗，請確定您的 xMatters OnDemand 帳戶具有系統管理員權限，然後再試一次。

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/TestConnection.png)
    
7. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/EmailNotification.png)

8. 按一下 [檔案]。

9. 在 [對應] 區段下方，選取 [將 Azure Active Directory 使用者同步至 xMatters OnDemand]。

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncUsers.png)

10. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 xMatters OnDemand 的使用者屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 xMatters OnDemand 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/UserAttributes.png)

11. 在 [對應] 區段下方，選取 [將 Azure Active Directory 群組同步至 xMatters OnDemand]。

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncGroups.png)

12. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 xMatters OnDemand 的群組屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 xMatters OnDemand 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/GroupAttributes.png)

13. 若要設定範圍篩選，請參閱[範圍篩選教學課程](./active-directory-saas-scoping-filters.md)中提供的下列指示。

14. 若要為xMatters OnDemand 啟用 Azure AD 佈建服務，請將 [設定] 區段中的 [佈建狀態] 變更為 [開啟]。

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningStatus.png)

15. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，定義要佈建到 xMatters OnDemand 的使用者和/或群組。

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ScopeSelection.png)

16. 當您準備好要佈建時，按一下 [儲存]。

    ![xMatters OnDemand 佈建](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SaveProvisioning.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告，其中會描述 xMatters OnDemand 上 Azure AD 佈建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](./active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](active-directory-enterprise-apps-manage-provisioning.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>後續步驟

* [了解如何針對佈建活動檢閱記錄和取得報告](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_03.png
