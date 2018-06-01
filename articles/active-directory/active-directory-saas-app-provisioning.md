---
title: 在 Azure AD 中自動化 SaaS 應用程式使用者佈建 | Microsoft Docs
description: 簡介如何使用 Azure AD 自動佈建、解除佈建，以及跨多個協力廠商 SaaS 應用程式持續更新使用者帳戶。
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: 72f796f0a4522b66feb55b827b02a83dcfdd3a01
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2018
ms.locfileid: "34069888"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和取消佈建
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>SaaS 應用程式的自動化使用者佈建是什麼？
Azure Active Directory (Azure AD) 可讓您自動化在雲端 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 應用程式中建立、維護和移除使用者身分識別，例如 Dropbox、Salesforce、ServiceNow 等等。

**以下是這項功能可讓您執行的一些範例：**

* 當人員加入您的小組或組織時，在正確的系統中為新的人員自動建立新帳戶。
* 當人員離開小組或組織時，在正確的系統中自動停用帳戶。
* 確保您的應用程式和系統中的身分識別，可根據目錄或您人力資源系統中的變更保持在最新狀態。
* 將非使用者物件 (例如群組) 佈建至支援的應用程式。

**自動化使用者佈建也包含下列功能：**

* 比對來源和目標系統之間現有身分識別的功能。
* 可自訂的屬性對應，其中定義了哪些使用者資料應該從來源系統流向目標系統。
* 針對佈建錯誤發出的選用電子郵件警示
* 報告和活動記錄檔，協助監視與疑難排解。

## <a name="why-use-automated-provisioning"></a>為何要使用自動化佈建？
一些使用這項功能的常見動機包括：

* 避免成本、效率不彰，以及與手動佈建程序相關的人為錯誤。
* 避免與裝載及維護自訂開發的佈建解決方案和指令碼相關的成本
* 為了在使用者離開組織時，立即從主要 SaaS 應用程式移除使用者的身分識別以保護您的組織。
* 為了輕鬆將大量使用者匯入至特定的 SaaS 應用程式或系統中。
* 為了享有使用一組原則來判斷已佈建之使用者及能登入應用程式之使用者的便利。


## <a name="how-does-automatic-provisioning-work"></a>自動化佈建如何運作？
    
**Azure AD 佈建服務**會藉由連接到每個應用程式廠商所提供的使用者管理 API 端點，將使用者佈建至 SaaS 應用程式和其他系統。 這些使用者管理 API 端點可以讓 Azure AD 以程式設計方式建立、更新和移除使用者。 針對所選應用程式，佈建服務也可以建立、更新和移除其他與身分識別相關的物件，例如群組和角色。 

![佈建](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*圖 1：Azure AD 佈建服務*

![輸出佈建](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*圖 2：從 Azure AD 至熱門 SaaS 應用程式的「輸出」使用者佈建工作流程*

![輸入佈建](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*圖 3：從熱門人力資本管理 (HCM) 應用程式至 Azure Active Directory 和 Windows Server Active Directory 的「輸入」使用者佈建工作流程*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>哪些應用程式和系統可以搭配使用 Azure AD 自動使用者佈建？

Azure AD 的特色是為各種熱門 SaaS 應用程式和人力資源系統提供預先整合的支援，以及為實作 SCIM 2.0 標準之特定部分的應用程式提供一般支援。

如需 Azure AD 支援預先整合之佈建連接器的所有應用程式清單，請參閱[適用於使用者佈建的應用程式教學課程清單](active-directory-saas-tutorial-list.md)。

如需有關如何為應用程式新增 Azure AD 使用者佈建支援，請參閱[使用 SCIM 自動將使用者和群組從 Azure Active Directory 佈建到應用程式](active-directory-scim-provisioning.md)。

若要連絡 Azure AD 工程小組以要求對於其他應用程式的佈建支援，請透過 [Azure Active Directory 意見反應論壇](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035)提交訊息。    

> [!NOTE]
> 若要讓應用程式支援自動化使用者佈建，它必須先提供必要的使用者管理 API，以允許外部程式自動建立、維護及移除使用者。 因此，並非所有 SaaS 應用程式都與此功能相容。 針對支援使用者管理 API 的應用程式，Azure AD 工程小組則是能夠建置與這些應用程式的佈建連接器，這項工作是以目前和潛在客戶的需求來排定優先順序。 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>如何對應用程式設定自動佈建？

對於所選應用程式的 Azure AD 佈建服務設定會從 **[Azure 入口網站](https://portal.azure.com)** 開始。 在 [Azure Active Directory] > [企業應用程式] 區段中，選取 [新增]，然後選取 [所有]，並根據您的情況新增下列其中一項：

* [精選應用程式] 區段中的所有應用程式皆支援自動佈建。 如需額外的應用程式，請參閱[適用於使用者佈建的應用程式教學課程清單](active-directory-saas-tutorial-list.md)。

* 對於自訂開發的 SCIM 整合使用「非資源庫應用程式」選項

![資源庫](./media/active-directory-saas-app-provisioning/gallery.png)

在應用程式管理畫面中，佈建可在 [佈建] 索引標籤中進行設定。

![設定](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* **管理員認證**必須提供給 Azure AD 佈建服務，讓它可連線至應用程式所提供的使用者管理 API。 此區段也可讓您在認證失敗或佈建作業進入[隔離](#quarantine)狀態時，啟用電子郵件通知。

* 可設定**屬性對應**，指定來源系統中的欄位 (如：Azure AD) 其內容應同步至目標系統中的哪個欄位 (如：ServiceNow)。 如果目標應用程式可支援，則此區段可讓您選擇性地設定群組佈建 (除了使用者帳戶以外)。 「相符的屬性」可讓您選取使用哪些欄位來比對系統之間的帳戶。 「[運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)」可讓您在將值寫入目標系統之前修改及轉換從來源系統取出的值。 如需詳細資訊，請參閱[自訂屬性對應](active-directory-saas-customizing-attribute-mappings.md)。

![設定](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **範圍設定篩選**可告知佈建服務，來源系統中的哪些使用者和群組應該佈建和/或取消佈建到目標系統。 範圍設定篩選中有兩個層面會一起評估，以決定誰會在佈建範圍中：

    * **屬性值篩選** - 屬性對應中的「來源物件範圍」功能表可針對特定屬性值進行篩選。 例如，您可以指定只有「部門」屬性為「銷售」的使用者應在佈建範圍中。 如需詳細資訊，請參閱 [使用範圍設定篩選](active-directory-saas-scoping-filters.md)。

    * **指派篩選** - 入口網站的 [佈建] > [設定] 區段中的「範圍」功能表可讓您指定是否只有「已指派」的使用者和群組應在佈建範圍中，或者所有 Azure AD 目錄中的使用者都應進行佈建。 有關「指派」使用者和群組的資訊，請參閱[在 Azure Active Directory 中將使用者或群組指派給企業應用程式](active-directory-coreapps-assign-user-azure-portal.md)。
    
* **設定**可控制應用程式的佈建服務作業，包括其目前是否正在執行。

* **稽核記錄**可提供 Azure AD 佈建服務所執行的每項作業記錄。 如需詳細資訊，請參閱[佈建報告指南](active-directory-saas-provisioning-reporting.md)。

![設定](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

> [!NOTE]
> 您也可以使用 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)來設定和管理 Azure AD 使用者佈建服務。


## <a name="what-happens-during-provisioning"></a>佈建期間會發生什麼事？

當 Azure AD 是來源系統時，佈建服務會使用 [Azure AD Graph API 的差異查詢功能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) \(英文\) 來監視使用者和群組。 佈建服務會針對來源系統和目標系統執行初始同步處理，後面再接著定期增量同步處理。 

### <a name="initial-sync"></a>初始同步處理
當佈建服務啟動時，所執行的第一個同步處理將會：

1. 從來源系統查詢所有使用者和群組，其中會擷取[屬性對應](active-directory-saas-customizing-attribute-mappings.md)中定義的所有屬性。
2. 使用任何已設定的[指派](active-directory-coreapps-assign-user-azure-portal.md)或[屬性型範圍設定篩選](active-directory-saas-scoping-filters.md)來篩選所傳回的使用者和群組。
3. 當發現已指派使用者來進行佈建或使用者在佈建範圍中時，服務會使用已指定的[比對屬性](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties)來查詢目標系統中是否有相符的使用者。 範例：如果來源系統中的 userPrincipal 名稱是比對屬性並與目標系統中的 userName 對應，則佈建服務會查詢目標系統中是否有與來源系統中 userPrincipal 名稱值相符的 userName。
4. 如果在目標系統中找不到相符的使用者，就會使用從來源系統傳回的屬性來建立該使用者。
5. 如果找到相符的使用者，則會使用來源系統所提供的屬性來更新該使用者。
6. 如果屬性對應包含「參考」屬性，服務就會在目標系統上執行額外的更新來建立和連結所參考的物件。 例如，使用者可能在目標系統中有 "Manager" 屬性，而此屬性連結至在目標系統中建立的另一個使用者。
7. 在初始同步處理結束時保存浮水印，以為後續的增量同步處理提供起點。

有些應用程式 (例如 ServiceNow、Google Apps 及 Box) 不僅支援佈建使用者，也支援佈建群組及其成員。 在這些情況下，如果已在[對應](active-directory-saas-customizing-attribute-mappings.md)中啟用群組佈建，佈建服務就會同步處理使用者和群組，然後接著同步處理群組成員資格。 

### <a name="incremental-syncs"></a>增量同步處理
進行初始同步處理之後，所有後續的同步處理將會：

1. 查詢來源系統中是否有任何自上次儲存浮水印之後更新的使用者和群組。
2. 使用任何已設定的[指派](active-directory-coreapps-assign-user-azure-portal.md)或[屬性型範圍設定篩選](active-directory-saas-scoping-filters.md)來篩選所傳回的使用者和群組。
3. 當發現已指派使用者來進行佈建或使用者在佈建範圍中時，服務會使用已指定的[比對屬性](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties)來查詢目標系統中是否有相符的使用者。
4. 如果在目標系統中找不到相符的使用者，就會使用從來源系統傳回的屬性來建立該使用者。
5. 如果找到相符的使用者，則會使用來源系統所提供的屬性來更新該使用者。
6. 如果屬性對應包含「參考」屬性，服務就會在目標系統上執行額外的更新來建立和連結所參考的物件。 例如，使用者可能在目標系統中有 "Manager" 屬性，而此屬性連結至在目標系統中建立的另一個使用者。
7. 如果將先前在佈建範圍中的使用者從範圍中移除 (包括解除指派)，則服務會透過更新，在目標系統中停用該使用者。
8. 如果將先前在佈建範圍中的使用者停用或在來源系統中虛刪除，則服務會透過更新，在目標系統中停用該使用者。
9. 如果將先前在佈建範圍中的使用者在來源系統中實刪除，則服務會在目標系統中刪除該使用者。 在 Azure AD 中，會在將使用者虛刪除 30 天之後將他們實刪除。
10. 在增量同步處理結束時保存新的浮水印，以為後續的增量同步處理提供起點。

>[!NOTE]
> 您可以使用 [屬性對應](active-directory-saas-customizing-attribute-mappings.md) 區段中的 [目標物件動作] 核取方塊，視需要停用建立、更新或刪除作業。 在更新期間停用使用者的邏輯也是透過來自 "accountEnabled" 這類欄位的屬性對應來控制。

佈建服務將會依據[每個應用程式特定的教學課程](active-directory-saas-tutorial-list.md)中所定義的間隔，繼續無限期執行背對背增量同步處理，直到發生下列其中一個事件為止：

* 使用 Azure 入口網站或使用適當的「圖形 API」來手動停止服務 
* 使用 Azure 入口網站中的 [清除狀態並重新啟動] 選項，或使用適當的「圖形 API」命令來觸發新的初始同步處理。 這會清除所有已儲存的浮水印，並導致重新評估所有來源物件。
* 因為屬性對應或範圍設定篩選發生變更，而觸發新的初始同步處理。 這也會清除所有已儲存的浮水印，並導致重新評估所有來源物件。
* 佈建程序因為高錯誤率而進入隔離狀態 (參見下方)，並處於隔離狀態超過四週。 在此事件中，將會自動停用服務。

### <a name="errors-and-retries"></a>錯誤和重試 
如果因為目標系統發生錯誤而無法在目標系統中新增、更新或刪除個別使用者，則會在下一個同步處理週期中重試作業。 如果使用者持續發生失敗，則會開始降低重試頻率，逐漸調整回每天僅嘗試一次。 若要解決失敗情況，系統管理員將必須檢查[稽核記錄檔](active-directory-saas-provisioning-reporting.md)中是否有「程序委付」事件，以判斷根本原因和採取適當的動作。 常見的錯誤可能包括：

* 來源系統中未填入目標系統中所需的使用者屬性
* 使用者在來源系統中有一個在目標系統中有唯一條件約束的屬性值，而相同值存在於另一個使用者記錄中

藉由調整來源系統中受影響使用者的屬性值，或將屬性對應調整成不會造成衝突，即可解決這些失敗。   

### <a name="quarantine"></a>隔離
如果對目標系統進行的大多數或所有呼叫因為某個錯誤而一直發生失敗 (例如因為管理員認證無效)，佈建作業就會進入「隔離」狀態。 在[佈建摘要報告](active-directory-saas-provisioning-reporting.md)中會指出此狀態，或如果已在 Azure 入口網站中設定電子郵件通知，則會透過電子郵件指出。 

處於隔離狀態時，增量同步處理的頻率會逐漸降低成一天一次。 

在修正所有違規錯誤之後，便會移除佈建作業的隔離狀態，並開始下一個資料同步週期。 如果佈建作業處於隔離狀態超過四週，系統就會將它停用。


## <a name="frequently-asked-questions"></a>常見問題集

**佈建我的使用者需要多久時間？**

視佈建作業執行的是初始同步處理還是增量同步處理而定，效能將會有所不同。

針對初始同步處理，完成作業所需的時間將直接取決於來源系統中使用者、群組及群組成員的數量。 具有數百個物件的非常小型來源系統可以在幾分鐘內，就完成初始同步處理。 不過，具有數十萬或數百萬個組合物件的來源系統將需要更長的時間。

針對增量同步處理，所需的時間取決於在該同步處理週期偵測到的變更數量。 如果偵測到的使用者或群組成員資格變更少於 5,000 個，通常在 40 分鐘週期內即可完成同步處理。 

請注意，整體效能同時取決於來源和目標系統。 有些目標系統會實作要求率限制和節流，這會影響進行大型同步處理作業期間的效能，以及預先建置的 Azure AD 佈建連接器，因為這些系統會將此因素納入考量。

如果有許多錯誤 (記錄在[稽核記錄](active-directory-saas-provisioning-reporting.md)中) 且佈建服務已進入「隔離」狀態，效能也會較慢。

**如何改善同步處理的效能？**

大多數效能問題都發生在具有大量群組和群組成員之系統的初始同步處理期間。

如果不需要進行群組或群組成員資格同步處理，則可透過下列方式大幅改善同步處理效能：

1. 將 [佈建] > [設定] > [範圍] 功能表設定為 [全部同步處理]，而不是同步處理指派的使用者和群組。
2. 使用[範圍設定篩選](active-directory-saas-scoping-filters.md)，而不是使用指派來篩選已佈建的使用者清單。

> [!NOTE]
> 針對支援佈建群組名稱和群組屬性的應用程式 (例如 ServiceNow 和 Google Apps)，停用此功能也可縮短完成初始同步處理所需的時間。 如果不想將群組名稱和群組成員資格佈建到應用程式，您可以在佈建設定的[屬性對應](active-directory-saas-customizing-attribute-mappings.md)中停用此功能。

**我要如何追蹤目前佈建作業的進度？**

請參閱[佈建報告指南](active-directory-saas-provisioning-reporting.md)。

**如何得知使用者無法正確佈建？**

所有失敗都會記錄在 Azure AD 稽核記錄中。 如需詳細資訊，請參閱[佈建報告指南](active-directory-saas-provisioning-reporting.md)。

**如何組建可與佈建服務搭配運作的應用程式？**

請參閱[使用 SCIM 自動將使用者和群組從 Azure Active Directory 佈建到應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning)。

**我要如何提交意見反應給工程小組？**

請透過 [Azure Active Directory 意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory/)與我們連絡。


## <a name="related-articles"></a>相關文章
* [如何整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [自訂使用者佈建的屬性對應](active-directory-saas-customizing-attribute-mappings.md)
* [撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [適用於使用者佈建的範圍篩選器](active-directory-saas-scoping-filters.md)
* [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](active-directory-scim-provisioning.md)
* [Azure AD 同步處理 API 概觀](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
* [適用於應用程式輸出使用者佈建的逐步部署方案](https://aka.ms/userprovisioningdeploymentplan)

