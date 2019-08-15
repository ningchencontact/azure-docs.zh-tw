---
title: 在 Azure AD 權利管理 (預覽) 中查看報告和記錄-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理 (預覽) 中查看使用者指派報告和審核記錄。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2420fc25795ec74939649fb8a17ead7c8cfdd1df
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032439"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 權利管理 (預覽) 中查看報告和記錄

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用者指派報告和 Azure Active Directory audit 記錄檔會提供您目錄中使用者的其他詳細資料。 身為系統管理員, 您可以查看使用者有權存取的資源, 並查看要求記錄以進行審核, 或判斷使用者要求的狀態。 本文說明如何使用「使用者指派」報告和 Azure AD 的「審核記錄」。

觀看下列影片, 以瞭解如何使用權利管理來管理 Azure Active Directory 中的使用者存取:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-resources-a-user-has-access-to"></a>查看使用者可存取的資源

1. 按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**使用者指派報表**]。

1. 按一下 [**選取使用者**] 以開啟 [選取使用者] 窗格。

1. 在清單中尋找您想要查看其可存取之資源的使用者。

1. 按一下該使用者, 然後按一下 [**選取**]。

    隨即會顯示使用者可存取的資源清單。 其中包含存取封裝、原則和日期。

    ![使用者指派報告](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>判斷使用者要求的狀態

若要取得使用者如何要求和存取存取套件的其他詳細資料, 您可以使用 Azure AD audit 記錄檔。 特別是, 您可以使用`EntitlementManagement`和`UserManagement`類別中的記錄檔記錄, 取得每個要求之處理步驟的其他詳細資料。  

1. 按一下  **Azure Active Directory** , 然後按一下  **Audit logs**。

1. 在頂端, 根據您要尋找的 audit `EntitlementManagement`記錄`UserManagement`, 將**類別**變更為或。  

1. 按一下 **[套用]** 。

1. 若要下載記錄檔, 請按一下 [**下載**]。

當 Azure AD 收到新的要求時, 它會寫入一個 audit 記錄, 其中的分類`EntitlementManagement`為, 而**活動**通常`User requests access package assignment`為。  在 Azure 入口網站中建立直接指派的情況下, audit 記錄的 [**活動**] 欄位是`Administrator directly assigns user to access package`, 而執行指派的使用者則是由**ActorUserPrincipalName**所識別。

Azure AD 會在要求進行時寫入其他的審核記錄, 包括:

| Category | activities | 要求狀態 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 要求不需要核准 |
| `UserManagement` | `Create request approval` | 要求需要核准 |
| `UserManagement` | `Add approver to request approval` | 要求需要核准 |
| `EntitlementManagement` | `Approve access package assignment request` | 要求已核准 |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |要求已核准, 或不需要核准 |

將存取權指派給使用者時, Azure AD 會寫入具有**活動** `Fulfill access package assignment`之`EntitlementManagement`類別目錄的 audit 記錄。  收到存取權的使用者是以**ActorUserPrincipalName**欄位識別。

如果未指派存取權, 則 Azure AD 會在核准者拒絕要求`EntitlementManagement`時,以活動`Deny access package assignment request`寫入類別目錄的審核記錄, 或者`Access package assignment request timed out (no approver action taken)`, 如果要求在核准者核准之前就超時。

當使用者的存取套件指派過期時, 使用者會取消, 或由系統管理員移除, 然後 Azure AD 寫入具有`EntitlementManagement` **活動**之`Remove access package assignment`類別目錄的 audit 記錄。

## <a name="next-steps"></a>後續步驟

- [針對 Azure AD 權利管理進行疑難排解](entitlement-management-troubleshoot.md)
- [常見案例](entitlement-management-scenarios.md)
