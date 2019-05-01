---
title: Azure AD 權限管理 （預覽）-Azure Active Directory 疑難排解
description: 您應該檢查，以協助您疑難排解 Azure Active Directory 權限管理 （預覽） 了解某些項目。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/25/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ca7955186d6c6fb1dff2acd6a2d5badd1cedaef
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541506"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>疑難排解 Azure AD 權限管理 （預覽）

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此文章說明您應該檢查，以協助您疑難排解 Azure Active Directory (Azure AD) 權利管理某些項目。

## <a name="checklist-for-entitlement-management-administration"></a>權限管理的檢查清單

* 如果您收到拒絕存取訊息時設定權限管理，但必須是全域管理員，請確定您的目錄有[Azure AD Premium P2 （或 EMS E5） 的授權](entitlement-management-overview.md#prerequisites)。  
* 如果您收到存取遭拒的訊息時建立或檢視存取套件，而且您目錄的建立者群組的成員，您必須建立在之前建立第一個存取套件的目錄。

## <a name="checklist-for-adding-a-resource"></a>新增資源的檢查清單

* 如果有已指派至您想要管理存取套件的資源的使用者，請確定使用者被指派至適當的原則與存取封裝。 比方說，您可能要在已經有群組中的 使用者存取封裝中納入的群組。 如果這些使用者群組需要連續存取權，他們必須存取套件的適當原則，讓群組不會遺失其存取權。 詢問使用者要求存取封裝包含該資源，或直接將它們指派給存取套件，您可以指派存取套件。 如需詳細資訊，請參閱 <<c0> [ 編輯和管理現有的存取封裝](entitlement-management-access-package-edit.md)。

## <a name="checklist-for-request-issues"></a>要求問題的檢查清單

* 當使用者想要要求存取套件的存取權時，是確定它們使用**我存取入口網站連結**存取封裝。 如需詳細資訊，請參閱 <<c0> [ 複製我存取入口網站連結](entitlement-management-access-package-edit.md#copy-my-access-portal-link)。

* 當使用者登入時我存取入口網站來要求存取套件時，請確定他們使用其組織帳戶進行驗證。 組織帳戶可以是任一個帳戶在資源目錄中，或包含在其中存取封裝原則的目錄。 如果使用者的帳戶不是組織帳戶，或在原則中不包含該目錄，使用者將不會看到存取套件。 如需詳細資訊，請參閱 <<c0> [ 要求存取套件的存取權](entitlement-management-request-access.md)。

* 如果封鎖的使用者登入的資源目錄，它們不能要求我存取入口網站中的存取。 使用者可以要求存取權之前，您必須從使用者的設定檔中移除的登入區塊。 若要移除的登入區塊中，在 Azure 入口網站中，按一下**Azure Active Directory**，按一下**使用者**，按一下 [使用者]，然後按一下**的設定檔**。 編輯**設定**區段，然後變更**封鎖登入**來**No**。 如需詳細資訊，請參閱[新增或更新使用 Azure Active Directory 使用者的設定檔資訊](../fundamentals/active-directory-users-profile-azure-portal.md)

* 在我存取入口網站中，如果使用者是要求者和核准者，它們不會看到他們存取套件的要求上**核准**頁面。 此行為是刻意設計-使用者無法核准自己的要求。 請確認已設定原則的其他核准者要求存取套件。 如需詳細資訊，請參閱 <<c0> [ 編輯現有原則](entitlement-management-access-package-edit.md#edit-an-existing-policy)。

* 如果新的外部使用者，會有先前未登入您的目錄，收到存取套件，包括 SharePoint Online 網站，其存取套件會顯示為在 SharePoint Online 中佈建其帳戶之前，未完整傳遞。

## <a name="next-steps"></a>後續步驟

- [檢視報表的使用者獲得存取權限管理 中的方式](entitlement-management-reports.md)
