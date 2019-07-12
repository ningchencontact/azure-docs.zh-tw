---
title: 在 Azure AD 中自動化 SaaS 應用程式使用者佈建 | Microsoft Docs
description: 簡介如何使用 Azure AD 自動佈建、解除佈建，以及跨多個協力廠商 SaaS 應用程式持續更新使用者帳戶。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68c235f32d003adcddecb98c20f30c517c723617
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723939"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和取消佈建

Azure Active Directory (Azure AD) 可讓您自動化建立、 維護和移除在雲端中的使用者身分識別 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 應用程式，例如 Dropbox、 Salesforce、 ServiceNow 等等。 這稱為自動佈建 saas 應用程式的使用者。

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

這項功能可讓您：

- 當人員加入您的小組或組織時，在正確的系統中為新的人員自動建立新帳戶。
- 當人員離開小組或組織時，在正確的系統中自動停用帳戶。
- 確保您的應用程式和系統中的身分識別，可根據目錄或您人力資源系統中的變更保持在最新狀態。
- 將非使用者物件 (例如群組) 佈建至支援的應用程式。

自動化的使用者佈建時，也會包含這項功能：

- 比對來源和目標系統之間現有身分識別的功能。
- 可自訂的屬性對應，其中定義了哪些使用者資料應該從來源系統流向目標系統。
- 佈建錯誤的選用電子郵件警示。
- 報告和活動記錄，協助監視與疑難排解。

## <a name="why-use-automated-provisioning"></a>為何要使用自動化佈建？

一些使用這項功能的常見動機包括：

- 避免成本、效率不彰，以及與手動佈建程序相關的人為錯誤。
- 避免與裝載及維護自訂開發的佈建解決方案和指令碼相關的成本。
- 立即於當使用者離開組織時，從主要 SaaS 應用程式移除使用者的身分識別，以保護您的組織。
- 輕鬆地匯入大量使用者的特定 SaaS 應用程式或系統。
- 有一組原則，以判斷誰佈建，而且誰能夠登入應用程式。

## <a name="how-does-automatic-provisioning-work"></a>自動化佈建如何運作？

**Azure AD 佈建服務**使用者佈建至 SaaS 應用程式和其他系統藉由連接到每個應用程式廠商所提供的使用者管理 API 端點。 這些使用者管理 API 端點可以讓 Azure AD 以程式設計方式建立、更新和移除使用者。 針對選取的應用程式，佈建服務可以也建立、 更新和移除其他身分識別相關的物件，例如群組和角色。

![Azure AD 佈建服務](./media/user-provisioning/provisioning0.PNG)
*圖 1:Azure AD 佈建服務*

![輸出的使用者佈建工作流程](./media/user-provisioning/provisioning1.PNG)
*圖 2:從 Azure AD 至熱門 SaaS 應用程式的「輸出」使用者佈建工作流程*

![輸入的使用者佈建工作流程](./media/user-provisioning/provisioning2.PNG)
*圖 3:從熱門人力資本管理 (HCM) 應用程式至 Azure Active Directory 和 Windows Server Active Directory 的「輸入」使用者佈建工作流程*

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>哪些應用程式和系統可以搭配使用 Azure AD 自動使用者佈建？

支援許多熱門 SaaS 應用程式和人力資源系統，以及用於實作 SCIM 2.0 標準的特定部分的應用程式的一般支援預先整合的 azure AD 功能。

### <a name="pre-integrated-applications"></a>預先整合的應用程式

如需 Azure AD 支援預先整合之佈建連接器的所有應用程式清單，請參閱[適用於使用者佈建的應用程式教學課程清單](../saas-apps/tutorial-list.md)。

若要連絡 Azure AD 工程小組以要求對於其他應用程式的佈建支援，請透過 [Azure Active Directory 意見反應論壇](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035)提交訊息。

> [!NOTE]
> 若要讓應用程式支援自動化使用者佈建，它必須先提供必要的使用者管理 API，以允許外部程式自動建立、維護及移除使用者。 因此，並非所有 SaaS 應用程式都與此功能相容。 對於支援使用者管理 Api 的應用程式，Azure AD 工程小組可以建立這些應用程式，佈建連接器，以及這項工作以目前和潛在客戶的需求來排定優先順序。

### <a name="connecting-applications-that-support-scim-20"></a>連線支援 SCIM 2.0 的應用程式

如需一般如何連線應用程式以實作 SCIM 2.0 型使用者管理 API 的相關資訊，請參閱[使用 SCIM 自動將使用者和群組從 Azure Active Directory 佈建到應用程式](use-scim-to-provision-users-and-groups.md)。

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>如何對應用程式設定自動佈建？

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

您可以使用 Azure Active Directory 入口網站來設定 Azure AD 佈建服務所選的應用程式。

1. 開啟 **[Azure Active Directory 入口網站](https://aad.portal.azure.com)** 。
1. 選取 **企業應用程式**從左窗格中。 會顯示所有設定的應用程式清單。
1. 選擇**新的應用程式 +** 以新增應用程式。 將根據您的案例下列其中一項：

   - **新增您自己的應用程式**選項支援自訂開發的 SCIM 整合。
   - 中的所有應用程式**從資源庫新增** > **精選應用程式**區段支援自動佈建。 如需額外的應用程式，請參閱[適用於使用者佈建的應用程式教學課程清單](../saas-apps/tutorial-list.md)。

1. 提供的任何詳細資料，然後選取**新增**。 新的應用程式新增至 [企業應用程式清單，並以其應用程式管理] 畫面隨即開啟。
1. 選取 **佈建**來管理使用者帳戶佈建應用程式的設定。

   ![會顯示佈建的 [設定] 畫面](./media/user-provisioning/provisioning_settings0.PNG)

1. 選取為 [自動] 選項**佈建模式**指定系統管理員認證、 對應、 啟動和停止，以及同步處理設定。

   - 依序展開**系統管理員認證**輸入 Azure AD 以連線至應用程式的使用者管理 API 所需的認證。 這個區段也可讓您啟用電子郵件通知，如果在認證失敗或佈建作業進入[隔離](#quarantine)。
   - 依序展開**對應**來檢視和編輯 Azure AD 之間流動的使用者屬性和目標應用程式在使用者帳戶佈建或更新。 如果目標應用程式支援的話，這個區段可讓您選擇性地設定 佈建群組和使用者帳戶。 選取對應資料表中，開啟到右方時，您可以在其中檢視和自訂使用者屬性對應編輯器。

     **範圍篩選器**告訴佈建服務的使用者和來源系統中的群組應該佈建或取消佈建到目標系統。 在 **屬性對應**窗格中，選取**來源物件範圍**來篩選特定的屬性值。 例如，您可以指定只有「部門」屬性為「銷售」的使用者應在佈建範圍中。 如需詳細資訊，請參閱 [使用範圍設定篩選](define-conditional-rules-for-provisioning-user-accounts.md)。

     如需詳細資訊，請參閱[自訂屬性對應](customize-application-attributes.md)。

   - **設定**控制佈建服務的應用程式，包括是否目前正在執行的作業。 **範圍**功能表可讓您指定是否只有已指派的使用者和群組應該佈建，範圍中，或應該佈建 Azure AD 目錄中的所有使用者。 有關「指派」使用者和群組的資訊，請參閱[在 Azure Active Directory 中將使用者或群組指派給企業應用程式](assign-user-or-group-access-portal.md)。

在 [應用程式管理] 畫面中，選取**稽核記錄檔**若要檢視記錄的每個作業執行的 Azure AD 所佈建服務。 如需詳細資訊，請參閱[佈建報告指南](check-status-user-account-provisioning.md)。

![範例-稽核記錄檔的應用程式畫面](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> 您也可以使用 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)來設定和管理 Azure AD 使用者佈建服務。

## <a name="what-happens-during-provisioning"></a>佈建期間會發生什麼事？

當 Azure AD 是來源系統時，佈建服務會使用 [Azure AD Graph API 的差異查詢功能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) \(英文\) 來監視使用者和群組。 佈建服務會針對來源系統和目標系統執行初始同步處理，後面再接著定期增量同步處理。

### <a name="initial-sync"></a>初始同步處理

佈建服務啟動時，將會執行第一個同步處理：

1. 從來源系統查詢所有使用者和群組，其中會擷取[屬性對應](customize-application-attributes.md)中定義的所有屬性。
1. 使用任何已設定的[指派](assign-user-or-group-access-portal.md)或[屬性型範圍設定篩選](define-conditional-rules-for-provisioning-user-accounts.md)來篩選所傳回的使用者和群組。
1. 當將使用者指派，或在佈建範圍內，服務會查詢對應的使用者，使用指定的目標系統[比對屬性](customize-application-attributes.md#understanding-attribute-mapping-properties)。 範例：如果來源系統中的 userPrincipal 名稱是比對屬性並與目標系統中的 userName 對應，則佈建服務會查詢目標系統中是否有與來源系統中 userPrincipal 名稱值相符的 userName。
1. 如果在目標系統中找不到相符的使用者，它會建立使用從來源系統傳回的屬性。 建立使用者帳戶之後，佈建服務會偵測，並會快取新的使用者用來執行所有未來的作業，該使用者的目標系統的識別碼。
1. 如果找到相符的使用者，就會更新使用與來源系統所提供的屬性。 比對的使用者帳戶之後，佈建服務會偵測，並會快取新的使用者用來執行所有未來的作業，該使用者的目標系統的識別碼。
1. 如果屬性對應包含 「 參考 」 屬性，服務會在目標系統，才能建立並連結參考的物件上的其他更新。 例如，使用者可能在目標系統中有 "Manager" 屬性，而此屬性連結至在目標系統中建立的另一個使用者。
1. 保存浮水印結尾的初始同步處理，如稍後增量同步處理提供起點。

例如不只佈建使用者，但也佈建群組和其成員的 ServiceNow、 G Suite、 和中支援某些應用程式。 在這些情況下，如果已啟用群組佈建[對應](customize-application-attributes.md)、 佈建服務進行同步處理使用者和群組，並稍後同步的群組成員資格。

### <a name="incremental-syncs"></a>增量同步處理

初始同步處理之後，所有其他同步處理將會：

1. 查詢來源系統中是否有任何自上次儲存浮水印之後更新的使用者和群組。
1. 使用任何已設定的[指派](assign-user-or-group-access-portal.md)或[屬性型範圍設定篩選](define-conditional-rules-for-provisioning-user-accounts.md)來篩選所傳回的使用者和群組。
1. 當將使用者指派，或在佈建範圍內，服務會查詢對應的使用者，使用指定的目標系統[比對屬性](customize-application-attributes.md#understanding-attribute-mapping-properties)。
1. 如果在目標系統中找不到相符的使用者，它會建立使用從來源系統傳回的屬性。 建立使用者帳戶之後，佈建服務會偵測，並會快取新的使用者用來執行所有未來的作業，該使用者的目標系統的識別碼。
1. 如果找到相符的使用者，就會更新使用與來源系統所提供的屬性。 如果它是新指派的帳戶，會比對，佈建服務會偵測，並會快取新的使用者用來執行所有未來的作業，該使用者的目標系統的識別碼。
1. 如果屬性對應包含 「 參考 」 屬性，服務會在目標系統，才能建立並連結參考的物件上的其他更新。 例如，使用者可能在目標系統中有 "Manager" 屬性，而此屬性連結至在目標系統中建立的另一個使用者。
1. 如果將先前在佈建範圍中的使用者從範圍中移除 (包括解除指派)，則服務會透過更新，在目標系統中停用該使用者。
1. 如果將先前在佈建範圍中的使用者停用或在來源系統中虛刪除，則服務會透過更新，在目標系統中停用該使用者。
1. 如果將先前在佈建範圍中的使用者在來源系統中實刪除，則服務會在目標系統中刪除該使用者。 在 Azure AD 中，使用者都是永久刪除 30 天之後已虛刪除。
1. 保存新的浮水印，結尾的增量同步處理，如稍後增量同步處理提供起點。

> [!NOTE]
> 您可以選擇性地停用**Create**，**更新**，或**刪除**透過作業**目標物件動作**中核取方塊[對應](customize-application-attributes.md)一節。 在更新期間停用使用者的邏輯也是透過來自 "accountEnabled" 這類欄位的屬性對應來控制。

佈建服務會繼續無限期執行背對背增量同步處理，在中所定義的時間間隔[特定的每個應用程式教學課程](../saas-apps/tutorial-list.md)，直到發生以下事件之一為止：

- 使用 Azure 入口網站或使用適當的「圖形 API」來手動停止服務 
- 使用 Azure 入口網站中的 [清除狀態並重新啟動]  選項，或使用適當的「圖形 API」命令來觸發新的初始同步處理。 此動作會清除任何儲存的浮水印，並導致重新評估所有來源物件。
- 因為屬性對應或範圍篩選器的變更，就會觸發新的初始同步處理。 此動作也會清除任何儲存的浮水印，並導致重新評估所有來源物件。
- 佈建程序因為高錯誤率而進入隔離 （如下所示），並處於隔離狀態超過四週。 在此事件中，將會自動停用服務。

### <a name="errors-and-retries"></a>錯誤和重試

如果無法加入、 更新或刪除目標系統中因錯誤而在目標系統中個別使用者，在下一個同步週期中重試作業。 如果使用者持續發生失敗，則會開始降低重試頻率，逐漸調整回每天僅嘗試一次。 若要解決此失敗，系統管理員必須檢查[稽核記錄檔](check-status-user-account-provisioning.md)的 「 處理序委付 」 事件，以判斷根本原因和採取適當的動作。 常見的錯誤可能包括：

- 來源系統中未填入目標系統中所需的使用者屬性
- 使用者擁有它沒有 unique 條件約束在目標系統中，來源系統中的屬性值和相同的值會出現在另一個使用者記錄

藉由調整來源系統中受影響使用者的屬性值，或將屬性對應調整成不會造成衝突，即可解決這些失敗。

### <a name="quarantine"></a>隔離

如果大部分或所有以一致的方式對目標系統進行的呼叫失敗，因為發生錯誤 （例如無效的系統管理員認證），佈建作業就會進入 「 隔離 」 狀態。 此狀態會表示在[佈建摘要報告](check-status-user-account-provisioning.md)和透過 Azure 入口網站中已設定電子郵件通知的電子郵件。

處於隔離狀態時，增量同步處理的頻率會逐漸降低成一天一次。

之後所有的違規的錯誤修正和下一個同步週期開始，就會從隔離區移除佈建作業。 如果佈建作業處於隔離狀態超過四週，系統就會將它停用。

## <a name="how-long-will-it-take-to-provision-users"></a>佈建使用者需要多久時間？

效能取決於您的佈建作業是否正在執行初始的佈建循環或累加式的循環。 佈建時間會採用的詳細資料及如何監視佈建服務的狀態，請參閱[檢查的使用者佈建狀態](application-provisioning-when-will-provisioning-finish-specific-user.md)。

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>如何判斷是否已正確佈建使用者？

使用者佈建服務所執行的所有作業都會都記錄在 Azure AD 稽核記錄檔。 這包括所有讀取和寫入作業對來源和目標系統，以及上次讀取或寫入每個作業期間的使用者資料。

如需如何讀取 Azure 入口網站中的稽核記錄的詳細資訊，請參閱[佈建報告指南](check-status-user-account-provisioning.md)。

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>如何針對使用者佈建的問題進行疑難排解？

如需透過案例來獲得如何針對自動使用者佈建進行疑難排解的指引，請參閱[為應用程式設定和佈建使用者時發生問題](application-provisioning-config-problem.md)。

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>推出自動使用者佈建的最佳做法有哪些？

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

如需將使用者佈建輸出到應用程式的範例逐步部署方案，請參閱[適用於使用者佈建的身分識別部署指南](https://aka.ms/userprovisioningdeploymentplan)。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>對 SaaS 應用程式的自動使用者佈建是否適用於 Azure AD 中的 B2B 使用者？

是的就可以使用 Azure AD 使用者佈建服務，以佈建 B2B （或來賓） 使用者在 SaaS 應用程式的 Azure AD 中。

不過，針對 B2B 使用者來登入使用 Azure AD 的 SaaS 應用程式，SaaS 應用程式必須具有其 SAML 型單一登入功能，以特定方式設定。 若想進一步了解如何設定 SaaS 應用程式以支援 B2B 使用者的登入，請參閱[設定適用於 B2B 共同作業的 SaaS 應用程式]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps)。

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>對 SaaS 應用程式的自動使用者佈建是否適用於 Azure AD 中的動態群組？

是的。 Azure AD 使用者佈建服務設定為 [僅指派的同步處理使用者和群組] 時，可以佈建或取消佈建 SaaS 應用程式中的使用者根據它們的成員是否[動態群組](../users-groups-roles/groups-create-rule.md)。 動態群組也適用於 [同步所有使用者與群組] 選項。

不過，使用動態群組可能會影響到從 Azure AD 到 SaaS 應用程式進行端對端使用者佈建的整體效能。 當使用動態群組，請記住下列注意事項和建議：

- 在 SaaS 應用程式中佈建或取消佈建動態群組使用者的速度，取決於動態群組評估成員資格變更的速度。 如需如何檢查動態群組處理狀態的相關資訊，請參閱[檢查成員資格規則的處理狀態](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)。

- 當使用動態群組時，規則必須仔細考量與使用者佈建和取消佈建納入考量，在取消佈建的事件中的成員資格結果遺失。

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>對 SaaS 應用程式的自動使用者佈建是否適用於 Azure AD 中的巢狀群組？

資料分割 當設定為 [僅指派的同步處理使用者和群組]，就有一個 Azure AD 使用者佈建服務無法讀取或巢狀群組中的使用者佈建。 您才可以讀取和立即明確指派的群組成員的使用者佈建。

這是「應用程式的群組型指派」所受到的限制，對單一登入也會產生影響，相關說明請見[使用群組管理 SaaS 應用程式的存取權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps )。

因應措施，您應該明確地指派 (或其他[範圍中](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) 包含要佈建需要的使用者群組。

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>正在佈建 Azure AD 之間及使用加密的通道的目標應用程式嗎？

是的。 我們會使用 [伺服器] 目標的 HTTPS SSL 加密。

## <a name="related-articles"></a>相關文章

- [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)
- [自訂使用者佈建屬性的對應](customize-application-attributes.md)
- [撰寫屬性對應的運算式](functions-for-customizing-application-data.md)
- [範圍篩選器來佈建使用者](define-conditional-rules-for-provisioning-user-accounts.md)
- [使用 SCIM 以啟用自動佈建使用者和群組從 Azure AD 應用程式](use-scim-to-provision-users-and-groups.md)
- [Azure AD 同步處理 API 概觀](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
