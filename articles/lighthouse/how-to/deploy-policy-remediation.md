---
title: 部署可補救的原則
description: 了解如何讓客戶在 Azure 委派的資源管理中上線，讓其資源可透過您自己的租用戶來管理。
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 10/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 3bc85d202b9ba230130716aad34ce233037a3346
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301954"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>部署可在委派的訂用帳戶內補救的原則

Azure Lighthouse 可讓服務提供者在委派的訂用帳戶內建立和編輯原則定義。 不過，若要部署使用[補救工作](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)的原則 (也就是，具有 [deployIfNotExists](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deployifnotexists) 或 [modify](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify) 效果的原則)，您必須在客戶租用戶中建立[受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。 Azure 原則可以使用此受控識別在原則內部署範本。 當您讓客戶上線進行 Azure 委派資源管理，以及當您部署原則本身時，必須執行下列步驟才能啟用此案例。

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>建立可將角色指派給客戶租用戶中受控識別的使用者

當您讓客戶上線進行 Azure 委派資源管理時，您會使用 [Azure Resource Manager 範本](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer#create-an-azure-resource-manager-template)以及 parameters 檔案，該檔案會定義您管理租用戶中將能夠存取客戶租用戶中委派資源的使用者、使用者群組和服務主體。 在您的 parameters 檔案中，每個使用者 (**principalId** ) 都會被指派一個[內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (**roleDefinitionId** )，以定義存取層級。

若要允許 **principalId** 在客戶租用戶中建立受控識別，您必須將其 **roleDefinitionId** 設定為 [使用者存取管理員]  。 雖然此角色通常不受支援，但可在此特定案例中使用，可讓具有此權限的使用者將一個或多個特定內建角色指派給受控識別。 這些角色都定義於 **delegatedRoleDefinitionIds** 屬性中。 除了「使用者存取管理員」或「擁有者」以外，您可以在此包含任何內建角色。

客戶上線之後，在此授權中建立的 **principalId** 就能夠將這些內建角色指派給客戶租用戶中的受控識別。 不過，他們不會有任何通常與「使用者存取管理員」角色相關聯的其他權限。

以下範例顯示將具有「使用者存取管理員」角色的 **principalId**。 這位使用者能夠將兩個內建角色指派給客戶租用戶中的受控識別：參與者和記錄分析參與者。

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>部署可補救的原則

當您如上所述建立具有必要權限的使用者之後，該使用者就可以在客戶租用戶中部署使用補救工作的原則。

例如，假設您想要在客戶租用戶中的 Azure Key Vault 資源上啟用診斷功能，如此[範例](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring)所說明。 管理租用戶中具有適當權限的使用者 (如上所述) 會部署 [Azure Resource Manager 範本](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json)以啟用此案例。

請注意，目前必須透過 API (而不是在 Azure 入口網站中) 建立要與委派訂用帳戶搭配使用的原則指派。 當您這麼做時，**apiVersion**  必須設定為 **2019-04-01-preview**，其中包含新的 **delegatedManagedIdentityResourceId** 屬性。 此屬性可讓您包含位於客戶租用戶的受控識別 (在已上線進行 Azure 委派資源管理的訂用帳戶或資源群組中)。

下列範例顯示具有 **delegatedManagedIdentityResourceId** 的角色指派。

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> 有個[類似範例](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag)可用來示範如何部署原則，以在委派的訂用帳戶中新增或移除標籤 (使用修改效果)。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 原則](https://docs.microsoft.com/azure/governance/policy/)。
- 了解[適用於 Azure 資源的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
