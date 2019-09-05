---
title: Azure Active Directory (Azure AD) 稽核活動參考 | Microsoft Docs
description: 取得在 Azure Active Directory (Azure AD) 中可以記錄於稽核記錄中的稽核活動概觀。
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: aaa60a7737b7781a21e23516d139332f10bdf448
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306020"
---
# <a name="azure-ad-audit-activity-reference"></a>Azure AD 稽核活動參考

透過 Azure Active Directory (Azure AD) 報告，您可以取得判斷環境執行狀況所需的資訊。

Azure AD 中的報告架構包含下列元件：

- **活動報告** 
    - [登入](concept-sign-ins.md) – 提供受控應用程式和使用者登入活動的使用情況相關資訊
    - [稽核記錄](concept-audit-logs.md) - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 
    
- **安全性報告** 
    - [有風險的登入](concept-risky-sign-ins.md) - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。 
    - [標幟為有風險的使用者](concept-user-at-risk.md) - 有風險的使用者表示可能被盜用的使用者帳戶。 

本文列出可以在稽核記錄中記錄的稽核活動。

## <a name="access-reviews"></a>存取檢閱

|稽核類別|activities|
|---|---|
|存取權檢閱|存取權檢閱結束|
|存取權檢閱|將核准者新增至要求核准|
|存取權檢閱|將檢閱者新增至存取權檢閱|
|存取權檢閱|套用存取權檢閱|
|存取權檢閱|建立存取權檢閱|
|存取權檢閱|建立程式|
|存取權檢閱|建立要求核准|
|存取權檢閱|刪除存取權檢閱|
|存取權檢閱|刪除程式|
|存取權檢閱|連結程式控制項|
|存取權檢閱|列於 Azure AD 存取權檢閱上|
|存取權檢閱|從存取權檢閱移除檢閱者|
|存取權檢閱|要求停止檢閱|
|存取權檢閱|要求套用檢閱結果|
|存取權檢閱|檢閱 Rbac 角色成員資格|
|存取權檢閱|檢閱應用程式指派|
|存取權檢閱|檢閱群組成員資格|
|存取權檢閱|檢閱要求核准要求|
|存取權檢閱|取消連結程式控制項|
|存取權檢閱|更新存取權檢閱|
|存取權檢閱|更新 Azure AD 存取權檢閱登入狀態|
|存取權檢閱|更新存取權檢閱郵件通知設定|
|存取權檢閱|更新存取權檢閱週期性計數設定|
|存取權檢閱|更新存取權檢閱週期性持續時間 (天) 設定|
|存取權檢閱|更新存取權檢閱週期性結束類型設定|
|存取權檢閱|更新存取權檢閱週期性類型設定|
|存取權檢閱|更新存取權檢閱提醒設定|
|存取權檢閱|更新程式|
|存取權檢閱|更新要求核准|
|存取權檢閱|使用者已停用|

## <a name="account-provisioning"></a>帳戶佈建

|稽核類別|activities|
|---|---|
|應用程式管理|擷取 V2 應用程式授權|
|應用程式管理|擷取目前租用戶中的 V2 應用程式服務主體|
|應用程式管理|更新 V1 應用程式|
|應用程式管理|更新 V2 應用程式|
|應用程式管理|更新 V2 應用程式授權|
|應用程式管理|新增 OAuth2PermissionGrant|
|應用程式管理|將應用程式角色指派新增至服務主體|

## <a name="application-proxy"></a>應用程式 Proxy

|稽核類別|activities|
|---|---|
|應用程式管理|加入應用程式|
|應用程式管理|將擁有者新增至應用程式|
|應用程式管理|將擁有者新增至服務主體|
|應用程式管理|將原則新增至服務主體|
|目錄管理|新增服務主體|
|目錄管理|新增服務主體認證|
|目錄管理|同意應用程式|
|目錄管理|刪除應用程式|
|目錄管理|實刪除應用程式|
|目錄管理|移除 OAuth2PermissionGrant|
|目錄管理|移除服務主體的應用程式角色指派|
|目錄管理|移除應用程式的擁有者|
|Resource|移除服務主體的擁有者|
|Resource|移除服務主體中的原則|
|Resource|移除服務主體|


## <a name="automated-password-rollover"></a>自動密碼變換

|稽核類別|activities|
|---|---|
|應用程式管理|移除服務主體認證|


## <a name="b2c"></a>B2C

|稽核類別|activities|
|---|---|
|應用程式管理|還原應用程式|
|應用程式管理|撤銷同意|
|應用程式管理|更新應用程式|
|應用程式管理|更新外部密碼|
|應用程式管理|更新服務主體|
|應用程式管理|將存取權杖發給應用程式|
|應用程式管理|將授權碼發給應用程式|
|應用程式管理|將 id_token 發給應用程式|
|應用程式管理|驗證本機帳戶認證|
|應用程式管理|驗證使用者驗證|
|應用程式管理|新增 V2 應用程式權限|
|應用程式管理|將以 ASCII 密碼為基礎的金鑰新增至 CPIM 金鑰容器|
|應用程式管理|將金鑰新增至 CPIM 金鑰容器|
|應用程式管理|AdminPolicyDatas-SetResources|
|應用程式管理|AdminUserJourneys-GetResources|
|應用程式管理|AdminUserJourneys-RemoveResources|
|驗證|AdminUserJourneys-SetResources|
|驗證|Create IdentityProvider|
|驗證|建立 V1 應用程式|
|驗證|建立 V2 應用程式|
|驗證|在租用戶中建立自訂網域|
|Authorization|建立新的 AdminUserJourney|
|Authorization|建立當地語系化資源 json|
|Authorization|建立新的自訂 IDP|
|Authorization|建立新的 IDP|
|Authorization|建立或更新 B2C 目錄資源|
|Authorization|建立原則|
|Authorization|建立 trustFramework 原則|
|Authorization|使用可設定的首碼建立 trustFramework 原則|
|Authorization|建立使用者屬性|
|Authorization|CreateTrustFrameworkPolicy|
|Authorization|建立或更新新的 AdminUserJourney|
|Authorization|刪除 IDP|
|Authorization|建立 IdentityProvider|
|Authorization|刪除 V1 應用程式|
|Authorization|刪除 V2 應用程式|
|Authorization|刪除 V2 應用程式授權|
|Authorization|刪除 B2C 目錄資源|
|Authorization|刪除 CPIM 金鑰容器|
|Authorization|刪除 trustFramework 原則|
|Authorization|刪除使用者屬性|
|Authorization|啟用 B2C 功能|
|Authorization|取得訂用帳戶中的 B2C 目錄資源|
|Authorization|取得自訂 IDP|
|Authorization|取得 IDP|
|Authorization|取得 V1 和 V2 應用程式|
|Authorization|取得 V1 應用程式|
|Authorization|取得 V1 應用程式|
|Authorization|取得 V2 應用程式|
|Authorization|取得 V2 應用程式|
|Authorization|取得 B2C 目錄資源|
|Authorization|取得租用戶中的自訂網域清單|
|Authorization|取得使用者旅程圖|
|Authorization|取得使用者旅程圖的允許應用程式宣告|
|Authorization|取得使用者旅程圖的允許自我判斷宣告|
|Authorization|取得原則的允許自我判斷宣告|
|Authorization|取得可用的輸出宣告清單|
|Authorization|取得使用者旅程圖的內容定義|
|Authorization|取得特定管理流程的 IDP|
|Authorization|取得 JWK 中的金鑰容器作用中金鑰中繼資料|
|Authorization|取得所有管理流程的清單|
|Authorization|取得所有使用者的所有管理流程的標記清單|
|Authorization|取得使用者的租用戶清單|
|Authorization|取得本機帳戶的自我判斷宣告|
|Authorization|取得當地語系化資源 json|
|Authorization|取得 Microsoft.AzureActiveDirectory 資源提供者的作業|
|Authorization|取得原則|
|Authorization|取得原則|
|Authorization|取得租用戶的資源屬性|
|Authorization|取得支援的 IDP 清單|
|Authorization|取得使用者旅程圖的支援 IDP 清單|
|Authorization|取得租用戶資訊|
|Authorization|取得租用戶允許的功能|
|Authorization|取得租用戶定義的自訂 IDP 清單|
|Authorization|取得租用戶定義的 IDP 清單|
|Authorization|取得租用戶定義的本機 IDP 清單|
|Authorization|取得使用者的租用戶詳細資料以便建立資源|
|Authorization|取得租用戶清單|
|Authorization|取得 tenantDomains|
|Authorization|取得 CPIM 的預設支援文化特性|
|Authorization|取得管理流程的詳細資料|
|Authorization|取得此租用戶的 UserJourneys 清單|
|Authorization|取得 CPIM 可用的支援文化特性集合|
|Authorization|取得 trustFramework 原則|
|Authorization|取得 xml 格式的 trustFramework 原則|
|Authorization|取得使用者屬性|
|Authorization|取得使用者屬性|
|Authorization|取得使用者旅程圖清單|
|Authorization|GetIEFPolicies|
|Authorization|GetIdentityProviders|
|Authorization|GetTrustFrameworkPolicy|
|Authorization|取得 jwk 格式的 CPIM 金鑰容器|
|Authorization|取得租用戶中的金鑰容器清單|
|Authorization|取得租用戶的類型|
|Authorization|MigrateTenantMetadata|
|Authorization|修補程式 IdentityProvider|
|Authorization|PutTrustFrameworkPolicy|
|Authorization|PutTrustFrameworkpolicy|
|Authorization|移除使用者旅程圖|
|Authorization|還原 CPIM 金鑰容器備份|
|Authorization|擷取 V2 應用程式授權|
|Authorization|擷取目前租用戶中的 V2 應用程式服務主體|
|Authorization|更新自訂 IDP|
|Authorization|更新 IDP|
|Authorization|更新本機 IDP|
|Authorization|更新 V1 應用程式|
|Authorization|更新 V2 應用程式|
|Authorization|更新 V2 應用程式授權|
|Authorization|更新原則|
|Authorization|更新使用者屬性|
|Authorization|上傳 CPIM 加密的金鑰|
|Authorization|使用者授權：已停用租用戶功能集的 API|
|Authorization|使用者授權：使用者以「租用戶管理員」身分授與存取權|
|Authorization|使用者授權：使用者獲取「已驗證的使用者」存取權限|
|Authorization|驗證是否已啟用 B2C 功能|
|Authorization|驗證是否已啟用功能|
|Authorization|建立程式|
|Authorization|刪除程式|
|Authorization|連結程式控制項|
|Authorization|列於 Azure AD 存取權檢閱上|
|Authorization|取消連結程式控制項|
|Authorization|更新程式|
|Authorization|停用桌面 Sso|
|Authorization|停用特定網域的桌面 Sso|
|Authorization|停用應用程式 Proxy|
|Authorization|停用傳遞驗證|
|Authorization|啟用桌面 Sso|
|目錄管理|啟用特定網域的桌面 Sso|
|目錄管理|啟用應用程式 Proxy|
|目錄管理|啟用傳遞驗證|
|目錄管理|在租用戶中建立自訂網域|
|目錄管理|啟用 B2C 功能|
|目錄管理|取得租用戶中的自訂網域清單|
|目錄管理|取得租用戶的資源屬性|
|目錄管理|取得租用戶資訊|
|目錄管理|取得租用戶允許的功能|
|目錄管理|取得 tenantDomains|
|Key|取得租用戶的類型|
|Key|驗證是否已啟用 B2C 功能|
|Key|驗證是否已啟用功能|
|Key|將夥伴新增至公司|
|Key|新增未驗證的網域|
|Key|新增已驗證的網域|
|Key|建立公司|
|Key|建立公司設定|
|Key|刪除公司設定|
|Key|將夥伴降級|
|Key|已刪除的目錄|
|其他|永久刪除的目錄|
|其他|排程刪除的目錄|
|Resource|將公司升級為夥伴|
|Resource|清除 Rights Management 屬性|
|Resource|移除公司的夥伴|
|Resource|移除未驗證的網域|
|Resource|移除已驗證的網域|
|Resource|設定公司資訊|
|Resource|設定 DirSync 功能|
|Resource|設定 DirSyncEnabled 旗標|
|Resource|設定合作關係|
|Resource|設定意外刪除閾值|
|Resource|設定公司允許的資料位置|
|Resource|設定啟用公司多語系功能|
|Resource|在租用戶上設定目錄功能|
|Resource|設定網域驗證|
|Resource|設定網域的同盟設定|
|Resource|設定密碼原則|
|Resource|設定 Rights Management 屬性|
|Resource|更新公司|
|Resource|更新公司設定|
|Resource|更新網站|
|Resource|驗證網域|
|Resource|驗證電子郵件驗證的網域|
|Resource|上架|
|Resource|更新警示設定|
|Resource|更新每週摘要設定|
|Resource|停用目錄的密碼回寫|
|Resource|啟用目錄的密碼回寫|
|Resource|將應用程式角色指派新增至群組|
|Resource|加入群組|
|Resource|將成員新增到群組|
|Resource|將擁有者新增至群組|
|Resource|建立群組設定|
|Resource|刪除群組|
|Resource|刪除群組設定|
|Resource|完成將群組型授權套用至使用者|
|Resource|實刪除群組|
|Resource|移除群組的應用程式角色指派|
|Resource|從群組移除成員|
|Resource|移除群組的擁有者|
|Resource|還原群組|
|Resource|設定群組授權|
|Resource|將群組設定為由使用者管理|
|Resource|開始將群組型授權套用至使用者|
|Resource|觸發群組授權重新計算|
|Resource|更新群組|
|Resource|更新群組設定|
|Resource|新增成員|
|Resource|建立群組|
|Resource|刪除群組|
|Resource|移除成員|
|Resource|更新群組|
|Resource|核准擱置中的要求以加入群組|
|Resource|取消擱置中的要求以加入群組|
|Resource|建立生命週期管理原則|
|Resource|刪除擱置中的要求以加入群組|
|Resource|拒絕擱置中的要求以加入群組|
|Resource|續約群組|
|Resource|要求加入群組|
|Resource|設定動態群組屬性|
|Resource|更新生命週期管理原則|
|Resource|將以 ASCII 密碼為基礎的金鑰新增至 CPIM 金鑰容器|
|Resource|將金鑰新增至 CPIM 金鑰容器|
|Resource|刪除 CPIM 金鑰容器|
|Resource|刪除金鑰容器|
|Resource|取得 JWK 中的金鑰容器作用中金鑰中繼資料|
|Resource|取得金鑰容器中繼資料|
|Resource|取得 jwk 格式的 CPIM 金鑰容器|
|Resource|取得租用戶中的金鑰容器清單|
|Resource|還原 CPIM 金鑰容器備份|
|Resource|儲存金鑰容器|
|Resource|上傳 CPIM 加密的金鑰|
|Resource|將授權碼發給應用程式|
|Resource|將 id_token 發給應用程式|


## <a name="core-directory"></a>核心目錄

|稽核類別|activities|
|---|---|
|管理單位的管理|下載單一風險偵測類型|
|管理單位的管理|下載加入每週摘要的管理員和狀態|
|管理單位的管理|下載所有風險偵測類型|
|管理單位的管理|下載免費的使用者風險偵測|
|管理單位的管理|下載標幟為有風險的使用者|
|應用程式管理|已處理 Batch 邀請|
|應用程式管理|已上傳 Batch 邀請|
|應用程式管理|將擁有者新增至原則|
|應用程式管理|新增原則|
|應用程式管理|刪除原則|
|應用程式管理|移除原則認證|
|應用程式管理|更新原則|
|應用程式管理|設定 MFA 註冊原則|
|應用程式管理|設定登入風險原則|
|應用程式管理|設定使用者風險原則|
|應用程式管理|接受使用規定|
|應用程式管理|建立使用規定|
|應用程式管理|拒絕使用規定|
|應用程式管理|刪除使用規定|
|應用程式管理|編輯使用規定|
|應用程式管理|發佈使用規定|
|應用程式管理|解除發佈使用規定|
|應用程式管理|新增應用程式 SSL 憑證|
|應用程式管理|刪除 SSL 繫結|
|應用程式管理|註冊連接器|
|應用程式管理|AdminPolicyDatas-RemoveResources|
|應用程式管理|AdminPolicyDatas-SetResources|
|應用程式管理|AdminUserJourneys-GetResources|
|目錄管理|AdminUserJourneys-RemoveResources|
|目錄管理|AdminUserJourneys-SetResources|
|目錄管理|Create IdentityProvider|
|目錄管理|建立新的 AdminUserJourney|
|目錄管理|建立當地語系化資源 json|
|目錄管理|建立新的自訂 IDP|
|目錄管理|建立新的 IDP|
|目錄管理|建立或更新 B2C 目錄資源|
|目錄管理|建立原則|
|目錄管理|建立 trustFramework 原則|
|目錄管理|使用可設定的首碼建立 trustFramework 原則|
|目錄管理|建立使用者屬性|
|目錄管理|CreateTrustFrameworkPolicy|
|目錄管理|刪除 IDP|
|目錄管理|建立 IdentityProvider|
|目錄管理|刪除 B2C 目錄資源|
|目錄管理|刪除 trustFramework 原則|
|目錄管理|刪除使用者屬性|
|目錄管理|取得資源群組中的 B2C 目錄資源|
|目錄管理|取得訂用帳戶中的 B2C 目錄資源|
|目錄管理|取得自訂 IDP|
|目錄管理|取得 IDP|
|目錄管理|取得 B2C 目錄資源|
|目錄管理|取得使用者旅程圖|
|目錄管理|取得使用者旅程圖的允許應用程式宣告|
|目錄管理|取得使用者旅程圖的允許自我判斷宣告|
|目錄管理|取得原則的允許自我判斷宣告|
|目錄管理|取得可用的輸出宣告清單|
|目錄管理|取得使用者旅程圖的內容定義|
|目錄管理|取得特定管理流程的 IDP|
|目錄管理|取得所有管理流程的清單|
|目錄管理|取得所有使用者的所有管理流程的標記清單|
|群組管理|大量下載群組成員-已啟動|
|群組管理|大量下載群組成員-已完成|
|群組管理|大量匯入群組成員-已啟動|
|群組管理|大量匯入群組成員-已完成|
|群組管理|大量移除群組成員-已啟動|
|群組管理|大量移除群組成員-已完成|
|群組管理|大量下載群組-已啟動|
|群組管理|大量下載群組-已完成|
|群組管理|取得使用者的租用戶清單|
|群組管理|取得本機帳戶的自我判斷宣告|
|群組管理|取得當地語系化資源 json|
|群組管理|取得 Microsoft.AzureActiveDirectory 資源提供者的作業|
|群組管理|取得原則|
|群組管理|取得原則|
|群組管理|取得支援的 IDP 清單|
|群組管理|取得使用者旅程圖的支援 IDP 清單|
|群組管理|取得租用戶定義的自訂 IDP 清單|
|群組管理|取得租用戶定義的 IDP 清單|
|群組管理|取得租用戶定義的本機 IDP 清單|
|群組管理|取得使用者的租用戶詳細資料以便建立資源|
|群組管理|取得 CPIM 的預設支援文化特性|
|群組管理|取得管理流程的詳細資料|
|群組管理|取得此租用戶的 UserJourneys 清單|
|群組管理|取得 CPIM 可用的支援文化特性集合|
|群組管理|取得 trustFramework 原則|
|群組管理|取得 xml 格式的 trustFramework 原則|
|群組管理|取得使用者屬性|
|原則管理|取得使用者屬性|
|原則管理|取得使用者旅程圖清單|
|原則管理|GetIEFPolicies|
|原則管理|GetIdentityProviders|
|原則管理|GetTrustFrameworkPolicy|
|Resource|MigrateTenantMetadata|
|Resource|移動資源|
|Resource|修補程式 IdentityProvider|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|移除使用者旅程圖|
|Resource|更新自訂 IDP|
|Resource|更新 IDP|
|Resource|更新本機 IDP|
|Resource|更新 B2C 目錄資源|
|Resource|更新原則|
|Resource|更新訂用帳戶狀態|
|角色管理|更新使用者屬性|
|角色管理|驗證移動資源|
|角色管理|新增裝置|
|角色管理|新增裝置組態|
|角色管理|將已註冊的擁有者新增至裝置|
|角色管理|將已註冊的使用者新增至裝置|
|角色管理|刪除裝置|
|角色管理|刪除裝置組態|
|角色管理|不再符合規範的裝置|
|角色管理|不再受控的裝置|
|使用者管理|AccessReview_Review|
|使用者管理|AccessReview_Update|
|使用者管理|ActivationAborted|
|使用者管理|ActivationApproved|
|使用者管理|ActivationCanceled|
|使用者管理|ActivationRequested|
|使用者管理|將合格成員新增至角色|
|使用者管理|將成員新增至角色|
|使用者管理|將角色指派新增至角色定義|
|使用者管理|從範本新增角色|
|使用者管理|將範圍成員新增至角色|
|使用者管理|已新增|
|使用者管理|指派|
|使用者管理|大量建立使用者-已啟動|
|使用者管理|大量建立使用者-已完成|
|使用者管理|大量刪除使用者-已啟動|
|使用者管理|大量刪除使用者-已完成|
|使用者管理|大量下載使用者-已啟動|
|使用者管理|大量下載使用者-已完成|
|使用者管理|大量還原已刪除的使用者-已啟動|
|使用者管理|大量還原已刪除的使用者-已完成|
|使用者管理|大量邀請使用者-已啟動|
|使用者管理|大量邀請使用者-已完成|
|使用者管理|移除裝置中已註冊的擁有者|
|使用者管理|移除裝置中已註冊的使用者|
|使用者管理|移除角色的合格成員|
|使用者管理|從角色移除成員|
|使用者管理|移除角色定義中的角色指派|
|使用者管理|移除角色的範圍成員|
|使用者管理|更新裝置|
|使用者管理|更新裝置組態|
|使用者管理|更新角色|






## <a name="identity-protection"></a>身分識別保護

|稽核類別|activities|
|---|---|
|目錄管理|提高權限|
|目錄管理|已移除|
|目錄管理|角色設定變更|
|其他|ScanAlertsNow|
|其他|註冊|
|其他|停權|
|其他|UpdateAlertSettings|
|其他|UpdateCurrentState|
|原則管理|存取權檢閱結束|
|原則管理|將核准者新增至要求核准|
|原則管理|將檢閱者新增至存取權檢閱|
|使用者管理|套用存取權檢閱|
|使用者管理|建立存取權檢閱|


## <a name="invited-users"></a>受邀的使用者

|稽核類別|activities|
|---|---|
|其他|建立要求核准|
|其他|刪除存取權檢閱|
|使用者管理|從存取權檢閱移除檢閱者|
|使用者管理|要求套用檢閱結果|
|使用者管理|要求停止檢閱|
|使用者管理|檢閱應用程式指派|
|使用者管理|檢閱群組成員資格|
|使用者管理|檢閱 Rbac 角色成員資格|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|稽核類別|activities|
|---|---|
|群組管理|檢閱要求核准要求|
|群組管理|更新存取權檢閱|
|群組管理|更新存取權檢閱郵件通知設定|
|群組管理|更新存取權檢閱週期性計數設定|
|群組管理|更新存取權檢閱週期性持續時間 (天) 設定|
|使用者管理|更新存取權檢閱週期性結束類型設定|
|使用者管理|更新存取權檢閱週期性類型設定|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|稽核類別|activities|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|已新增|
|PIM|AddedOutsidePIM|
|PIM|指派|
|PIM|DismissAlert|
|PIM|提高權限|
|PIM|ReactivateAlert|
|PIM|已移除|
|PIM|RemovedOutsidePIM|
|PIM|要求停止檢閱|
|PIM|角色設定變更|
|PIM|ScanAlertsNow|
|PIM|註冊|
|PIM|取消指派|
|PIM|停權|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>自助式群組管理

|稽核類別|activities|
|---|---|
|群組管理|重設使用者密碼|
|群組管理|還原使用者|
|群組管理|設定強制變更使用者密碼|
|群組管理|設定使用者管理員|
|群組管理|設定啟用使用者 oath 權杖中繼資料|
|群組管理|更新 StsRefreshTokenValidFrom 時間戳記|
|群組管理|更新外部密碼|
|群組管理|更新使用者|
|群組管理|管理員產生臨時密碼|


## <a name="self-service-password-management"></a>自助式密碼管理

|稽核類別|activities|
|---|---|
|目錄管理|管理員要求使用者重設密碼|
|目錄管理|將外部使用者指派至應用程式|
|使用者管理|未傳送電子郵件，使用者已取消訂閱|
|使用者管理|邀請外部使用者|
|使用者管理|兌換外部使用者的邀請|
|使用者管理|建立病毒式租用戶|
|使用者管理|建立病毒式使用者|
|使用者管理|使用者密碼註冊|
|使用者管理|使用者密碼重設|
|使用者管理|封鎖自助式密碼重設|


## <a name="terms-of-use"></a>使用規定

|稽核類別|activities|
|---|---|
|使用規定|接受使用規定|
|使用規定|建立使用規定|
|使用規定|拒絕使用規定|
|使用規定|刪除同意|
|使用規定|刪除使用規定|
|使用規定|編輯使用規定|
|使用規定|將使用規定設為過期|
|使用規定|永久刪除使用規定|
|使用規定|發佈使用規定|
|使用規定|解除發佈使用規定|


## <a name="next-steps"></a>後續步驟

- [Azure AD 報告概觀](overview-reports.md)。
- [稽核記錄報告](concept-audit-logs.md)。 
- [以程式設計方式存取 Azure AD 報告](concept-reporting-api.md)
