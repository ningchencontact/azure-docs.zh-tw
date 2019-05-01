---
title: 在 Azure AD 權限管理 （預覽）-Azure Active Directory 中檢視報告與記錄檔
description: 了解如何檢視使用者指派報告和稽核在 Azure Active Directory 權限管理 （預覽） 中的記錄檔。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541536"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>檢視報表並登入 Azure AD 權限管理 （預覽）

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="view-resources-a-user-has-access-to"></a>檢視使用者擁有存取權的資源

1. 按一下  **Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**指派的使用者報告**。

1. 按一下 **選取使用者**以開啟 選取使用者 窗格。

1. 使用者在清單中尋找您想要檢視他們擁有存取權的資源。

1. 按一下使用者，然後按一下**選取**。

    會顯示一份使用者擁有存取權的資源。 它包含存取套件、 原則和日期。

    ![使用者指派報告](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>判斷使用者的要求狀態

若要取得使用者如何要求並接收存取套件的存取權的其他詳細資料，您可以使用 Azure AD 稽核記錄檔。 特別是，您可以使用中的記錄檔記錄`EntitlementManagement`和`UserManagement`類別，以取得其他詳細資料上的處理步驟，針對每個要求。  

1. 按一下  **Azure Active Directory** ，然後按一下**稽核記錄檔**。

1. 在頂端，變更**分類**設為`EntitlementManagement`或`UserManagement`，取決於您要尋找之稽核記錄。  

1. 按一下 [套用]。

1. 若要下載記錄，請按一下**下載**。

當 Azure AD 收到新的要求時，它將在其中寫入稽核記錄，**分類**是`EntitlementManagement`並**活動**通常是`User requests access package assignment`。  在 Azure 入口網站中建立的直接指派的情況下**活動**稽核記錄的欄位都`Administrator directly assigns user to access package`，並由執行指派的使用者識別**ActorUserPrincipalName**.

Azure AD 會寫入其他稽核記錄，而要求正在進行中，包括：

| Category | 活動 | 要求狀態 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 要求不需要核准 |
| `UserManagement` | `Create request approval` | 要求需要核准 |
| `UserManagement` | `Add approver to request approval` | 要求需要核准 |
| `EntitlementManagement` | `Approve access package assignment request` | 要求核准 |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |要求核准，或不需要核准 |

Azure AD 使用者指派存取權時，寫入稽核記錄`EntitlementManagement`分類**活動** `Fulfill access package assignment`。  收到的存取權的使用者由**ActorUserPrincipalName**欄位。

如果未指派的存取，則 Azure AD 將寫入的稽核記錄`EntitlementManagement`分類**活動**任一`Deny access package assignment request`，如果要求遭到拒絕的核准者，或`Access package assignment request timed out (no approver action taken)`，如果要求已逾時之前核准者可以核准。

當使用者的存取封裝指派到期時，由使用者取消或移除系統管理員，然後 Azure AD 將寫入的稽核記錄`EntitlementManagement`分類**活動**的`Remove access package assignment`。

## <a name="next-steps"></a>後續步驟

- [疑難排解 Azure AD 權限管理](entitlement-management-troubleshoot.md)
- [常見案例](entitlement-management-scenarios.md)
