---
title: 在 Azure AD 權限管理 （預覽）-Azure Active Directory 中的委派工作
description: 深入了解您可以指派給委派 Azure Active Directory 權限管理工作的角色。
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
ms.openlocfilehash: d4c4933847a39a56084894e5bbd40e166e6b73b6
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798643"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>委派工作，在 Azure AD 權限管理 （預覽）

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

根據預設，全域管理員和使用者管理員可以建立和管理 Azure AD 權限管理的各個層面。 不過，這些角色中的使用者可能不會知道其中存取套件所需的所有案例。 它通常是在部門內的使用者知道誰需要共同作業。

而不是授與不受限制的權限給非系統管理員，您可以授與使用者的最低權限，他們需要執行其工作並避免建立衝突或不適當的存取權限。 此文章說明您可以指派給委派權限管理 中的各種工作的角色。

## <a name="delegate-example-for-departmental-adoption"></a>系所採用的委派範例

若要了解您如何委派權限管理 中的工作，最好先看一個範例。 假設您的組織有下列五位使用者：

| 使用者 | department | 注意 |
| --- | --- | --- |
| Alice | IT | 全域管理員 |
| Bob | 研究 | Bob 也會參考資料群組的擁有者 |
| Carole | 研究 |  |
| Dave | 行銷 |  |
| Elisa | 行銷 | Elisa 也是行銷應用程式擁有者 |

參考資料和行銷部門想要使用為其使用者的權限管理。 Alice 尚無法供其他部門使用權利管理。 以下是一種方式，Alice 無法將工作委派給研究和行銷部門。

1. Alice 會建立新的 Azure AD 安全性類別目錄的建立者，群組，並將 Bob，Carol，大衛，Elisa 新增為該群組的成員。

1. Alice 會將該群組新增至 「 類別目錄的建立者角色使用的權限管理設定。

1. Carol 會建立**研究**目錄，並將 Bob 新增為該目錄的共同擁有者。 讓它可用於存取封裝中研究共同作業，Bob 將他所擁有的目錄做為資源，參考資料群組加入。

1. Dave 會建立**行銷**目錄，並將 Elisa 新增為該目錄的共同擁有者。 Elisa 新增她擁有到類別目錄做為資源，行銷應用程式，以便它可以用於存取封裝中行銷共同作業。

現在的研究和行銷部門可以利用權限管理。 Bob，Carol，大衛，Elisa 可以建立及管理其各自的類別目錄中存取套件。

![權利管理委派的範例](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>權利管理角色

權利管理具有下列權限管理專用的角色。

| Role | 描述 |
| --- | --- |
| 類別目錄的建立者 | 建立及管理目錄。 通常是 IT 系統管理員不是全域管理員或資源集合的資源擁有者。 會自動建立目錄的人員會成為類別目錄的第一個類別目錄擁有者，而且可以加入額外的類別目錄的擁有者。 |
| 目錄擁有者 | 編輯及管理現有的目錄。 通常是 IT 系統管理員或資源擁有者或指定目錄的擁有者的使用者。 |
| 存取封裝管理員 | 編輯和管理目錄內的所有現有存取套件。 |

此外，指定的核准者及存取封裝的要求者也有權限，雖然它們不是角色。
 
* 核准者：授權原則來核准或拒絕存取套件的要求，但無法變更存取套件定義。
* 要求者：獲授權存取封裝的原則來要求該存取套件。

下表列出這些角色可以執行的工作。

| 工作 | 類別目錄的建立者 | 目錄擁有者 | 存取封裝管理員 | 核准者 |
| --- | :---: | :---: | :---: | :---: |
| [建立新的目錄](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [將資源新增到目錄](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [編輯類別目錄](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [刪除類別目錄](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [將類別目錄的擁有者或存取封裝管理員新增至目錄](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [在目錄中建立新的存取封裝](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [管理存取套件中的資源角色](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [指定誰可以要求存取套件](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [直接將使用者指派給存取套件](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [檢視指派給存取套件的人員](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [檢視存取套件的要求](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [檢視要求的傳遞錯誤](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [取消暫止的要求](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [隱藏存取套件](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [刪除存取封裝](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [核准存取要求](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>必要的角色，將資源新增至目錄

全域管理員可以新增或移除的目錄中的任何群組 （雲端建立的安全性群組或雲端建立 Office 365 群組）、 應用程式或 SharePoint Online 網站。 使用者管理員可以新增或移除的目錄中的任何群組或應用程式。

使用者不是全域管理員或使用者系統管理員群組、 應用程式或 SharePoint Online 網站中加入一個類別目錄，該使用者必須擁有*兩者*需要 Azure AD 目錄角色及類別目錄的擁有者權限管理角色。 下表列出將資源新增至目錄時所需的角色組合。 若要移除目錄中的資源，您必須使用相同的角色。

| Azure AD 目錄角色 | 權利管理角色 | 可以新增安全性群組 | 可以新增 Office 365 群組 | 可以新增應用程式 | 可以新增 SharePoint Online 網站 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [全域管理員](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [使用者管理員](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  |  |
| [小組服務管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  |  |
| [SharePoint 系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [應用程式系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  |  | :heavy_check_mark: |  |
| [雲端應用程式系統管理員](../users-groups-roles/directory-assign-admin-roles.md) | 目錄擁有者 |  |  | :heavy_check_mark: |  |
| 使用者 | 目錄擁有者 | 只有當群組擁有者 | 只有當群組擁有者 | 只有當應用程式擁有者 |  |

## <a name="add-a-catalog-creator"></a>新增類別目錄的建立者

如果您想要委派建立類別目錄，您會將使用者新增至 「 類別目錄的建立者角色中。  您可以新增個別使用者，或是為了方便起見，可以加入的群組時，其成員就能夠建立目錄。 請遵循下列步驟來將使用者指派給 「 類別目錄的建立者角色。

**必要角色：** 全域管理員或使用者管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，在**權利管理**區段中，按一下**設定**。

1. 按一下 [編輯]  。

1. 在**權利管理委派**區段中，按一下**加入目錄的建立者**選取使用者或群組將成為此權利管理角色的成員。

1. 按一下 [選取]  。

1. 按一下 [儲存]  。

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>新增目錄擁有者或存取套件管理員

如果您想要委派管理目錄中的類別目錄或存取套件，您可以新增使用者目錄的擁有者或存取封裝管理員 」 角色。 人會建立目錄，就會變成第一個類別目錄擁有者。 請遵循下列步驟來將使用者指派給目錄的擁有者或存取套件管理員角色。

**必要角色：** 使用者管理員或目錄的擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下**目錄**，然後開啟您想要新增系統管理員的目錄。

1. 在左側功能表中，按一下**角色和系統管理員**。

1. 按一下 **新增擁有者**或是**加入存取封裝管理員**選取這些角色的成員。

1. 按一下 **選取**以便將這些成員。

## <a name="next-steps"></a>後續步驟

- [新增核准者](entitlement-management-access-package-edit.md#policy-request)
- [將資源新增至目錄](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
