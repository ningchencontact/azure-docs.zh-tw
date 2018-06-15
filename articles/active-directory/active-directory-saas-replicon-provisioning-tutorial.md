---
title: 教學課程︰以 Azure Active Directory 設定 Replicon 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 將使用者帳戶自動佈建及取消佈建至 Replicon。
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
ms.date: 02/20/2018
ms.author: v-ant
ms.openlocfilehash: 0ff0bcffae506e593f8ddc95886142df30164e65
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293274"
---
# <a name="tutorial-configure-replicon-for-automatic-user-provisioning"></a>教學課程︰設定 Replicon 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 Replicon 自動佈建及取消佈建使用者和/或群組時，Replicon 與 Azure Active Directory (Azure AD) 中須執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](./active-directory-saas-app-provisioning.md)。

## <a name="prerequisites"></a>先決條件

本教學課程中說明的案例假設您已經具有下列項目：

*   Azure AD 租用戶
*   已啟用具有 [Plus 方案](https://www.replicon.com/time-bill-pricing/)或更高方案的 Replicon
*   Replicon 中具有管理員權限的使用者帳戶

> [!NOTE]
> 注意︰Azure AD 佈建整合仰賴 [Replicon API](https://www.replicon.com/help/developers)，此 API 僅提供給 Plus 方案或更高方案中的 Replicon 小組使用。

## <a name="adding-replicon-from-the-gallery"></a>從資源庫新增 Replicon
將 Replicon 設定為可使用 Azure AD 自動佈建使用者之前，您必須將 Replicon 從 Azure AD 應用程式庫新增至您的受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Replicon，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式] > [所有應用程式]。

    ![企業應用程式區段][2]
    
3. 若要新增 Replicon，按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中輸入 **Replicon**。

    ![Replicon 應用程式搜尋](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearch.png)

5. 在結果窗格中，選取 [Replicon]，然後按一下 [新增] 按鈕將 Replicon 新增至您的 SaaS 應用程式清單。

    ![Replicon 搜尋結果](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearchResults.png)

    ![Replicon 建立應用程式](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppCreate.png)


## <a name="assigning-users-to-replicon"></a>將使用者指派給 Replicon

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 Replicon。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給 Replicon：

*   [將使用者或群組指派給企業應用程式](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-replicon"></a>將使用者指派給 Replicon 的重要秘訣

*   建議將單一 Azure AD 使用者指派給 Replicon，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

*   將使用者指派給 Replicon 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (如果有的話)。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-replicon"></a>對 Replicon 設定自動使用者佈建 

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Replicon 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以選擇為 Replicon 啟用 SAML 型單一登入，請遵循 [Replicon 單一登入教學課程](active-directory-saas-replicon-tutorial.md)中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能彼此補充，您可以分開設定它們。

### <a name="to-configure-automatic-user-provisioning-for-replicon-in-azure-ad"></a>在 Azure AD 中為 Replicon 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 [Azure Active Directory] > [企業應用程式] > [所有應用程式]。

2. 從您的 SaaS 應用程式清單中選取 Replicon。

    ![Replicon 佈建](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon2.png)

3. 選取 [佈建] 索引標籤。

    ![Replicon 佈建](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningTab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Replicon 佈建](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon1.png)

5. 在 [管理員認證] 區段下，輸入 Replicon 帳戶的 [管理員使用者名稱]、[管理員密碼]、[公司識別碼]、[網域]。 這些值的範例如下：

    *   在 [管理員使用者名稱] 欄位中，填入 Replicon 租用戶中管理帳戶的使用者名稱。 範例：contosoadmin。

    *   在 [管理員密碼] 欄位中，填入與管理員使用者名稱相對應的密碼。

    *   在 [公司識別碼] 欄位中，填入 Replicon 租用戶的公司識別碼。 範例：在以下的登入中，公司識別碼是 Contoso。

    ![Replicon 登入](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconLogin.png)

    *   在 [網域] 欄位中，填入步驟 6 中所述的網域。
    
6. 依據 [Replicon 服務說明](https://www.replicon.com/help/determining-the-url-for-your-service-calls) \(英文\) 中所述的步驟，取得 Replicon 租用戶帳戶的 **serviceEndpointRootURL**。 取得 URL 時，**網域**會是 **serviceEndpointRootURL** 的子網域，如強調標示的部分所示。 

    ![Replicon 佈建](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconEndpoint.png)

7. 如步驟 5 所示填寫欄位後，按一下 [測試連線] 以確認 Azure AD 可以連線到 Replicon。 如果連線失敗，請確定您的 Replicon 帳戶具有系統管理員權限並再試一次。

    ![Replicon 佈建](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconTestConnection.png)

8. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![Replicon 佈建](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconNotificationEmail.png)

9. 按一下 [檔案] 。

10. 在 [對應] 區段中，選取 [將 Azure Active Directory 使用者同步至 Replicon]。
    
    ![Replicon 佈建](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMapping.png)

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Replicon 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Replicon 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Replicon 佈建](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMappingAtrributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](./active-directory-saas-scoping-filters.md)中提供的下列指示。

13. 若要啟用 Replicon 的 Azure AD 佈建服務，在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![Replicon 佈建](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningStatus.png)

14. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Replicon 的使用者和/或群組。

    ![Replicon 佈建](./media/active-directory-saas-replicon-provisioning-tutorial/UserGroupSelection.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![Replicon 佈建](./media/active-directory-saas-replicon-provisioning-tutorial/SaveProvisioning.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告，當中會描述 Replicon 上的 Azure AD 佈建服務所執行之所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](./active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
