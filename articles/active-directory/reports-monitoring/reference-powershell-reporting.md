---
title: Azure AD 用於報告的 PowerShell Cmdlet |Microsoft Docs
description: 適用于報告的 Azure AD PowerShell Cmdlet 參考。
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
ms.date: 07/12/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 372041dc24db4ed66977364e2a2997c7195cca9c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988042"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>適用於報表的 Azure AD PowerShell Cmdlet

有了 Azure Active Directory (Azure AD) 報表, 您可以在方向 (audit logs) 和驗證資料 (登入記錄) 中取得有關所有寫入作業的活動詳細資料。 雖然可以使用 MS 圖形 API 來取得資訊, 但現在您可以使用 Azure AD PowerShell Cmdlet 來抓取相同的資料, 以進行報告。

本文概要說明用於審查記錄和登入記錄的 PowerShell Cmdlet。

## <a name="audit-logs"></a>稽核記錄

[Audit 記錄](concept-audit-logs.md)可讓您透過記錄來追蹤 Azure AD 內各種功能所完成的所有變更。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。

您可以使用 ' AzureADAuditDirectoryLogs Cmdlet 取得 audit 記錄的存取權。


| 狀況                      | PowerShell 命令 |
| :--                           | :--                |
| 應用程式顯示名稱      | AzureADAuditDirectoryLogs-篩選 "initiatedBy/app/displayName eq ' Azure AD 雲端同步處理 '" |
| Category                      | AzureADAuditDirectoryLogs-篩選 "category eq ' 應用程式管理 '" |
| 活動日期時間            | AzureADAuditDirectoryLogs-Filter "activityDateTime gt 2019-04-18" |
| 以上皆是              | AzureADAuditDirectoryLogs-篩選 "initiatedBy/app/displayName eq ' Azure AD 雲端同步處理 ' 和分類 eq ' 應用程式管理 ' 和 activityDateTime gt 2019-04-18"|


下圖顯示此命令的範例。 

![[資料摘要] 按鈕](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>登入記錄

登[入](concept-sign-ins.md)記錄提供受控應用程式和使用者登入活動的使用方式相關資訊。

您可以使用 AzureADAuditSignInLogs Cmdlet 來存取登入記錄。


| 狀況                      | PowerShell 命令 |
| :--                           | :--                |
| 使用者顯示名稱             | AzureADAuditSignInLogs-Filter "Event.pushnotification.userdisplayname eq ' Timothy Perkins '" |
| 建立日期時間              | AzureADAuditSignInLogs-Filter "createdDateTime gt 2019-04-18T17:30: 00.0 Z" (自5:30 年 pm 起的所有專案, 于 4/18) |
| 狀態                        | AzureADAuditSignInLogs-Filter "status/errorCode eq 50105" |
| 應用程式顯示名稱      | AzureADAuditSignInLogs-Filter "appDisplayName eq ' StoreFrontStudio [wsfed enabled] '" |
| 以上皆是              | AzureADAuditSignInLogs-Filter "Event.pushnotification.userdisplayname eq ' Timothy Perkins ' and status/errorCode ne 0 and appDisplayName eq ' StoreFrontStudio [wsfed enabled] '" |


下圖顯示此命令的範例。 

![[資料摘要] 按鈕](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>後續步驟

- [Azure AD 報告概觀](overview-reports.md)。
- [稽核記錄報告](concept-audit-logs.md)。 
- [以程式設計方式存取 Azure AD 報告](concept-reporting-api.md)
