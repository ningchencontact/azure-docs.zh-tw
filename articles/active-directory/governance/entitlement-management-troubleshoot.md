---
title: 針對 Azure AD 權利管理 (預覽) 進行疑難排解-Azure Active Directory
description: 瞭解您應該檢查的一些專案, 以協助您針對 Azure Active Directory 權利管理 (預覽) 進行疑難排解。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 420a7079a7961868277a2d78ffbac4adba240d9f
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678062"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>疑難排解 Azure AD 權利管理 (預覽)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文說明您應該檢查的一些專案, 以協助您進行 Azure Active Directory (Azure AD) 權利管理的疑難排解。

## <a name="checklist-for-entitlement-management-administration"></a>權利管理管理的檢查清單

* 如果您在設定權利管理時收到拒絕存取的訊息, 而且您是全域管理員, 請確定您的目錄具有[Azure AD Premium P2 (或 EMS E5) 授權](entitlement-management-overview.md#license-requirements)。  
* 如果您在建立或查看存取套件時收到拒絕存取的訊息, 而且您是目錄建立者群組的成員, 則必須在建立第一個存取套件之前先建立目錄。

## <a name="checklist-for-adding-a-resource"></a>新增資源的檢查清單

* 若要讓應用程式成為存取套件中的資源, 它必須至少有一個可以指派的資源角色。 角色是由應用程式本身定義, 並在 Azure AD 中進行管理。 請注意, Azure 入口網站可能也會顯示無法選取為應用程式之服務的服務主體。  特別是, **Exchange online**和**SharePoint online**是服務, 而不是在目錄中具有資源角色的應用程式, 因此不能包含在存取套件中。  相反地, 請使用以群組為基礎的授權, 為需要存取這些服務的使用者建立適當的授權。

* 若要讓群組成為存取套件中的資源, 必須能夠在 Azure AD 中進行修改。  源自內部部署 Active Directory 的群組無法指派為資源, 因為無法在 Azure AD 中變更其擁有者或成員屬性。   源自 Exchange Online 的群組, 因為通訊群組無法在 Azure AD 中修改。 

* SharePoint Online 文件庫和個別檔無法新增為資源。  相反地, 請建立 Azure AD 安全性群組、在存取套件中包含該群組和網站角色, 然後在 SharePoint Online 中使用該群組來控制文件庫或檔的存取權。

* 如果有已指派給您要使用存取套件管理之資源的使用者, 請確定已將使用者指派給具有適當原則的存取套件。 例如, 您可能想要在存取套件中包含群組中已經有使用者的群組。 如果群組中的使用者需要繼續存取, 他們必須擁有存取套件的適當原則, 才能讓他們無法存取群組。 您可以要求使用者要求包含該資源的存取套件, 或直接將它們指派給存取套件, 藉此指派存取封裝。 如需詳細資訊, 請參閱[編輯和管理現有的存取封裝](entitlement-management-access-package-edit.md)。

## <a name="checklist-for-providing-external-users-access"></a>提供外部使用者存取的檢查清單

* 如果有 B2B[允許清單](../b2b/allow-deny-list.md), 則不允許其目錄的使用者將無法要求存取權。

* 請確定沒有條件式[存取原則](../conditional-access/require-managed-devices.md)可防止外部使用者要求存取權, 或無法在存取套件中使用應用程式。

## <a name="checklist-for-request-issues"></a>要求問題的檢查清單

* 當使用者想要要求存取套件的存取權時, 請確定他們使用的是存取套件的**我的存取權入口網站連結**。 如需詳細資訊, 請參閱[複製我的存取權入口網站連結](entitlement-management-access-package-edit.md#copy-my-access-portal-link)。  如果外部使用者造訪**myaccess.microsoft.com**, 他們會在自己的組織中看到可供他們使用的存取套件。

* 當不在您目錄中的使用者登入我的存取權入口網站以要求存取套件時, 請務必使用其組織帳戶進行驗證。 組織帳戶可以是資原始目錄中的帳戶, 或包含在存取封裝其中一個原則中的目錄。 如果使用者的帳戶不是組織帳戶, 或其驗證所在的目錄未包含在原則中, 則使用者將看不到存取套件。 如需詳細資訊, 請參閱[要求存取封裝的存取權](entitlement-management-request-access.md)。

* 如果使用者遭到封鎖而無法登入資原始目錄, 他們將無法在我的存取權入口網站中要求存取權。 在使用者可以要求存取之前, 您必須先從使用者的設定檔中移除登入區塊。 若要移除登入區塊, 請在 Azure 入口網站中, 按一下  **Azure Active Directory**, 按一下 **使用者**, 按一下使用者, 然後按一下 **設定檔**。 編輯 [**設定**] 區段, 並將 [**封鎖登入**] 變更為 [**否**]。 如需詳細資訊, 請參閱[使用 Azure Active Directory 新增或更新使用者的設定檔資訊](../fundamentals/active-directory-users-profile-azure-portal.md)。  您也可以檢查使用者是否因身分[識別保護原則](../identity-protection/howto-unblock-user.md)而遭到封鎖。

* 在我的存取權入口網站中, 如果使用者同時是要求者和核准者, 他們將不會在 [**核准**] 頁面上看到其存取套件的要求。 這是刻意的行為, 使用者無法核准自己的要求。 確定他們要求的存取套件已在原則上設定其他核准者。 如需詳細資訊, 請參閱[編輯現有的原則](entitlement-management-access-package-edit.md#edit-an-existing-policy)。

* 如果新的外部使用者先前尚未登入您的目錄, 則會收到包含 SharePoint Online 網站的存取套件, 其存取套件會顯示為未完全傳遞, 直到其帳戶布建于 SharePoint Online 為止。

## <a name="next-steps"></a>後續步驟

- [查看使用者在權利管理中取得存取權的報告](entitlement-management-reports.md)
