---
title: Azure Active Directory 修正群組動態成員資格的問題 | Microsoft Docs
description: Azure AD 中群組動態成員資格的疑難排解秘訣。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eededcc180d7652fd52c79b85ca3c34f65a22a4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60469697"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>疑難排解並解決群組問題

## <a name="troubleshooting-group-creation-issues"></a>群組建立問題進行疑難排解

**我已停用安全性的群組建立在 Azure 入口網站中，但仍然可以透過 Powershell 建立群組** **使用者可以在 Azure 入口網站中建立安全性群組** 設定在 Azure 入口網站的控制項，zda bude 非系統管理員使用者可以在 「 存取面板或 Azure 入口網站中建立安全性群組。 它不會控制透過 Powershell 建立安全性的群組。

若要停用的非系統管理員身分在 Powershell 中的使用者群組建立：
1. 請確認允許非管理使用者建立群組：
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. 如果它傳回 `UsersPermissionToCreateGroupsEnabled : True`，非管理使用者就可以建立群組。 若要停用這項功能：
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**我收到允許錯誤，嘗試在 Powershell 中建立動態群組時的最大群組**<br/>
如果您收到一則訊息中指出的 Powershell_動態群組原則的最大值允許的群組計數達到_，這表示您已達到最大限制動態群組中您的租用戶。 每個租用戶的動態群組的最大數量為 5,000。

若要建立任何新的動態群組，您必須先刪除一些現有的動態群組。 沒有任何方法，以提高限制。

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>疑難排解群組的動態成員資格

**我針對某個群組設定了一個規則，但是群組中的成員資格沒有任何更新**<br/>
1. 確認使用者或裝置屬性，在規則中的值。 請確定有符合規則的使用者。 對於裝置，請檢查裝置內容，以確保任何同步處理的屬性包含預期的值。<br/>
2. 請檢查成員資格處理狀態，確認它是否完整。 您可以檢查[處理狀態的成員資格](groups-create-rule.md#check-processing-status-for-a-rule)上次更新日期上**概觀**群組的頁面。

如果一切良好，請等候一段時間，以填入群組。 根據您的租用戶大小，群組可能最多需要 24 小時來完成初次填入或規則變更。

**我已設定一個規則，但是目前現有的規則成員已被移除**<br/>這是預期行為。 因為在啟用或變更規則時，現有群組的成員會遭到移除。 從評估規則所傳回的使用者會當作成員，新增至群組。

**當我新增或變更規則時，沒有立即看到成員資格變更，為什麼？**<br/>專用的成員資格評估會定期在非同步的背景處理序中完成。 該程序所需的時間，取決於您目錄中的使用者數目以及因規則所建立的群組大小。 具有少量使用者的目錄通常會在很短的時間內看到群組成員資格變更。 具有大量使用者的目錄則可能需要 30 分鐘或更長的時間填入。

**如何強制立即處理群組？**<br/>
目前沒有辦法自動觸發程序會視需要處理的群組。 不過，您可以手動觸發藉由更新至結尾加入空白的成員資格規則，重新處理。  

**我遇到規則處理錯誤**<br/>下表列出常見動態成員資格規則錯誤及其更正方式。

| 規則剖析器錯誤 | 錯誤的使用方式 | 更正的使用方式 |
| --- | --- | --- |
| Error:不支援屬性。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>請確定此屬性位於[支援的屬性清單](groups-dynamic-membership.md#supported-properties)中。 |
| Error:屬性不支援運算子。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>屬性類型不支援使用的運算子 (在此範例中，-contains 不能在布林類型上使用)。 屬為屬性類型使用正確的運算子。 |
| Error:查詢編譯錯誤。 | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2.  (user.userPrincipalName -match "*@domain.ext") | 1.缺少運算子。 使用這兩個 -and 或 -or 聯結述詞<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2.使用 -match 的規則運算式發生錯誤<br>(user.userPrincipalName -match ".*@domain.ext")<br>或者：(user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>後續步驟

這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組來管理資源的存取權](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory 中的應用程式管理](../manage-apps/what-is-application-management.md)
* [什麼是 Azure Active Directory？](../fundamentals/active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)