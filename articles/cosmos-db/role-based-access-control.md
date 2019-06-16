---
title: Azure Cosmos DB 中的角色型存取控制，與 Azure Active Directory 整合
description: 了解 Azure Cosmos DB 如何提供與 Active directory 整合 (RBAC) 的資料庫保護。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 971d2ec96906a3309963495dd1af5d293a71f265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243507"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB 中的角色型存取控制

Azure Cosmos DB 會提供 Azure Cosmos DB 中的一般管理案例中的內建的角色型存取控制 (RBAC)。 Azure Active Directory 中有設定檔的個人可以將這些 RBAC 角色指派給使用者、 群組、 服務主體，或受管理身分識別授與或拒絕存取資源和 Azure Cosmos DB 資源執行作業。 角色指派的範圍為控制平面的存取，其中包括存取 Azure Cosmos 帳戶、 資料庫、 容器，並提供 （輸送量）。

## <a name="built-in-roles"></a>內建角色

以下是 Azure Cosmos DB 所支援的內建角色：

|**內建角色**  |**說明**  |
|---------|---------|
|[DocumentDB 帳戶參與者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | 可以管理 Azure Cosmos DB 帳戶。  |
|[Cosmos DB 帳戶讀者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | 可以讀取 Azure Cosmos DB 帳戶資料。        |
|[Cosmos 備份操作員](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  可以提交還原要求的 Azure Cosmos 資料庫或容器。       |
|[Cosmos DB 運算子](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Azure Cosmos 帳戶、 資料庫和容器可以佈建，但無法存取，才能存取資料的索引鍵。         |

> [!IMPORTANT]
> Azure Cosmos DB 中的 RBAC 支援適用於只控制平面作業。 資料平面作業會使用主要金鑰或資源權杖來保護。 若要進一步了解，請參閱[安全存取 Azure Cosmos DB 中的資料](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>身分識別與存取權管理 (IAM)

**存取控制 (IAM)** 在 Azure 入口網站中的窗格會用來設定 Azure Cosmos 資源的角色型存取控制。 角色會套用至使用者、 群組、 服務主體和 Active Directory 中的受管理身分識別。 您可以使用內建角色或自訂角色的個人和群組。 下列螢幕擷取畫面顯示 Azure 入口網站中使用存取控制 (IAM) 的 Active Directory 整合 (RBAC):

![Azure 入口網站中的存取控制 (IAM) - 示範資料庫安全性](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>自訂角色

除了內建的角色，使用者也可以建立[自訂角色](../role-based-access-control/custom-roles.md)在 Azure 中並將這些角色套用至服務主體，其 Active Directory 租用戶內的所有訂用帳戶。 自訂角色可用來建立具有一組自訂的資源提供者作業的 RBAC 角色定義的使用者。 若要了解哪些作業可供建置自訂的角色，如 Azure Cosmos DB，請參閱[Azure Cosmos DB 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 資源的角色型存取控制 (RBAC)](../role-based-access-control/overview.md)
- [適用於 Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
