---
title: 委派 Azure AD 權利管理 (預覽) 中的工作-Azure Active Directory
description: 瞭解您可以指派的角色, 以在 Azure Active Directory 權利管理中委派工作。
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
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b631f078240821e79513c4bd944a33b4725bc52
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207147"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>委派 Azure AD 權利管理 (預覽) 中的工作

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

根據預設, 全域系統管理員和使用者系統管理員可以建立及管理 Azure AD 權利管理的所有層面。 不過, 這些角色中的使用者可能不知道需要存取套件的所有案例。 這通常是在部門中, 使用者知道需要共同作業的人員。 

您可以授與使用者執行其工作所需的最低許可權, 並避免建立衝突或不適當的存取權限, 而不是將不受限制的許可權授予非系統管理員。 本文說明您可以指派的角色, 以委派權利管理中的各種工作。 

## <a name="delegate-example-for-departmental-adoption"></a>委派部門採用範例

若要瞭解如何在「權利管理」中委派工作, 它有助於考慮一個範例。 

假設您的組織有下列五個使用者:

| 使用者 | 部門 | 注意 |
| --- | --- | --- |
| Alice | IT | 全域管理員 |
| Bob | 研究 | Bob 也是 Research 群組的擁有者 |
| Carole | 研究 |  |
| Dave | 行銷 |  |
| Elisa | 行銷 | Elisa 也是行銷應用程式的擁有者 |

研究和行銷部門都想要為其使用者使用權利管理。 Alice 尚未準備好讓其他部門使用權利管理。 以下是 Alice 可以將工作委派給研究和行銷部門的一種方式。

1. Alice 會為目錄建立者建立新的 Azure AD 安全性群組, 然後新增 Bob、Carol、Dave 和 Elisa 做為該群組的成員。

1. Alice 使用權利管理設定, 將該群組新增至目錄建立者角色。

1. Carol 會建立**研究**目錄, 並將 Bob 新增為該目錄的共同擁有者。 Bob 將他擁有的 research 群組新增至目錄做為資源, 以便在研究共同作業的存取套件中使用。

1. Dave 會建立**行銷**目錄, 並新增 Elisa 作為該目錄的共同擁有者。 Elisa 會將她擁有的行銷應用程式新增至目錄做為資源, 以便用於行銷共同作業的存取封裝中。

現在, 研究和行銷部門可以利用權利管理。 Bob、Carol、Dave 和 Elisa 可以在其各自的目錄中建立和管理存取封裝。

![權利管理委派範例](./media/entitlement-management-delegate/elm-delegate.png)


## <a name="entitlement-management-roles"></a>權利管理角色

權利管理具有下列專屬於權利管理的角色。

| Role | 描述 |
| --- | --- |
| 目錄建立者 | 建立和管理目錄。 通常是不是全域管理員的 IT 系統管理員, 或是資源集合的資源擁有者。 建立類別目錄的人員會自動成為目錄的第一個目錄擁有者, 而且可以新增其他目錄擁有者。 |
| 目錄擁有者 | 編輯和管理現有的目錄。 通常是 IT 系統管理員或資源擁有者, 或是目錄擁有者已指定的使用者。 |
| 存取套件管理員 | 編輯和管理目錄中的所有現有存取封裝。 |

此外, 指定的核准者和存取套件的要求者也具有許可權, 但它們並不是角色。
 
* 人員由原則授權以核准或拒絕存取封裝的要求, 但無法變更存取套件定義。
* 申請者由存取封裝的原則授權, 以要求該存取封裝。

下表列出這些角色可以執行的工作。

| 工作 | 目錄建立者 | 目錄擁有者 | 存取套件管理員 | 核准者 |
| --- | :---: | :---: | :---: | :---: |
| [建立新的目錄](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [將資源新增至目錄](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [編輯目錄](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [刪除目錄](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [將目錄擁有者或存取套件管理員新增至目錄](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [在目錄中建立新的存取套件](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [管理存取套件中的資源角色](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [建立和編輯原則](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [直接將使用者指派給存取套件](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [查看誰具有存取套件的指派](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [查看存取套件的要求](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [查看要求的傳遞錯誤](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [解除擱置中的要求](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [隱藏存取套件](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [刪除存取套件](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [核准存取要求](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>將資源新增至目錄的必要角色

全域管理員可以在目錄中新增或移除任何群組 (已建立雲端的安全性群組或雲端建立的 Office 365 群組)、應用程式或 SharePoint Online 網站。 使用者系統管理員可以新增或移除目錄中的任何群組或應用程式。

對於不是全域管理員或使用者系統管理員的使用者, 若要將群組、應用程式或 SharePoint Online 網站新增至目錄, 該使用者必須*同時*擁有必要的 Azure AD 目錄角色和目錄擁有者權利管理角色。 下表列出將資源新增至目錄所需的角色組合。 若要移除目錄中的資源, 您必須擁有相同的角色。

| Azure AD 目錄角色 | 權利管理角色 | 可以新增安全性群組 | 可以新增 Office 365 群組 | 可以新增應用程式 | 可以新增 SharePoint Online 網站 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [全域管理員](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [使用者系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  |  |
| [Microsoft Teams 服務管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  |  |
| [SharePoint 管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [應用程式系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  |  | :heavy_check_mark: |  |
| [雲端應用程式系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  |  | :heavy_check_mark: |  |
| 使用者 | 目錄擁有者 | 只有在群組擁有者 | 只有在群組擁有者 | 只有應用程式擁有者 |  |

若要判斷工作的最低特殊許可權角色, 您也可以[在 Azure Active Directory 中, 依管理工作參考系統管理員角色](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)。

## <a name="add-a-catalog-creator"></a>新增目錄建立者

如果您想要委派建立目錄, 請將使用者新增至目錄建立者角色。  您可以新增個別使用者, 或為了方便加入群組, 其成員接著可以建立目錄。 請遵循下列步驟, 將使用者指派給目錄建立者角色。

**必要角色：** 全域管理員或使用者管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表的 [**權利管理**] 區段中, 按一下 [**設定**]。

1. 按一下 [編輯]。

1. 在 [**委派權利管理**] 區段中, 按一下 [**新增類別目錄建立者**], 選取將成為此權利管理角色成員的使用者或群組。

1. 按一下 [選取]。

1. 按一下 [儲存]。

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>新增類別目錄擁有者或存取套件管理員

如果您想要委派目錄的管理或存取目錄中的封裝, 您可以將使用者新增至目錄擁有者或存取套件管理員角色。 建立目錄的人員會成為第一個目錄擁有者。 請遵循下列步驟, 將使用者指派給目錄擁有者或存取套件管理員角色。

**必要角色：** 全域管理員、使用者系統管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**目錄**], 然後開啟您想要新增系統管理員的目錄。

1. 在左側功能表中, 按一下 [**角色和系統管理員**]。

1. 按一下 [**新增擁有**者] 或 [**新增存取套件管理員**], 以選取這些角色的成員。

1. 按一下 [**選取**] 以加入這些成員。

## <a name="next-steps"></a>後續步驟

- [新增核准者](entitlement-management-access-package-edit.md#policy-request)
- [將資源新增至目錄](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
