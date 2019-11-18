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
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f661aa67f04de23c7b4871e78d3628c639e7567
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144527"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>使用 Azure Active Directory 自動化應用程式的使用者布建和解除布建

在 Azure Active Directory （Azure AD）中，**應用程式**布建一詞指的是在使用者需要存取的雲端（[SaaS](https://azure.microsoft.com/overview/what-is-saas/)）應用程式中自動建立使用者身分識別和角色。 除了建立使用者身分識別以外，自動布建還包括維護和移除使用者身分識別，做為狀態或角色變更。 常見的案例包括將 Azure AD 的使用者布建到[Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)、 [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial)、 [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)等應用程式中。

![布建總覽圖表](media/user-provisioning/provisioning-overview.png)

這項功能可讓您：

- **自動布建：當**新人員加入您的小組或組織時，自動在正確的系統中建立新帳戶。
- **自動**解除布建：當人員離開小組或組織時，自動停用正確系統中的帳戶。
- **在系統之間同步處理資料：** 確保您應用程式和系統中的身分識別會根據目錄或人力資源系統中的變更保持最新狀態。
- 布建**群組：** 將群組布建至支援它們的應用程式。
- **管理存取權：** 監視並 audit 已布建到您應用程式的人員。
- **在棕色的欄位案例中順暢部署：** 比對系統之間現有的身分識別，並允許輕鬆整合，即使使用者已存在於目標系統中也一樣。
- **使用豐富的自訂：** 利用可自訂的屬性對應，定義哪些使用者資料應該從來源系統流向目標系統。
- **取得重大事件的警示：** 布建服務會提供重大事件的警示，並允許 Log Analytics 整合，您可以在其中定義自訂警示以符合您的商務需求。

## <a name="benefits-of-automatic-provisioning"></a>自動布建的優點

隨著現代化組織中使用的應用程式數目持續成長，IT 系統管理員會負責大規模的存取管理。 安全性判斷提示標記語言（SAML）或 Open ID Connect （OIDC）之類的標準可讓系統管理員快速設定單一登入（SSO），但存取權也需要將使用者布建到應用程式中。 對於許多系統管理員而言，布建表示每週手動建立每個使用者帳戶或上傳 CSV 檔案，但這些程式既耗時又耗費資源，而且容易出錯。 已採用 SAML 及時（JIT）之類的解決方案來自動化布建，但是企業也需要解決方案，在使用者離開組織時取消布建，或不再需要根據角色變更來存取特定應用程式。

使用自動布建的一些常見動機包括：

- 將布建程式的效率和正確性最大化。
- 節省與裝載及維護自訂開發的布建解決方案和腳本相關的成本。
- 藉由在使用者離開組織時立即從關鍵 SaaS 應用程式移除他們的身分識別，來保護您的組織。
- 輕鬆將大量使用者匯入至特定的 SaaS 應用程式或系統。
- 具有一組原則來判斷布建者和誰可以登入應用程式。

Azure AD 的使用者布建有助於解決這些挑戰。 若要深入瞭解客戶如何使用 Azure AD 的使用者布建，您可以閱讀[ASOS 案例研究](https://aka.ms/asoscasestudy)。 下列影片提供 Azure AD 中的使用者布建總覽：

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>哪些應用程式和系統可以搭配使用 Azure AD 自動使用者佈建？

Azure AD 為許多熱門的 SaaS 應用程式和人力資源系統提供預先整合的支援，以及可執行[SCIM 2.0 standard](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)特定部分之應用程式的一般支援。

* **預先整合的應用程式（資源庫 SaaS 應用程式）** 。 在使用者布建的[應用程式教學課程清單](../saas-apps/tutorial-list.md)中，您可以找到 Azure AD 支援預先整合布建連接器的所有應用程式。 資源庫中列出的預先整合應用程式通常會使用以 SCIM 2.0 為基礎的使用者管理 Api 來進行布建。 

   ![Salesforce 標誌](media/user-provisioning/gallery-app-logos.png)

   如果您想要要求新的應用程式進行布建，您可以[要求您的應用程式與我們的應用程式庫整合](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)。 針對使用者布建要求，我們要求應用程式必須具有符合 SCIM 規範的端點。 請要求應用程式廠商遵循 SCIM 標準，讓我們可以快速將應用程式上線至我們的平臺。

* **支援 SCIM 2.0 的應用程式**。 如需如何以一般方式連接可執行 SCIM 2.0 型使用者管理 Api 之應用程式的相關資訊，請參閱[使用 SCIM 從 Azure Active Directory 自動將使用者和群組布建到應用程式](use-scim-to-provision-users-and-groups.md)。

## <a name="what-is-scim"></a>什麼是 SCIM？

為了協助自動布建和取消布建，應用程式會公開專屬的使用者和群組 Api。 不過，嘗試在多個應用程式中管理使用者的任何人，都會告訴您，每個應用程式都會嘗試執行相同的簡單動作，例如建立或更新使用者、將使用者新增至群組或解除布建使用者。 不過，所有這些簡單動作的執行方式有點不同，使用不同的端點路徑、指定使用者資訊的不同方法，以及代表每個資訊元素的不同架構。

為了解決這些挑戰，SCIM 規格提供了通用的使用者架構，可協助使用者在應用程式之間移動。 SCIM 已成為布建的現行標準，當與 SAML 或 OpenID Connect 等同盟標準搭配使用時，會為系統管理員提供端對端標準解決方案來進行存取管理。

如需使用 SCIM 自動將使用者和群組布建和取消布建至應用程式的詳細指引，請參閱[使用 Azure Active Directory SCIM 使用者](use-scim-to-provision-users-and-groups.md)布建。

## <a name="how-does-automatic-provisioning-work"></a>自動化佈建如何運作？

Azure AD 布建**服務**會藉由連接到每個應用程式廠商所提供的使用者管理 API 端點，將使用者布建到 SaaS 應用程式和其他系統。 這些使用者管理 API 端點可以讓 Azure AD 以程式設計方式建立、更新和移除使用者。 對於選取的應用程式，布建服務也可以建立、更新和移除其他身分識別相關物件，例如群組和角色。

![Azure AD 布建服務](./media/user-provisioning/provisioning0.PNG)
*圖1： Azure AD*布建服務

![輸出使用者布建工作流程](./media/user-provisioning/provisioning1.PNG)
*圖2：從 Azure AD 到熱門 SaaS 應用程式的「輸出」使用者布建工作流程*

![輸入使用者布建工作流程](./media/user-provisioning/provisioning2.PNG)
*圖3：從熱門人力資本管理（HCM）應用程式到 Azure Active Directory 和 Windows Server 的「輸入」使用者布建工作流程 Active Directory*

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>如何對應用程式設定自動佈建？

針對資源庫中列出的預先整合應用程式，您可以使用逐步指引來設定自動布建。 請參閱[整合式資源庫應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/saas-apps/)。 下列影片示範如何設定 SalesForce 的自動使用者布建。

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

對於支援 SCIM 2.0 的其他應用程式，請依照[使用 Azure Active Directory SCIM 使用者](use-scim-to-provision-users-and-groups.md)布建一文中的步驟進行。

## <a name="what-happens-during-provisioning"></a>佈建期間會發生什麼事？

當 Azure AD 是來源系統時，佈建服務會使用 [Azure AD Graph API 的差異查詢功能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) \(英文\) 來監視使用者和群組。 布建服務會對來源系統和目標系統執行初始迴圈，後面接著週期性增量迴圈。

### <a name="initial-cycle"></a>初始迴圈

當布建服務啟動時，第一次執行的同步處理將會：

1. 從來源系統查詢所有使用者和群組，其中會擷取[屬性對應](customize-application-attributes.md)中定義的所有屬性。
1. 使用任何已設定的[指派](assign-user-or-group-access-portal.md)或[屬性型範圍設定篩選](define-conditional-rules-for-provisioning-user-accounts.md)來篩選所傳回的使用者和群組。
1. 當使用者被指派或在布建範圍內時，服務會使用指定的比對[屬性](customize-application-attributes.md#understanding-attribute-mapping-properties)來查詢目標系統中是否有相符的使用者。 範例：如果來源系統中的 userPrincipal 名稱是比對屬性並與目標系統中的 userName 對應，則佈建服務會查詢目標系統中是否有與來源系統中 userPrincipal 名稱值相符的 userName。
1. 如果在目標系統中找不到相符的使用者，則會使用從來源系統傳回的屬性來建立。 建立使用者帳戶之後，布建服務會偵測並快取新使用者的目標系統識別碼，用來執行該使用者所有未來的作業。
1. 如果找到相符的使用者，則會使用來源系統所提供的屬性來更新它。 使用者帳戶符合後，布建服務會偵測並快取新使用者的目標系統識別碼，用來執行該使用者所有未來的作業。
1. 如果屬性對應包含 "reference" 屬性，服務會在目標系統上進行其他更新，以建立及連結參考的物件。 例如，使用者可能在目標系統中有 "Manager" 屬性，而此屬性連結至在目標系統中建立的另一個使用者。
1. 在初始週期結束時保存浮水印，以提供後續累加迴圈的起點。

某些應用程式（例如 ServiceNow、G Suite 和 Box）不僅支援布建使用者，還能提供群組和其成員。 在這些情況下，如果已在對應中啟用群組布[建，布](customize-application-attributes.md)建服務會同步處理使用者和群組，然後在稍後同步處理群組成員資格。

### <a name="incremental-cycles"></a>增量迴圈

在初始迴圈之後，所有其他週期將會：

1. 查詢來源系統中是否有任何自上次儲存浮水印之後更新的使用者和群組。
1. 使用任何已設定的[指派](assign-user-or-group-access-portal.md)或[屬性型範圍設定篩選](define-conditional-rules-for-provisioning-user-accounts.md)來篩選所傳回的使用者和群組。
1. 當使用者被指派或在布建範圍內時，服務會使用指定的比對[屬性](customize-application-attributes.md#understanding-attribute-mapping-properties)來查詢目標系統中是否有相符的使用者。
1. 如果在目標系統中找不到相符的使用者，則會使用從來源系統傳回的屬性來建立。 建立使用者帳戶之後，布建服務會偵測並快取新使用者的目標系統識別碼，用來執行該使用者所有未來的作業。
1. 如果找到相符的使用者，則會使用來源系統所提供的屬性來更新它。 如果它是新指派的帳戶，則布建服務會偵測並快取新使用者的目標系統識別碼，用來執行該使用者所有未來的作業。
1. 如果屬性對應包含 "reference" 屬性，服務會在目標系統上進行其他更新，以建立及連結參考的物件。 例如，使用者可能在目標系統中有 "Manager" 屬性，而此屬性連結至在目標系統中建立的另一個使用者。
1. 如果將先前在佈建範圍中的使用者從範圍中移除 (包括解除指派)，則服務會透過更新，在目標系統中停用該使用者。
1. 如果將先前在佈建範圍中的使用者停用或在來源系統中虛刪除，則服務會透過更新，在目標系統中停用該使用者。
1. 如果將先前在佈建範圍中的使用者在來源系統中實刪除，則服務會在目標系統中刪除該使用者。 在 Azure AD 中，使用者會在虛刪除之後的30天內被實刪除。
1. 在累加迴圈結束時保存新的浮水印，以提供稍後增量迴圈的起點。

> [!NOTE]
> 您可以使用 [對應**區段中的** **目標物件動作**] 核取方塊，選擇性地停用**建立**、**更新**或[刪除](customize-application-attributes.md)作業。 在更新期間停用使用者的邏輯也是透過來自 "accountEnabled" 這類欄位的屬性對應來控制。

布建服務會依[每個應用程式特定教學](../saas-apps/tutorial-list.md)課程中定義的間隔，無限期地繼續執行回到後增量迴圈，直到發生下列其中一個事件為止：

- 使用 Azure 入口網站或使用適當的「圖形 API」來手動停止服務 
- 使用 Azure 入口網站中的 [**清除狀態] 和 [重新開機**] 選項，或使用適當的圖形 API 命令來觸發新的初始迴圈。 此動作會清除所有儲存的浮水印，並再次評估所有來源物件。
- 因為屬性對應或範圍篩選準則變更，所以會觸發新的初始迴圈。 此動作也會清除所有儲存的浮水印，並再次評估所有來源物件。
- 布建程式會進入隔離狀態（如下所示），因為這是較高的錯誤率，而且會持續隔離超過四周。 在此事件中，將會自動停用服務。

### <a name="errors-and-retries"></a>錯誤和重試

如果無法在目標系統中新增、更新或刪除個別使用者，因為目標系統發生錯誤，則會在下一個同步處理週期中重試作業。 如果使用者持續發生失敗，則會開始降低重試頻率，逐漸調整回每天僅嘗試一次。 若要解決此錯誤，系統管理員必須檢查布建[記錄](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)以判斷根本原因，並採取適當的動作。 常見的錯誤可能包括：

- 來源系統中未填入目標系統中所需的使用者屬性
- 在來源系統中有一個屬性值的使用者在目標系統中有唯一的條件約束，而且相同的值會出現在另一個使用者記錄中

藉由調整來源系統中受影響使用者的屬性值，或將屬性對應調整成不會造成衝突，即可解決這些失敗。

### <a name="quarantine"></a>隔離

如果對目標系統進行的大部分或所有呼叫一直因錯誤而失敗（例如針對不正確系統管理員認證），則布建作業會進入「隔離」狀態。 如果在 Azure 入口網站中設定了電子郵件通知，則會在布建[摘要報告](check-status-user-account-provisioning.md)中指出此狀態，以及透過電子郵件傳送。

在隔離時，增量迴圈的頻率會逐漸縮減為每天一次。

當所有違規錯誤都已修正，且下一個同步處理週期開始時，將會從隔離中移除布建作業。 如果佈建作業處於隔離狀態超過四週，系統就會將它停用。 在這裡深入瞭解[隔離狀態。](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="how-long-will-it-take-to-provision-users"></a>佈建使用者需要多久時間？

效能取決於您的布建作業是執行初始布建週期還是增量迴圈。 如需布建花費時間和如何監視布建服務狀態的詳細資訊，請參閱[檢查使用者](application-provisioning-when-will-provisioning-finish-specific-user.md)布建的狀態。

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>如何判斷是否已正確佈建使用者？

使用者布建服務所執行的所有作業都會記錄在 Azure AD 布建[記錄（預覽）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)中。 這包括對來源和目標系統進行的所有讀取和寫入作業，以及在每個操作期間讀取或寫入的使用者資料。

如需有關如何讀取 Azure 入口網站中布建記錄的詳細資訊，請參閱布建[報告指南](check-status-user-account-provisioning.md)。

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>如何針對使用者佈建的問題進行疑難排解？

如需透過案例來獲得如何針對自動使用者佈建進行疑難排解的指引，請參閱[為應用程式設定和佈建使用者時發生問題](application-provisioning-config-problem.md)。

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>推出自動使用者佈建的最佳做法有哪些？

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

如需將使用者佈建輸出到應用程式的範例逐步部署方案，請參閱[適用於使用者佈建的身分識別部署指南](https://aka.ms/deploymentplans/userprovisioning)。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>對 SaaS 應用程式的自動使用者佈建是否適用於 Azure AD 中的 B2B 使用者？

是，您可以使用 Azure AD 的使用者布建服務，在 Azure AD SaaS 應用程式中布建 B2B （或來賓）使用者。

不過，若要讓 B2B 使用者使用 Azure AD 登入 SaaS 應用程式，SaaS 應用程式必須以特定方式設定其 SAML 型單一登入功能。 若想進一步了解如何設定 SaaS 應用程式以支援 B2B 使用者的登入，請參閱[設定適用於 B2B 共同作業的 SaaS 應用程式]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps)。

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>對 SaaS 應用程式的自動使用者佈建是否適用於 Azure AD 中的動態群組？

是。 當設定為 [僅同步指派的使用者和群組] 時，Azure AD 的使用者布建服務可以根據其是否為[動態群組](../users-groups-roles/groups-create-rule.md)的成員，在 SaaS 應用程式中布建或取消布建使用者。 動態群組也適用於 [同步所有使用者與群組] 選項。

不過，使用動態群組可能會影響到從 Azure AD 到 SaaS 應用程式進行端對端使用者佈建的整體效能。 使用動態群組時，請記住下列注意事項和建議：

- 在 SaaS 應用程式中佈建或取消佈建動態群組使用者的速度，取決於動態群組評估成員資格變更的速度。 如需如何檢查動態群組處理狀態的相關資訊，請參閱[檢查成員資格規則的處理狀態](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)。

- 使用動態群組時，必須仔細考慮使用者布建和取消布建的規則，因為遺失的成員資格會導致解除布建事件。

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>對 SaaS 應用程式的自動使用者佈建是否適用於 Azure AD 中的巢狀群組？

號 當設定為 [僅同步已指派的使用者和群組] 時，Azure AD 的使用者布建服務無法讀取或布建位於嵌套群組中的使用者。 它只能讀取和布建已明確指派群組之直屬成員的使用者。

這是「應用程式的群組型指派」所受到的限制，對單一登入也會產生影響，相關說明請見[使用群組管理 SaaS 應用程式的存取權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps )。

因應措施是在包含需要布建之使用者的群組中明確指派（或以其他方式為[範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)）。

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Azure AD 與使用加密通道的目標應用程式之間的布建嗎？

是。 我們會針對伺服器目標使用 HTTPS SSL 加密。

## <a name="related-articles"></a>相關文章

- [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)
- [自訂使用者布建的屬性對應](customize-application-attributes.md)
- [撰寫屬性對應的運算式](functions-for-customizing-application-data.md)
- [使用者布建的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
- [使用 SCIM 來啟用自動將使用者和群組布建至應用程式 Azure AD](use-scim-to-provision-users-and-groups.md)
- [Azure AD 同步處理 API 概觀](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
