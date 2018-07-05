---
title: Azure MFA 的存取及使用報告 | Microsoft Docs
description: 說明如何使用 Azure Multi-Factor Authentication 功能 - 報告。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 4eb91e37331a5af064d2af0e937eb071d805688f
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097874"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 中的報告

Azure Multi-Factor Authentication 提供數個報告，可供您和貴組織透過 Azure 入口網站使用。 下表列出可用的報告：

| 報告 | 位置 | 說明 |
|:--- |:--- |:--- |
| 已封鎖的使用者歷程記錄 | Azure AD > MFA Server > 封鎖/解除封鎖使用者 | 顯示使用者封鎖或解除封鎖要求的歷程記錄。 |
| 使用方式和詐騙警示 | Azure AD > 登入 | 提供整體使用量、使用者摘要和使用者詳細資料的相關資訊；以及在指定的日期範圍期間所提交的詐騙警示歷程記錄。 |
| 內部部署元件的使用方式 | Azure AD > MFA Server > 活動報表 | 透過 NPS 擴充功能、ADFS 和 MFA 伺服器提供 MFA 整體使用量的相關資訊。 |
| 已略過的使用者歷程記錄 | Azure AD > MFA Server > 單次許可 | 提供針對使用者許可 Multi-Factor Authentication 之要求的歷程記錄。 |
| 伺服器狀態 | Azure AD > MFA Server > 伺服器狀態 | 顯示與帳戶相關聯之 Multi-Factor Authentication Server 的狀態。 |

## <a name="view-reports"></a>檢視報告 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側，選取 [Azure Active Directory] > [MFA Server]。
3. 選取您要檢視的報告。

   <center>![雲端](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>Powershell 報告

識別已使用下列 Powershell 註冊 MFA 的使用者。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

識別尚未使用下列 Powershell 註冊 MFA 的使用者。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>後續步驟

* [適用於使用者](end-user/current/multi-factor-authentication-end-user.md)
* [部署的位置](concept-mfa-whichversion.md)
