---
title: 教學課程：使用 Azure Active Directory 設定 Insight4GRC 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 以自動布建和取消布建使用者帳戶至 Insight4GRC。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 7e8c6c2277f29fc114033aac24844e9e85816b78
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951032"
---
# <a name="tutorial-configure-insight4grc--for-automatic-user-provisioning"></a>教學課程：設定 Insight4GRC 來自動布建使用者

本教學課程的目的是要示範要在 Insight4GRC 和 Azure Active Directory (Azure AD) 中執行的步驟, 以設定 Azure AD 自動布建和取消布建使用者和/或群組至 Insight4GRC。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需預覽功能之一般 Microsoft Azure 使用規定的詳細資訊, 請參閱[Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

## <a name="prerequisites"></a>先決條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [Insight4GRC 租](https://www.rsmuk.com/)使用者。
* Insight4GRC 中具有系統管理員許可權的使用者帳戶。

## <a name="assigning-users-to-insight4grc"></a>將使用者指派給 Insight4GRC 

Azure Active Directory 使用稱為「*指派*」的概念, 來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中, 只有已指派給 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前, 您應該決定 Azure AD 中的哪些使用者和/或群組需要存取 Insight4GRC。 一旦決定後, 您可以遵循此處的指示, 將這些使用者和/或群組指派給 Insight4GRC:

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-insight4grc"></a>將使用者指派給 Insight4GRC 的重要秘訣 

* 建議將單一 Azure AD 使用者指派給 Insight4GRC, 以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Insight4GRC 時, 您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色 (如果有的話)。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-insight4grc-for-provisioning"></a>安裝 Insight4GRC 以提供布建

將 Insight4GRC 設定為使用 Azure AD 自動布建使用者之前, 您必須啟用 Insight4GRC 上的 SCIM 布建。

1. 若要取得持有人權杖, 終端客戶必須聯絡[支援小組](mailto:support.ss@rsmuk.com), 而且他們會將持有人權杖提供給客戶。

2. 若要取得 SCIM 端點 URL, 您必須將 Insight4GRC 功能變數名稱準備就緒, 因為它將用來建立您的 SCIM 端點 URL。 您可以在使用 Insight4GRC 購買的初始軟體中, 取得 Insight4GRC 功能變數名稱。


## <a name="add-insight4grc--from-the-gallery"></a>從資源庫新增 Insight4GRC

若要使用 Azure AD 設定 Insight4GRC 來自動布建使用者, 您需要從 Azure AD 應用程式資源庫將 Insight4GRC 新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Insight4GRC, 請執行下列步驟:**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中, 選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式, 請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中, 輸入**Insight4GRC**, 在結果面板中選取 [ **Insight4GRC** ], 然後按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的 Insight4GRC](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-insight4grc"></a>設定自動使用者布建至 Insight4GRC  

本節將引導您逐步設定 Azure AD 布建服務, 以根據 Azure AD 中的使用者和/或群組指派, 在 Insight4GRC 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以選擇啟用 Insight4GRC 的 SAML 型單一登入, 請遵循[Insight4GRC-單一登入教學](insight4grc-tutorial.md)課程中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-insight4grc--in-azure-ad"></a>若要在 Azure AD 中設定 Insight4GRC 的自動使用者布建:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**], 然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Insight4GRC]。

    ![應用程式清單中的 Insight4GRC 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![布建索引標籤](common/provisioning-automatic.png)

5.  在 [管理員認證] 區段下`https://{Insight4GRC Domain Name}.insight4grc.com/public/api/scim/v2` , 使用稍早抓取的 {Insight4GRC 功能變數名稱} 值輸入**租使用者 URL** 。 輸入稍早在**秘密權杖**中抓取的**Token 值**。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到 Insight4GRC。 如果連線失敗, 請確定您的 Insight4GRC 帳戶具有系統管理員許可權, 然後再試一次。

    ![租使用者 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]。

8. 在 [對應] 區段下, 選取 [**同步處理 Azure Active Directory 使用者至 Insight4GRC**]。

    ![Insight4GRC-使用者對應](media/insight4grc-provisioning-tutorial/usermapping.png)

9. 在 [**屬性對應**] 區段中, 檢查從 Azure AD 同步處理到 Insight4GRC 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Insight4GRC 中的使用者帳戶, 以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Insight4GRC-使用者對應](media/insight4grc-provisioning-tutorial/userattribute.png)

10. 在對應 區段下, 選取 **同步處理 Azure Active Directory 群組至 Insight4GRC**

    ![Insight4GRC-群組對應](media/insight4grc-provisioning-tutorial/groupmapping.png)

11. 在 [**屬性對應**] 區段中, 檢查從 Azure AD 同步至 Insight4GRC 的群組屬性。 選取為 [比對] 屬性的屬性會用來比對 Insight4GRC 中的群組帳戶以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Insight4GRC-群組對應](media/insight4grc-provisioning-tutorial/groupattribute.png)

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Insight4GRC 的 Azure AD 布建服務, 請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![布建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值, 以定義您想要布建到 Insight4GRC 的使用者和/或群組。

    ![布建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![正在儲存布建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度, 並遵循連結來布建活動報告, 其中描述 Insight4GRC 上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。


## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶](../manage-apps/configure-automatic-user-provisioning-portal.md)布建。
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [了解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)。