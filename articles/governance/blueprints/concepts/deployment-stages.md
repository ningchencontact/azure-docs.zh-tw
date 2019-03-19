---
title: Blueprint （藍圖） 部署的階段
description: 了解藍圖服務部署期間經歷的步驟。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: a3ab5589cd327b73f2e66540da5c49343c4449cd
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2019
ms.locfileid: "57997622"
---
# <a name="stages-of-a-blueprint-deployment"></a>Blueprint （藍圖） 部署的階段

取得部署藍圖，一系列的動作是服務所花費 Azure 藍圖部署的藍圖中定義的資源。 這篇文章提供有關每個步驟所涉及的詳細資料。

Blueprint （藍圖） 部署就會觸發將藍圖指派給訂用帳戶或[更新現有的指派](../how-to/update-existing-assignments.md)。 在部署期間，藍圖，請採用下列高階步驟：

> [!div class="checklist"]
> - 授與擁有者權限的藍圖
> - 建立藍圖指派物件
> - 藍圖 （選擇性） 建立**系統指派**受控身分識別
> - 受管理的身分識別會部署藍圖成品
> - 藍圖服務及**系統指派**受控身分識別的權限都已被撤銷

## <a name="blueprints-granted-owner-rights"></a>授與擁有者權限的藍圖

若要指派訂用帳戶或訂用帳戶的擁有者權限會授與 Azure 藍圖服務主體。 授與的角色允許建立，並稍後撤銷，藍圖[系統指派給受控身分識別](../../../active-directory/managed-identities-azure-resources/overview.md)。

權限會授與自動指派透過入口網站。 不過，如果作業透過 REST API 中，授與權限必須完成使用個別的 API 呼叫。 Azure blueprint （藍圖) AppId 是`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`，但依租用戶的服務主體而異。 使用[Azure Active Directory 圖形 API](../../../active-directory/develop/active-directory-graph-api.md)和 REST 端點[servicePrincipals](/graph/api/resources/serviceprincipal)取得服務主體。 然後，授與 Azure 藍圖_擁有者_透過角色[入口網站](../../../role-based-access-control/role-assignments-portal.md)， [Azure CLI](../../../role-based-access-control/role-assignments-cli.md)， [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md)， [RESTAPI](../../../role-based-access-control/role-assignments-rest.md)，或有[Resource Manager 範本](../../../role-based-access-control/role-assignments-template.md)。

藍圖服務不會直接部署資源。

## <a name="the-blueprint-assignment-object-is-created"></a>建立藍圖指派物件

使用者、 群組或服務主體將藍圖指派訂用帳戶。 指派物件存在於其中 blueprint （藍圖） 所指派的訂用帳戶層級。 部署作業所建立的資源不是部署的實體的內容中完成。

建立藍圖指派的型別時[受控身分識別](../../../active-directory/managed-identities-azure-resources/overview.md)已選取。 預設值是**系統指派**受控身分識別。 A**指派使用者給**可以選擇受管理的身分識別。 使用時**指派使用者給**受控身分識別，必須定義和建立藍圖指派之前，請授與權限。

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>（選擇性） 藍圖，會建立 「 系統指派給受控身分識別

當[系統指派給受控身分識別](../../../active-directory/managed-identities-azure-resources/overview.md)已選取 在指派期間藍圖建立身分識別，並授與受管理的身分識別[擁有者](../../../role-based-access-control/built-in-roles.md#owner)角色。 如果[升級現有的指派](../how-to/update-existing-assignments.md)，藍圖會使用先前建立的受管理身分識別。

相關藍圖指派受管理的身分識別用來部署或重新部署藍圖中定義的資源。 此設計可避免不小心干擾其他的指派。
此設計也支援[資源鎖定](./resource-locking.md)藉由控制 blueprint （藍圖） 從每個已部署資源的安全性功能。

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>受管理的身分識別會部署藍圖成品

受管理的身分識別接著觸發程序中定義的藍圖內的成品的 Resource Manager 部署[排序順序](./sequencing-order.md)。 若要確保以正確的順序部署相依於其他成品的成品，您可以調整順序。

部署的存取權失敗通常是存取的受管理的身分識別授與層級的結果。 藍圖服務所管理的安全性生命週期**系統指派**受控身分識別。 不過，使用者也負責管理的權限和生命週期**指派使用者給**受控身分識別。

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Blueprint （藍圖） 服務和系統指派給受控身分識別的權限都已被撤銷

一旦部署完成後，藍圖撤銷的權限**系統指派**受管理的訂用帳戶的識別。 然後，藍圖服務撤銷其權限的訂用帳戶。 權限移除可防止藍圖變成永久的擁有者的訂用帳戶。

## <a name="next-steps"></a>後續步驟

- 了解如何使用[靜態和動態參數](parameters.md)。
- 了解如何自訂[藍圖排序順序](sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](resource-locking.md)。
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)。
- 解決問題的藍圖，以使用在指派期間[一般疑難排解](../troubleshoot/general.md)。