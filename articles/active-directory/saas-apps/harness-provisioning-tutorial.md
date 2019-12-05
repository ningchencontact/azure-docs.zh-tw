---
title: 教學課程：使用 Azure Active Directory 設定控管自動使用者布建 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 自動布建和取消布建使用者帳戶以進行控管。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 34d05d6392e00757bf1e5562ffd8341ad04cc9dc
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807754"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>教學課程：設定自動使用者布建的控制

本教學課程的目的是要示範要在控管和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者和/或群組來進行控管。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [控管租使用者](https://harness.io/pricing/)
* 使用系統管理員許可權的使用者帳戶。

## <a name="assigning-users-to-harness"></a>將使用者指派給控管

Azure Active Directory 使用稱為「*指派*」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要存取控管。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給控管：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-harness"></a>將使用者指派給控管的重要秘訣

* 建議將單一 Azure AD 使用者指派給控管，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給控管時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="set-up-harness-for-provisioning"></a>設定控管以進行布建

1. 登入您的控管[管理主控台](https://app.harness.io/#/login)。 流覽至 **持續安全性 > 存取管理**。

    ![控制管理主控台](media/harness-provisioning-tutorial/admin.png)

2.  按一下 [ **API 金鑰**]。

    ![控制新增 SCIM](media/harness-provisioning-tutorial/apikeys.png)

3. 按一下 [**新增金鑰**]。 在 [**新增 Api 金鑰**] 對話方塊中，提供 [**名稱**]，然後從下拉式功能表中選取 [**繼承許可權**] 的選項。 按一下 [**提交**] 按鈕。

    ![控制新增金鑰](media/harness-provisioning-tutorial/addkey.png)

    ![控管 [加入新的金鑰] 對話方塊](media/harness-provisioning-tutorial/title.png)

3.  複製**金鑰**。 此值將會在 Azure 入口網站中控管應用程式的 [布建] 索引標籤的 [密碼權杖] 欄位中輸入。

    ![控制建立權杖](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>從資源庫新增控管

在設定控管以 Azure AD 自動布建使用者之前，您需要從 Azure AD 應用程式資源庫將控制項新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增控管，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入控管，在結果面板**中選取 [** 控管 **]，然後**按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的控管](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-harness"></a>設定自動使用者布建以控制 

本節會引導您完成設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派來建立、更新和停用使用者和/或群組的步驟。

> [!TIP]
> 您也可以遵循控管[單一登入教學](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)課程中提供的指示，選擇啟用控管的 SAML 型單一登入。 單一登入可以與自動使用者布建分開設定，雖然這兩個功能彼此互補

> [!NOTE]
> 若要深入瞭解如何控制 SCIM 端點，請[參閱](https://docs.harness.io/article/smloyragsm-api-keys)

### <a name="to-configure-automatic-user-provisioning-for-harness-in-azure-ad"></a>若要在 Azure AD 中設定控管的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Harness]。

    ![應用程式清單中的控管連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![布建索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下，于 [**租使用者 URL**] 中輸入 `https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`。 輸入稍早在**秘密權杖**中所取得的**SCIM Authentication Token**值。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到控管。 如果連線失敗，請確定您的控管帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者以進行**控管]。

    ![控制使用者對應](media/harness-provisioning-tutorial/usermappings.png)

9. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步處理到控管的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對控管中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![控制使用者屬性](media/harness-provisioning-tutorial/userattributes.png)

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組以進行**控管]。

    ![控制群組對應](media/harness-provisioning-tutorial/groupmappings.png)

11. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步處理到控管的群組屬性。 選取為 [比對] 屬性**的屬性會**用來比對控管中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![控制群組屬性](media/harness-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用適用于控管的 Azure AD 布建服務，請**在 [** **設定**] 區段中，將 [布建**狀態**] 變更為

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中說明控管上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
