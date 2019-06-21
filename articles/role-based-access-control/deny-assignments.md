---
title: 了解 Azure 資源的拒絕指派 | Microsoft Docs
description: 了解 Azure 資源之角色型存取控制 (RBAC) 中的拒絕指派。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: f15d6fd81337aa4a859539e86f37a516848c9370
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165991"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>了解 Azure 資源的拒絕指派

與角色指派相同，「拒絕指派」  也會基於拒絕存取權的目的來附加一組在特定範圍內拒絕使用者、群組或服務主體的動作。 拒絕指派會封鎖使用者執行指定的 Azure 資源動作，即使角色指派授予他們存取權也一樣。

此文章說明如何定義拒絕指派。

## <a name="how-deny-assignments-are-created"></a>如何拒絕建立指派

拒絕建立和受保護資源的 Azure 指派。 例如，Azure 藍圖和 Azure 受管理應用程式使用拒絕的指派，以保護系統所管理的資源。 如需詳細資訊，請參閱[使用 Azure 藍圖資源鎖定保護新資源](../governance/blueprints/tutorials/protect-new-resources.md)。

## <a name="compare-role-assignments-and-deny-assignments"></a>比較角色指派，並拒絕指派

拒絕指派角色指派，以遵循類似的模式，但也有一些差異。

| 功能 | 角色指派 | 拒絕指派 |
| --- | --- | --- |
| 授與存取權 | :heavy_check_mark: |  |
| 拒絕存取 |  | :heavy_check_mark: |
| 您可以直接建立 | :heavy_check_mark: |  |
| 套用在範圍內 | :heavy_check_mark: | :heavy_check_mark: |
| 排除原則 |  | :heavy_check_mark: |
| 防止子範圍的繼承 |  | :heavy_check_mark: |
| 適用於[傳統訂用帳戶管理員](rbac-and-directory-admin-roles.md)指派 |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>拒絕指派屬性

 拒絕指派有下列屬性：

> [!div class="mx-tableFixed"]
> | 屬性 | 必要項 | 類型 | 描述 |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | 是 | 字串 | 拒絕指派的顯示名稱。 名稱在指定範圍內必須是唯一的。 |
> | `Description` | 否 | 字串 | 拒絕指派的描述。 |
> | `Permissions.Actions` | 至少一個 Actions 或一個 DataActions | String[] | 一個字串陣列，指定拒絕指派要封鎖存取權的管理作業。 |
> | `Permissions.NotActions` | 否 | String[] | 一個字串陣列，指定要從拒絕指派排除的管理作業。 |
> | `Permissions.DataActions` | 至少一個 Actions 或一個 DataActions | String[] | 一個字串陣列，指定拒絕指派要封鎖存取權的資料作業。 |
> | `Permissions.NotDataActions` | 否 | String[] | 一個字串陣列，指定要從拒絕指派排除的資料作業。 |
> | `Scope` | 否 | 字串 | 一個字串， 指定拒絕指派要套用的範圍。 |
> | `DoNotApplyToChildScopes` | 否 | Boolean | 指定拒絕指派是否要套用到子範圍。 預設值為 false。 |
> | `Principals[i].Id` | 是 | String[] | 要套用拒絕指派的 Azure AD 主體物件識別碼 (使用者、群組、服務主體或受控識別) 陣列。 設定為空 GUID `00000000-0000-0000-0000-000000000000` 以代表所有主體。 |
> | `Principals[i].Type` | 否 | String[] | 由 Principals[i].Id 代表的物件類型陣列。設定為 `SystemDefined` 以代表所有主體。 |
> | `ExcludePrincipals[i].Id` | 否 | String[] | 不套用拒絕指派的 Azure AD 主體物件識別碼 (使用者、群組、服務主體或受控識別) 陣列。 |
> | `ExcludePrincipals[i].Type` | 否 | String[] | 由 ExcludePrincipals[i].Id 代表的物件類型陣列。 |
> | `IsSystemProtected` | 否 | Boolean | 指定此拒絕指派是否由 Azure 建立且無法編輯或刪除。 目前，所有拒絕指派都受系統保護。 |

## <a name="the-all-principals-principal"></a>所有主體的主體

若要支援拒絕指派，名為系統定莪主體*所有主體*已經導入。 此主體代表 Azure AD 目錄中的所有使用者、群組、服務主體和受控識別。 若主體識別碼是零值 GUID `00000000-0000-0000-0000-000000000000` 且主體類型是 `SystemDefined`，則主體代表所有主體。 在 Azure PowerShell 輸出中，所有主體看都起來如下所示：

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

所有的主體可以結合`ExcludePrincipals`拒絕某些使用者以外，所有主體。 所有主體都有下列限制：

- 只能在 `Principals` 中使用，而無法在 `ExcludePrincipals` 中使用。
- `Principals[i].Type` 必須設為 `SystemDefined`。

## <a name="next-steps"></a>後續步驟

* [清單會拒絕使用 Azure 入口網站的 Azure 資源的指派](deny-assignments-portal.md)
* [了解 Azure 資源的角色定義](role-definitions.md)
