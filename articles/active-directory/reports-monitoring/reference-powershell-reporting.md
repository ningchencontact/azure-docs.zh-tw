---
title: Azure AD 用於報告的 PowerShell Cmdlet |Microsoft Docs
description: 適用于報告的 Azure AD PowerShell Cmdlet 參考。
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27fa3d7be5238527f86e9dfde3be70ae09259d69
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302743"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>適用於報表的 Azure AD PowerShell Cmdlet

透過 Azure Active Directory (Azure AD) 報告，您可以取得判斷環境執行狀況所需的資訊。 您可以使用適用于報告的 Azure AD PowerShell Cmdlet 來捕獲報表資料。

本文提供 Cmdlet 的總覽。




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
