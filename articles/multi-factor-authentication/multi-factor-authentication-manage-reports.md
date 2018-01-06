---
title: "Azure MFA 的存取及使用報告 | Microsoft Docs"
description: "說明如何使用 Azure Multi-Factor Authentication 功能 - 報告。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 696f4ae3cb479a208e73e53a9a9a437caeabd294
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2018
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 中的報告

Azure Multi-factor Authentication 提供數個可供您和貴組織可透過 Azure 入口網站存取的報表。 下表列出可用的報告：

| 報告 | 位置 | 說明 |
|:--- |:--- |:--- |
| 封鎖的使用者歷程記錄 | Azure AD > MFA Server > 封鎖/解除封鎖使用者 | 顯示要求封鎖或解除封鎖使用者的歷程記錄。 |
| 使用方式和詐騙警示 | Azure AD > 登入 | 提供有關整體使用量、 使用者摘要和使用者詳細資料。與指定的日期範圍內已提交詐騙警示的歷程記錄。 |
| 在內部部署元件的使用方式 | Azure AD > MFA Server > 活動報表 | 提供有關整體使用量的 MFA 透過 NPS 擴充功能的 ADFS，和 MFA 伺服器。 |
| 許可的使用者歷程記錄 | Azure AD > MFA Server > 單次許可 | 提供要針對使用者許可 Multi-factor Authentication 的要求歷程記錄。 |
| 伺服器狀態 | Azure AD > MFA Server > 伺服器狀態 | 顯示與您的帳戶相關聯的多重要素驗證伺服器的狀態。 |

## <a name="view-reports"></a>檢視報告 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側選取**Azure Active Directory** > **MFA Server**。
3. 選取您想要檢視的報表。

   <center>![雲端](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell 報告

識別已註冊為使用 PowerShell 後面的 MFA 的使用者。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

識別沒有註冊為使用 PowerShell 後面的 MFA 的使用者。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>後續步驟

* [適用於使用者](end-user/multi-factor-authentication-end-user.md)
* [如何部署](multi-factor-authentication-get-started.md)
