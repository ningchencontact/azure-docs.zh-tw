---
title: Azure Active Directory 入口網站中的稽核活動報告 | Microsoft Docs
description: 介紹 Azure Active Directory 入口網站中的稽核活動報告
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/31/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 97ea32a1e0f8815accff6201251771ab8c088859
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的稽核活動報告 

透過 Azure Active Directory 中的報告，您可以取得判斷您的環境執行狀況所需的資訊。

Azure AD 中的報告架構包含下列元件：

- **活動** 
    - **登入活動** – 受控應用程式和使用者登入活動的使用情況相關資訊
    - **稽核記錄** - 關於使用者和群組管理、受控應用程式和目錄活動的系統活動資訊。
- **Security** 
    - **有風險的登入** - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。 如需詳細資訊，請參閱＜有風險的登入＞。
    - **標幟為有風險的使用者** - 有風險的使用者表示可能被盜用的使用者帳戶。 如需詳細資訊，請參閱＜標幟為有風險的使用者＞。

本主題提供稽核活動的概觀。
 
## <a name="who-can-access-the-data"></a>誰可以存取資料？
* 具有安全性系統管理員或安全性讀取器角色的使用者
* 全域管理員
* 個別使用者 (非系統管理員) 可以看到自己的活動


## <a name="audit-logs"></a>稽核記錄

Azure Active Directory 中的稽核記錄會提供符合規範的系統活動記錄。  
所有稽核資料的第一個進入點是 [Azure Active Directory] 的 [活動] 區段中的 [稽核記錄]。

![稽核記錄檔](./media/active-directory-reporting-activity-audit-logs/61.png "稽核記錄檔")

稽核記錄的預設清單檢視顯示︰

- 發生時間與日期
- 活動的啟動器/動作項目 (對象) 
- 活動 (項目) 
- 目標

![稽核記錄檔](./media/active-directory-reporting-activity-audit-logs/18.png "稽核記錄檔")

您可以按一下工具列中的 [資料行] 來自訂清單檢視。

![稽核記錄檔](./media/active-directory-reporting-activity-audit-logs/19.png "稽核記錄檔")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![稽核記錄檔](./media/active-directory-reporting-activity-audit-logs/21.png "稽核記錄檔")


按一下清單檢視中的項目，即可取得所有可用的詳細資料。

![稽核記錄檔](./media/active-directory-reporting-activity-audit-logs/22.png "稽核記錄檔")


## <a name="filtering-audit-logs"></a>篩選稽核記錄檔

若要將報告的資料縮小至您適用的層級，您可以使用下列欄位篩選稽核資料︰

- 日期範圍
- 啟動者 (執行者)
- 類別
- 活動資源類型
- 活動

![稽核記錄檔](./media/active-directory-reporting-activity-audit-logs/23.png "稽核記錄檔")


**日期範圍**篩選條件可讓您定義傳回資料的時間範圍。  
可能的值包括：

- 1 個月
- 7 天
- 24 小時
- 自訂

當您選取自訂時間範圍時，可以設定開始時間和結束時間。

**啟動者**篩選條件可讓您定義執行者的名稱或其萬用主體名稱 (UPN)。

**類別**篩選條件可讓您選取下列其中一個篩選條件︰

- 全部
- 核心類別
- 核心目錄
- 自助式密碼管理
- 自助式群組管理
- 帳戶佈建 - 自動密碼變換
- 受邀的使用者
- MIM 服務
- 身分識別保護
- B2C

**活動資源類型**篩選條件可讓您選取下列其中一個篩選條件︰

- 全部 
- 群組
- 目錄
- User
- Application
- 原則
- 裝置
- 其他

當您選取 [群組] 作為 [活動資源類型] 時，會取得可讓您也提供**來源**的額外篩選條件類別：

- Azure AD
- O365


**活動**篩選條件是根據您類別和您選取的活動資源類型。 您可以選取您想要查看的特定活動或選擇全部。 

您可以使用圖形 API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta 來取得所有稽核活動的清單 (其中，$tenantdomain 是網域名稱)，或請參閱[稽核報告事件](active-directory-reporting-audit-events.md)一文。


## <a name="audit-logs-shortcuts"></a>稽核記錄快速鍵

除了 **Azure Active Directory** 之外，Azure 入口網站可提供您稽核資料的兩個額外進入點︰

- 使用者和群組
- 企業應用程式

### <a name="users-and-groups-audit-logs"></a>使用者和群組稽核記錄檔

透過以使用者和群組為基礎的稽核報告，可以取得下列問題的解答︰

- 使用者已套用哪些類型的更新？

- 有多少使用者已變更？

- 有多少密碼已變更？

- 系統管理員已在目錄中執行哪些作業？

- 已新增的群組為何？

- 群組有成員資格變更嗎？

- 群組的擁有者已變更嗎？

- 指派給群組或使用者的授權為何？

如果您只想檢閱使用者和群組相關的稽核資料，您可以在 [使用者和群組] 的 [活動] 區段中的 [稽核記錄] 之下找到篩選過的檢視。 此進入點將 [使用者和群組] 作為預先選取的 [活動資源類型]。

![稽核記錄檔](./media/active-directory-reporting-activity-audit-logs/93.png "稽核記錄檔")

### <a name="enterprise-applications-audit-logs"></a>企業應用程式稽核記錄

透過以應用程式為基礎的稽核報告，可以取得下列問題的解答︰

* 已新增或更新的應用程式為何？
* 已移除的應用程式為何？
* 應用程式的服務原則已變更嗎？
* 應用程式的名稱已變更嗎？
* 誰已同意應用程式？

如果您只想檢閱應用程式相關的稽核資料，您可以在 [企業應用程式] 刀鋒視窗的 [活動] 區段中的 [稽核記錄] 之下找到篩選過的檢視。 此進入點將 [企業應用程式] 當作預先選取的 [活動資源類型]。

![稽核記錄檔](./media/active-directory-reporting-activity-audit-logs/134.png "稽核記錄檔")

您可以將此檢視進一步向下篩選為只有 [群組] 或只有 [使用者]。

![稽核記錄檔](./media/active-directory-reporting-activity-audit-logs/25.png "稽核記錄檔")



## <a name="azure-ad-audit-activity-list"></a>Azure AD 稽核活動清單

本節提供您可以記錄的所有活動清單。 


|服務名稱|稽核類別|活動資源類型|活動|
|---|---|---|---|
|帳戶佈建|應用程式管理|Application|系統管理|
|帳戶佈建|應用程式管理|Application|目錄作業|
|帳戶佈建|應用程式管理|Application|匯出|
|帳戶佈建|應用程式管理|Application|Import|
|帳戶佈建|應用程式管理|Application|其他|
|帳戶佈建|應用程式管理|Application|處理序委付|
|帳戶佈建|應用程式管理|Application|同步處理規則動作|
|應用程式 Proxy|應用程式管理|Application|新增應用程式|
|應用程式 Proxy|資源|資源|新增應用程式 SSL 憑證|
|應用程式 Proxy|資源|資源|刪除 SSL 繫結|
|應用程式 Proxy|應用程式管理|Application|刪除應用程式|
|應用程式 Proxy|目錄管理|目錄|停用桌面 Sso|
|應用程式 Proxy|目錄管理|目錄|停用特定網域的桌面 Sso|
|應用程式 Proxy|目錄管理|目錄|停用應用程式 Proxy|
|應用程式 Proxy|目錄管理|目錄|停用傳遞驗證|
|應用程式 Proxy|目錄管理|目錄|啟用桌面 Sso|
|應用程式 Proxy|目錄管理|目錄|啟用特定網域的桌面 Sso|
|應用程式 Proxy|目錄管理|目錄|啟用應用程式 Proxy|
|應用程式 Proxy|目錄管理|目錄|啟用傳遞驗證|
|應用程式 Proxy|資源|資源|註冊連接器|
|應用程式 Proxy|應用程式管理|Application|更新應用程式|
|應用程式 Proxy|應用程式管理|Application|更新應用程式單一登入模式|
|自動密碼變換|應用程式管理|Application|自動密碼變換|
|B2C|應用程式管理|Application|新增 V2 應用程式權限|
|B2C|Authorization|Authorization|新增 V2 應用程式權限|
|B2C|Key|Key|將以 ASCII 密碼為基礎的金鑰新增至 CPIM 金鑰容器|
|B2C|Authorization|Authorization|將以 ASCII 密碼為基礎的金鑰新增至 CPIM 金鑰容器|
|B2C|Key|Key|將金鑰新增至 CPIM 金鑰容器|
|B2C|Authorization|Authorization|將金鑰新增至 CPIM 金鑰容器|
|B2C|資源|資源|AdminPolicyDatas-RemoveResources|
|B2C|Authorization|Authorization|AdminPolicyDatas-SetResources|
|B2C|資源|資源|AdminPolicyDatas-SetResources|
|B2C|資源|資源|AdminUserJourneys-GetResources|
|B2C|Authorization|Authorization|AdminUserJourneys-GetResources|
|B2C|Authorization|Authorization|AdminUserJourneys-RemoveResources|
|B2C|資源|資源|AdminUserJourneys-RemoveResources|
|B2C|資源|資源|AdminUserJourneys-SetResources|
|B2C|Authorization|Authorization|AdminUserJourneys-SetResources|
|B2C|Authorization|Authorization|Create IdentityProvider|
|B2C|資源|資源|Create IdentityProvider|
|B2C|Authorization|Authorization|建立 V1 應用程式|
|B2C|應用程式管理|Application|建立 V1 應用程式|
|B2C|應用程式管理|Application|建立 V2 應用程式|
|B2C|Authorization|Authorization|建立 V2 應用程式|
|B2C|目錄管理|目錄|在租用戶中建立自訂網域|
|B2C|Authorization|Authorization|在租用戶中建立自訂網域|
|B2C|Authorization|Authorization|建立新的 AdminUserJourney|
|B2C|資源|資源|建立新的 AdminUserJourney|
|B2C|資源|資源|建立當地語系化資源 json|
|B2C|Authorization|Authorization|建立當地語系化資源 json|
|B2C|Authorization|Authorization|建立新的自訂 IDP|
|B2C|資源|資源|建立新的自訂 IDP|
|B2C|資源|資源|建立新的 IDP|
|B2C|Authorization|Authorization|建立新的 IDP|
|B2C|Authorization|Authorization|建立或更新 B2C 目錄資源|
|B2C|資源|資源|建立或更新 B2C 目錄資源|
|B2C|資源|資源|建立原則|
|B2C|Authorization|Authorization|建立原則|
|B2C|Authorization|Authorization|建立 trustFramework 原則|
|B2C|資源|資源|建立 trustFramework 原則|
|B2C|Authorization|Authorization|使用可設定的首碼建立 trustFramework 原則|
|B2C|資源|資源|使用可設定的首碼建立 trustFramework 原則|
|B2C|資源|資源|建立使用者屬性|
|B2C|Authorization|Authorization|建立使用者屬性|
|B2C|Authorization|Authorization|CreateTrustFrameworkPolicy|
|B2C|資源|資源|CreateTrustFrameworkPolicy|
|B2C|Authorization|Authorization|建立或更新新的 AdminUserJourney|
|B2C|資源|資源|刪除 IDP|
|B2C|Authorization|Authorization|刪除 IDP|
|B2C|資源|資源|建立 IdentityProvider|
|B2C|Authorization|Authorization|建立 IdentityProvider|
|B2C|應用程式管理|Application|刪除 V1 應用程式|
|B2C|Authorization|Authorization|刪除 V1 應用程式|
|B2C|應用程式管理|Application|刪除 V2 應用程式|
|B2C|Authorization|Authorization|刪除 V2 應用程式|
|B2C|Authorization|Authorization|刪除 V2 應用程式授權|
|B2C|應用程式管理|Application|刪除 V2 應用程式授權|
|B2C|資源|資源|刪除 B2C 目錄資源|
|B2C|Authorization|Authorization|刪除 B2C 目錄資源|
|B2C|Key|Key|刪除 CPIM 金鑰容器|
|B2C|Authorization|Authorization|刪除 CPIM 金鑰容器|
|B2C|Key|Key|刪除金鑰容器|
|B2C|資源|資源|刪除 trustFramework 原則|
|B2C|Authorization|Authorization|刪除 trustFramework 原則|
|B2C|資源|資源|刪除使用者屬性|
|B2C|Authorization|Authorization|刪除使用者屬性|
|B2C|Authorization|Authorization|啟用 B2C 功能|
|B2C|目錄管理|目錄|啟用 B2C 功能|
|B2C|資源|資源|取得資源群組中的 B2C 目錄資源|
|B2C|資源|資源|取得訂用帳戶中的 B2C 目錄資源|
|B2C|Authorization|Authorization|取得訂用帳戶中的 B2C 目錄資源|
|B2C|Authorization|Authorization|取得自訂 IDP|
|B2C|資源|資源|取得自訂 IDP|
|B2C|資源|資源|取得 IDP|
|B2C|Authorization|Authorization|取得 IDP|
|B2C|Authorization|Authorization|取得 V1 和 V2 應用程式|
|B2C|應用程式管理|Application|取得 V1 和 V2 應用程式|
|B2C|Authorization|Authorization|取得 V1 應用程式|
|B2C|應用程式管理|Application|取得 V1 應用程式|
|B2C|Authorization|Authorization|取得 V1 應用程式|
|B2C|應用程式管理|Application|取得 V1 應用程式|
|B2C|應用程式管理|Application|取得 V2 應用程式|
|B2C|Authorization|Authorization|取得 V2 應用程式|
|B2C|應用程式管理|Application|取得 V2 應用程式|
|B2C|Authorization|Authorization|取得 V2 應用程式|
|B2C|資源|資源|取得 B2C 目錄資源|
|B2C|Authorization|Authorization|取得 B2C 目錄資源|
|B2C|Authorization|Authorization|取得租用戶中的自訂網域清單|
|B2C|目錄管理|目錄|取得租用戶中的自訂網域清單|
|B2C|Authorization|Authorization|取得使用者旅程圖|
|B2C|資源|資源|取得使用者旅程圖|
|B2C|資源|資源|取得使用者旅程圖的允許應用程式宣告|
|B2C|Authorization|Authorization|取得使用者旅程圖的允許應用程式宣告|
|B2C|資源|資源|取得使用者旅程圖的允許自我判斷宣告|
|B2C|Authorization|Authorization|取得使用者旅程圖的允許自我判斷宣告|
|B2C|資源|資源|取得原則的允許自我判斷宣告|
|B2C|Authorization|Authorization|取得原則的允許自我判斷宣告|
|B2C|資源|資源|取得可用的輸出宣告清單|
|B2C|Authorization|Authorization|取得可用的輸出宣告清單|
|B2C|資源|資源|取得使用者旅程圖的內容定義|
|B2C|Authorization|Authorization|取得使用者旅程圖的內容定義|
|B2C|Authorization|Authorization|取得特定管理流程的 IDP|
|B2C|資源|資源|取得特定管理流程的 IDP|
|B2C|Key|Key|取得 JWK 中的金鑰容器作用中金鑰中繼資料|
|B2C|Authorization|Authorization|取得 JWK 中的金鑰容器作用中金鑰中繼資料|
|B2C|Key|Key|取得金鑰容器中繼資料|
|B2C|資源|資源|取得所有管理流程的清單|
|B2C|Authorization|Authorization|取得所有管理流程的清單|
|B2C|Authorization|Authorization|取得所有使用者的所有管理流程的標記清單|
|B2C|資源|資源|取得所有使用者的所有管理流程的標記清單|
|B2C|資源|資源|取得使用者的租用戶清單|
|B2C|Authorization|Authorization|取得使用者的租用戶清單|
|B2C|資源|資源|取得本機帳戶的自我判斷宣告|
|B2C|Authorization|Authorization|取得本機帳戶的自我判斷宣告|
|B2C|資源|資源|取得當地語系化資源 json|
|B2C|Authorization|Authorization|取得當地語系化資源 json|
|B2C|Authorization|Authorization|取得 Microsoft.AzureActiveDirectory 資源提供者的作業|
|B2C|資源|資源|取得 Microsoft.AzureActiveDirectory 資源提供者的作業|
|B2C|資源|資源|取得原則|
|B2C|Authorization|Authorization|取得原則|
|B2C|資源|資源|取得原則|
|B2C|Authorization|Authorization|取得原則|
|B2C|目錄管理|目錄|取得租用戶的資源屬性|
|B2C|Authorization|Authorization|取得租用戶的資源屬性|
|B2C|資源|資源|取得支援的 IDP 清單|
|B2C|Authorization|Authorization|取得支援的 IDP 清單|
|B2C|資源|資源|取得使用者旅程圖的支援 IDP 清單|
|B2C|Authorization|Authorization|取得使用者旅程圖的支援 IDP 清單|
|B2C|目錄管理|目錄|取得租用戶資訊|
|B2C|Authorization|Authorization|取得租用戶資訊|
|B2C|目錄管理|目錄|取得租用戶允許的功能|
|B2C|Authorization|Authorization|取得租用戶允許的功能|
|B2C|Authorization|Authorization|取得租用戶定義的自訂 IDP 清單|
|B2C|資源|資源|取得租用戶定義的自訂 IDP 清單|
|B2C|資源|資源|取得租用戶定義的 IDP 清單|
|B2C|Authorization|Authorization|取得租用戶定義的 IDP 清單|
|B2C|資源|資源|取得租用戶定義的本機 IDP 清單|
|B2C|Authorization|Authorization|取得租用戶定義的本機 IDP 清單|
|B2C|資源|資源|取得使用者的租用戶詳細資料以便建立資源|
|B2C|Authorization|Authorization|取得使用者的租用戶詳細資料以便建立資源|
|B2C|Authorization|Authorization|取得租用戶清單|
|B2C|Authorization|Authorization|取得 tenantDomains|
|B2C|目錄管理|目錄|取得 tenantDomains|
|B2C|資源|資源|取得 CPIM 的預設支援文化特性|
|B2C|Authorization|Authorization|取得 CPIM 的預設支援文化特性|
|B2C|資源|資源|取得管理流程的詳細資料|
|B2C|Authorization|Authorization|取得管理流程的詳細資料|
|B2C|Authorization|Authorization|取得此租用戶的 UserJourneys 清單|
|B2C|資源|資源|取得此租用戶的 UserJourneys 清單|
|B2C|Authorization|Authorization|取得 CPIM 可用的支援文化特性集合|
|B2C|資源|資源|取得 CPIM 可用的支援文化特性集合|
|B2C|Authorization|Authorization|取得 trustFramework 原則|
|B2C|資源|資源|取得 trustFramework 原則|
|B2C|Authorization|Authorization|取得 xml 格式的 trustFramework 原則|
|B2C|資源|資源|取得 xml 格式的 trustFramework 原則|
|B2C|資源|資源|取得使用者屬性|
|B2C|Authorization|Authorization|取得使用者屬性|
|B2C|Authorization|Authorization|取得使用者屬性|
|B2C|資源|資源|取得使用者屬性|
|B2C|Authorization|Authorization|取得使用者旅程圖清單|
|B2C|資源|資源|取得使用者旅程圖清單|
|B2C|Authorization|Authorization|GetIEFPolicies|
|B2C|資源|資源|GetIEFPolicies|
|B2C|Authorization|Authorization|GetIdentityProviders|
|B2C|資源|資源|GetIdentityProviders|
|B2C|資源|資源|GetTrustFrameworkPolicy|
|B2C|Authorization|Authorization|GetTrustFrameworkPolicy|
|B2C|Key|Key|取得 jwk 格式的 CPIM 金鑰容器|
|B2C|Authorization|Authorization|取得 jwk 格式的 CPIM 金鑰容器|
|B2C|Key|Key|取得租用戶中的金鑰容器清單|
|B2C|Authorization|Authorization|取得租用戶中的金鑰容器清單|
|B2C|Authorization|Authorization|取得租用戶的類型|
|B2C|目錄管理|目錄|取得租用戶的類型|
|B2C|驗證|驗證|將存取權杖發給應用程式|
|B2C|驗證|驗證|發出應用程式的授權碼|
|B2C|其他|其他|將授權碼發給應用程式|
|B2C|驗證|驗證|將 id_token 發給應用程式|
|B2C|其他|其他|將 id_token 發給應用程式|
|B2C|Authorization|Authorization|MigrateTenantMetadata|
|B2C|資源|資源|MigrateTenantMetadata|
|B2C|資源|資源|移動資源|
|B2C|Authorization|Authorization|修補程式 IdentityProvider|
|B2C|資源|資源|修補程式 IdentityProvider|
|B2C|資源|資源|PutTrustFrameworkPolicy|
|B2C|Authorization|Authorization|PutTrustFrameworkPolicy|
|B2C|Authorization|Authorization|PutTrustFrameworkpolicy|
|B2C|資源|資源|PutTrustFrameworkpolicy|
|B2C|資源|資源|移除使用者旅程圖|
|B2C|Authorization|Authorization|移除使用者旅程圖|
|B2C|Authorization|Authorization|還原 CPIM 金鑰容器備份|
|B2C|Key|Key|還原 CPIM 金鑰容器備份|
|B2C|應用程式管理|Application|擷取 V2 應用程式授權|
|B2C|Authorization|Authorization|擷取 V2 應用程式授權|
|B2C|應用程式管理|Application|擷取目前租用戶中的 V2 應用程式服務主體|
|B2C|Authorization|Authorization|擷取目前租用戶中的 V2 應用程式服務主體|
|B2C|Key|Key|儲存金鑰容器|
|B2C|Authorization|Authorization|更新自訂 IDP|
|B2C|資源|資源|更新自訂 IDP|
|B2C|資源|資源|更新 IDP|
|B2C|Authorization|Authorization|更新 IDP|
|B2C|資源|資源|更新本機 IDP|
|B2C|Authorization|Authorization|更新本機 IDP|
|B2C|應用程式管理|Application|更新 V1 應用程式|
|B2C|Authorization|Authorization|更新 V1 應用程式|
|B2C|應用程式管理|Application|更新 V2 應用程式|
|B2C|Authorization|Authorization|更新 V2 應用程式|
|B2C|應用程式管理|Application|更新 V2 應用程式授權|
|B2C|Authorization|Authorization|更新 V2 應用程式授權|
|B2C|資源|資源|更新 B2C 目錄資源|
|B2C|資源|資源|更新原則|
|B2C|Authorization|Authorization|更新原則|
|B2C|資源|資源|更新訂用帳戶狀態|
|B2C|資源|資源|更新使用者屬性|
|B2C|Authorization|Authorization|更新使用者屬性|
|B2C|Key|Key|上傳 CPIM 加密的金鑰|
|B2C|Authorization|Authorization|上傳 CPIM 加密的金鑰|
|B2C|Authorization|Authorization|使用者授權：已停用租用戶功能集的 API|
|B2C|Authorization|Authorization|使用者授權：使用者以「租用戶管理員」身分授與存取權|
|B2C|Authorization|Authorization|使用者授權：使用者獲取「已驗證的使用者」存取權限|
|B2C|驗證|驗證|驗證本機帳戶認證|
|B2C|資源|資源|驗證移動資源|
|B2C|驗證|驗證|驗證使用者驗證|
|B2C|目錄管理|目錄|驗證是否已啟用 B2C 功能|
|B2C|Authorization|Authorization|驗證是否已啟用 B2C 功能|
|B2C|Authorization|Authorization|驗證是否已啟用功能|
|B2C|目錄管理|目錄|驗證是否已啟用功能|
|核心目錄|應用程式管理|Application|新增 OAuth2PermissionGrant|
|核心目錄|管理單位的管理|AdministrativeUnit|新增管理單位|
|核心目錄|使用者管理|User|將應用程式角色指派授權新增至使用者|
|核心目錄|群組管理|群組|將應用程式角色指派新增至群組|
|核心目錄|應用程式管理|Application|將應用程式角色指派新增至服務主體|
|核心目錄|應用程式管理|Application|新增應用程式|
|核心目錄|資源|資源|新增裝置|
|核心目錄|資源|資源|新增裝置組態|
|核心目錄|角色管理|角色|將合格成員新增至角色|
|核心目錄|群組管理|群組|新增群組|
|核心目錄|管理單位的管理|AdministrativeUnit|將成員新增至管理單位|
|核心目錄|群組管理|群組|將成員加入群組|
|核心目錄|角色管理|角色|將成員新增至角色|
|核心目錄|應用程式管理|Application|將擁有者新增至應用程式|
|核心目錄|群組管理|群組|將擁有者新增至群組|
|核心目錄|原則管理|原則|將擁有者新增至原則|
|核心目錄|應用程式管理|Application|將擁有者新增至服務主體|
|核心目錄|目錄管理|目錄|將夥伴新增至公司|
|核心目錄|原則管理|原則|Add policy|
|核心目錄|應用程式管理|Application|將原則新增至服務主體|
|核心目錄|資源|資源|將已註冊的擁有者新增至裝置|
|核心目錄|資源|資源|將已註冊的使用者新增至裝置|
|核心目錄|角色管理|角色|將角色指派新增至角色定義|
|核心目錄|角色管理|角色|從範本新增角色|
|核心目錄|角色管理|角色|將範圍成員新增至角色|
|核心目錄|應用程式管理|Application|新增服務主體|
|核心目錄|應用程式管理|Application|新增服務主體認證|
|核心目錄|目錄管理|目錄|新增未驗證的網域|
|核心目錄|使用者管理|User|新增使用者|
|核心目錄|使用者管理|User|新增使用者增強式驗證電話應用程式詳細資料|
|核心目錄|目錄管理|目錄|新增已驗證的網域|
|核心目錄|使用者管理|User|變更使用者授權|
|核心目錄|使用者管理|User|變更使用者密碼|
|核心目錄|應用程式管理|Application|同意應用程式|
|核心目錄|使用者管理|User|將同盟使用者轉換為受控|
|核心目錄|使用者管理|User|為使用者建立應用程式密碼|
|核心目錄|目錄管理|目錄|建立公司|
|核心目錄|目錄管理|目錄|建立公司設定|
|核心目錄|群組管理|群組|建立群組設定|
|核心目錄|管理單位的管理|AdministrativeUnit|刪除管理單位|
|核心目錄|應用程式管理|Application|刪除應用程式|
|核心目錄|使用者管理|User|刪除使用者的應用程式密碼|
|核心目錄|目錄管理|目錄|刪除公司設定|
|核心目錄|資源|資源|刪除裝置|
|核心目錄|資源|資源|刪除裝置組態|
|核心目錄|群組管理|群組|刪除群組|
|核心目錄|群組管理|群組|刪除群組設定|
|核心目錄|原則管理|原則|刪除原則|
|核心目錄|使用者管理|User|刪除使用者|
|核心目錄|目錄管理|目錄|將夥伴降級|
|核心目錄|資源|資源|不再符合規範的裝置|
|核心目錄|資源|資源|不再受控的裝置|
|核心目錄|目錄管理|目錄|已刪除的目錄|
|核心目錄|目錄管理|目錄|永久刪除的目錄|
|核心目錄|目錄管理|目錄|排程刪除的目錄|
|核心目錄|使用者管理|User|停用帳戶|
|核心目錄|使用者管理|User|啟用增強式驗證|
|核心目錄|群組管理|群組|完成將群組型授權套用至使用者|
|核心目錄|應用程式管理|Application|實刪除應用程式|
|核心目錄|群組管理|群組|實刪除群組|
|核心目錄|使用者管理|User|實刪除使用者|
|核心目錄|目錄管理|目錄|將公司升級為夥伴|
|核心目錄|目錄管理|目錄|清除 Rights Management 屬性|
|核心目錄|應用程式管理|Application|移除 OAuth2PermissionGrant|
|核心目錄|群組管理|群組|移除群組的應用程式角色指派|
|核心目錄|應用程式管理|Application|移除服務主體的應用程式角色指派|
|核心目錄|使用者管理|User|移除使用者的應用程式角色指派|
|核心目錄|角色管理|角色|移除角色的合格成員|
|核心目錄|管理單位的管理|AdministrativeUnit|移除管理單位的成員|
|核心目錄|群組管理|群組|從群組移除成員|
|核心目錄|角色管理|角色|移除角色的成員|
|核心目錄|應用程式管理|Application|移除應用程式的擁有者|
|核心目錄|群組管理|群組|移除群組的擁有者|
|核心目錄|應用程式管理|Application|移除服務主體的擁有者|
|核心目錄|目錄管理|目錄|移除公司的夥伴|
|核心目錄|原則管理|原則|移除原則認證|
|核心目錄|應用程式管理|Application|移除服務主體中的原則|
|核心目錄|資源|資源|移除裝置中已註冊的擁有者|
|核心目錄|資源|資源|移除裝置中已註冊的使用者|
|核心目錄|角色管理|角色|移除角色定義中的角色指派|
|核心目錄|角色管理|角色|移除角色的範圍成員|
|核心目錄|應用程式管理|Application|移除服務主體|
|核心目錄|應用程式管理|Application|移除服務主體認證|
|核心目錄|目錄管理|目錄|移除未驗證的網域|
|核心目錄|使用者管理|User|移除使用者增強式驗證電話應用程式詳細資料|
|核心目錄|目錄管理|目錄|移除已驗證的網域|
|核心目錄|使用者管理|User|重設使用者密碼|
|核心目錄|群組管理|群組|還原群組|
|核心目錄|應用程式管理|Application|還原應用程式|
|核心目錄|使用者管理|User|還原使用者|
|核心目錄|應用程式管理|Application|撤銷同意|
|核心目錄|目錄管理|目錄|設定公司資訊|
|核心目錄|目錄管理|目錄|設定 DirSync 功能|
|核心目錄|目錄管理|目錄|設定 DirSyncEnabled 旗標|
|核心目錄|目錄管理|目錄|設定合作關係|
|核心目錄|目錄管理|目錄|設定意外刪除閾值|
|核心目錄|目錄管理|目錄|設定公司允許的資料位置|
|核心目錄|目錄管理|目錄|設定啟用公司多語系功能|
|核心目錄|目錄管理|目錄|在租用戶上設定目錄功能|
|核心目錄|目錄管理|目錄|設定網域驗證|
|核心目錄|目錄管理|目錄|設定網域的同盟設定|
|核心目錄|使用者管理|User|設定強制變更使用者密碼|
|核心目錄|群組管理|群組|設定群組授權|
|核心目錄|群組管理|群組|將群組設定為由使用者管理|
|核心目錄|目錄管理|目錄|設定密碼原則|
|核心目錄|目錄管理|目錄|設定 Rights Management 屬性|
|核心目錄|使用者管理|User|設定使用者管理員|
|核心目錄|使用者管理|User|設定啟用使用者 oath 權杖中繼資料|
|核心目錄|群組管理|群組|開始將群組型授權套用至使用者|
|核心目錄|群組管理|群組|觸發群組授權重新計算|
|核心目錄|使用者管理|User|更新 StsRefreshTokenValidFrom 時間戳記|
|核心目錄|管理單位的管理|AdministrativeUnit|更新管理單位|
|核心目錄|應用程式管理|Application|更新應用程式|
|核心目錄|目錄管理|目錄|更新公司|
|核心目錄|目錄管理|目錄|更新公司設定|
|核心目錄|資源|資源|更新裝置|
|核心目錄|資源|資源|更新裝置組態|
|核心目錄|目錄管理|目錄|更新網站|
|核心目錄|使用者管理|User|更新外部密碼|
|核心目錄|應用程式管理|Application|更新外部密碼|
|核心目錄|群組管理|群組|更新群組|
|核心目錄|群組管理|群組|更新群組設定|
|核心目錄|原則管理|原則|更新原則|
|核心目錄|角色管理|角色|更新角色|
|核心目錄|應用程式管理|Application|更新服務主體|
|核心目錄|使用者管理|User|更新使用者|
|核心目錄|目錄管理|目錄|驗證網域|
|核心目錄|目錄管理|目錄|驗證電子郵件驗證的網域|
|身分識別保護|使用者管理|User|管理員產生臨時密碼|
|身分識別保護|使用者管理|User|管理員要求使用者重設密碼|
|身分識別保護|其他|其他|下載單一風險事件類型|
|身分識別保護|其他|其他|下載加入每週摘要的管理員和狀態|
|身分識別保護|其他|其他|下載所有風險事件類型|
|身分識別保護|其他|其他|下載免費使用者風險事件|
|身分識別保護|其他|其他|下載標幟為有風險的使用者|
|身分識別保護|目錄管理|目錄|登入|
|身分識別保護|原則管理|原則|設定 MFA 註冊原則|
|身分識別保護|原則管理|原則|設定登入風險原則|
|身分識別保護|原則管理|原則|設定使用者風險原則|
|身分識別保護|目錄管理|目錄|更新警示設定|
|身分識別保護|目錄管理|目錄|更新每週摘要設定|
|受邀的使用者|使用者管理|User|將外部使用者指派至應用程式|
|受邀的使用者|其他|其他|已處理 Batch 邀請|
|受邀的使用者|其他|其他|已上傳 Batch 邀請|
|受邀的使用者|使用者管理|User|未傳送電子郵件，使用者已取消訂閱|
|受邀的使用者|使用者管理|User|邀請外部使用者|
|受邀的使用者|使用者管理|User|兌換外部使用者的邀請|
|受邀的使用者|使用者管理|User|建立病毒式租用戶|
|受邀的使用者|使用者管理|User|建立病毒式使用者|
|Microsoft Identity Manager (MIM)|群組管理|群組|新增成員|
|Microsoft Identity Manager (MIM)|群組管理|群組|建立群組|
|Microsoft Identity Manager (MIM)|群組管理|群組|刪除群組|
|Microsoft Identity Manager (MIM)|群組管理|群組|移除成員|
|Microsoft Identity Manager (MIM)|群組管理|群組|更新群組|
|Microsoft Identity Manager (MIM)|使用者管理|User|使用者密碼註冊|
|Microsoft Identity Manager (MIM)|使用者管理|User|使用者密碼重設|
|Privileged Identity Management|角色管理|角色|AccessReview_Review|
|Privileged Identity Management|角色管理|角色|AccessReview_Update|
|Privileged Identity Management|角色管理|角色|ActivationAborted|
|Privileged Identity Management|角色管理|角色|ActivationApproved|
|Privileged Identity Management|角色管理|角色|ActivationCanceled|
|Privileged Identity Management|角色管理|角色|ActivationRequested|
|Privileged Identity Management|角色管理|角色|已新增|
|Privileged Identity Management|角色管理|角色|指派|
|Privileged Identity Management|角色管理|角色|提高權限|
|Privileged Identity Management|角色管理|角色|已移除|
|Privileged Identity Management|角色管理|角色|角色設定變更|
|Privileged Identity Management|角色管理|角色|ScanAlertsNow|
|Privileged Identity Management|角色管理|角色|註冊|
|Privileged Identity Management|角色管理|角色|停權|
|Privileged Identity Management|角色管理|角色|UpdateAlertSettings|
|Privileged Identity Management|角色管理|角色|UpdateCurrentState|
|自助式群組管理|群組管理|群組|核准擱置中的要求以加入群組|
|自助式群組管理|群組管理|群組|取消擱置中的要求以加入群組|
|自助式群組管理|群組管理|群組|建立生命週期管理原則|
|自助式群組管理|群組管理|群組|刪除擱置中的要求以加入群組|
|自助式群組管理|群組管理|群組|拒絕擱置中的要求以加入群組|
|自助式群組管理|群組管理|群組|更新群組|
|自助式群組管理|群組管理|群組|要求加入群組|
|自助式群組管理|群組管理|群組|設定動態群組屬性|
|自助式群組管理|群組管理|群組|更新生命週期管理原則|
|自助式密碼管理|使用者管理|User|封鎖自助式密碼重設|
|自助式密碼管理|使用者管理|User|變更密碼 (自助式)|
|自助式密碼管理|目錄管理|目錄|停用目錄的密碼回寫|
|自助式密碼管理|目錄管理|目錄|啟用目錄的密碼回寫|
|自助式密碼管理|使用者管理|User|重設密碼 (由系統管理員)|
|自助式密碼管理|使用者管理|User|重設密碼 (自助式)|
|自助式密碼管理|使用者管理|User|自助式密碼重設流程活動進度|
|自助式密碼管理|使用者管理|User|自助式密碼重設流程活動進度|
|自助式密碼管理|使用者管理|User|解除鎖定使用者帳戶 (自助式)|
|自助式密碼管理|使用者管理|User|使用者已註冊自助式密碼重設|
|使用規定|原則管理|原則|接受使用規定|
|使用規定|原則管理|原則|建立使用規定|
|使用規定|原則管理|原則|拒絕使用規定|
|使用規定|原則管理|原則|刪除使用規定|
|使用規定|原則管理|原則|編輯使用規定|
|使用規定|原則管理|原則|發佈使用規定|
|使用規定|原則管理|原則|解除發佈使用規定|




## <a name="next-steps"></a>後續步驟

如需報告概觀，請參閱 [Azure Active Directory 報告](active-directory-reporting-azure-portal.md)。

