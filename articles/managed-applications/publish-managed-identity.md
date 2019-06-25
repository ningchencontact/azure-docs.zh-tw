---
title: Azure 受管理的應用程式管理的身分識別
description: 了解如何設定受管理的應用程式與受控身分識別。 受控身分識別可用來部署受控應用程式連結至現有的資源，授與管理 Azure 資源群組外部的受管理的資源，並提供活動記錄的受控應用程式的運作身分識別管理的應用程式和在 Azure 中其他服務。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 5ef653e825a5f1eb0f5df52f9c2544a5224b34cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66003442"
---
# <a name="azure-managed-application-with-managed-identity"></a>Azure 受管理的應用程式管理的身分識別

> [!NOTE]
> 受控應用程式的受管理身分識別支援目前為預覽狀態。 請若要使用受控身分識別，使用 2018年-09-01-預覽 api 版本。

了解如何設定受管理的應用程式包含受管理的身分識別。 受控身分識別可用來讓客戶授與其他現有的資源管理的應用程式存取。 身分識別由 Azure 平台負責管理，因此您不需要佈建或輪替任何密碼。 如需有關受管理的身分識別在 Azure Active Directory (AAD) 的詳細資訊，請參閱[管理 Azure 資源的身分識別](../active-directory/managed-identities-azure-resources/overview.md)。

您的應用程式可以授與兩種類型的身分識別：

- **系統指派的身分識別**會繫結至您的應用程式，如果您的應用程式已刪除，則會被刪除。 應用程式只能有一個系統指派的身分識別。
- A**指派給使用者的身分識別**是獨立的可指派給您的應用程式的 Azure 資源。 應用程式可以有多個使用者指派的身分識別。

## <a name="how-to-use-managed-identity"></a>如何使用受控身分識別

受控身分識別會讓受控應用程式中的許多案例。 可以解決一些常見案例是：

- 部署受控應用程式連結至現有的 Azure 資源。 範例會部署 Azure 虛擬機器 (VM) 附加至 受控應用程式內[現有的網路介面](../virtual-network/virtual-network-network-interface-vm.md)。
- 外部的 Azure 資源的受控應用程式和 「 發行者 」 存取權授與**受控的資源群組**。
- 活動記錄檔和其他服務在 Azure 中的提供受控應用程式的運作身分的識別。

## <a name="adding-managed-identity"></a>新增受控身分識別

使用受控身分識別建立受管理的應用程式需要在 Azure 資源上設定一個額外的屬性。 下列範例顯示範例**識別**屬性：

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

有兩個常見的方式，建立受控應用程式與**識別**:[CreateUIDefinition.json](./create-uidefinition-overview.md)並[Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。 建立簡單的單一案例，CreateUIDefinition 應該用來啟用受管理的身分識別，因為它提供更豐富的體驗。 不過，處理進階或複雜時需要系統自動化，或可以使用多個受管理的應用程式部署範本。

### <a name="using-createuidefinition"></a>使用 CreateUIDefinition

受管理的應用程式可以設定與受控身分識別，透過[CreateUIDefinition.json](./create-uidefinition-overview.md)。 在  [outputs 區段](./create-uidefinition-overview.md#outputs)，金鑰`managedIdentity`可用來覆寫的受管理的應用程式範本的身分識別屬性。 可讓範例鈴**系統指派**上受管理的應用程式的身分識別。 輸入向取用者使用 CreateUIDefinition 元素，可形成更複雜的身分識別物件。 這些輸入可用來建構與管理的應用程式**指派給使用者的身分識別**。

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>使用受管理的身分識別的 CreateUIDefinition 的時機

以下是一些關於何時應該使用 CreateUIDefinition 啟用受控身分識別管理的應用程式的建議。

- 受控應用程式建立會透過 Azure 入口網站或 marketplace。
- 受控身分識別需要複雜的取用者輸入。
- 受控身分識別被需要在建立受控應用程式。

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

基本 CreateUIDefinition 可 SystemAssigned 身分識別管理的應用程式。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

採用基本 CreateUIDefinition**指派給使用者的身分識別**作為輸入，並讓 UserAssigned 身分識別管理的應用程式的資源。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

上述 CreateUIDefinition.json 會產生取用者輸入文字方塊的建立使用者體驗**指派給使用者的身分識別**Azure 資源識別碼。 產生的體驗會像這樣：

![範例使用者指派身分識別 CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本

> [!NOTE]
> Marketplace 的受管理的應用程式範本會自動產生的客戶透過 Azure 入口網站建立體驗。
> 針對這些案例， `managedIdentity` CreateUIDefinition 上的輸出索引鍵必須用來啟用身分識別。

受控身分識別也可以透過 Azure Resource Manager 範本來啟用。 可讓範例鈴**系統指派**上受管理的應用程式的身分識別。 使用 Azure Resource Manager 範本參數提供輸入，可形成更複雜的身分識別物件。 這些輸入可用來建構與管理的應用程式**指派給使用者的身分識別**。

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>受管理的身分識別使用 Azure Resource Manager 範本的時機

以下是一些關於何時應該使用 Azure Resource Manager 範本啟用受控身分識別管理的應用程式的建議。

- 受管理的應用程式可以透過程式設計方式部署以範本為基礎。
- 受控身分識別的自訂角色指派所需佈建受管理的應用程式。
- 受控應用程式不需要 Azure 入口網站和 marketplace 建立流程。

#### <a name="systemassigned-template"></a>SystemAssigned 範本

基本的 Azure Resource Manager 範本，以部署受控應用程式與**系統指派**身分識別。

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned 範本

基本的 Azure Resource Manager 範本，以部署受控應用程式與**指派給使用者的身分識別**。

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>對 Azure 資源的存取權授與

受管理的應用程式授與身分識別之後, 可以被授與現有的 azure 資源的存取權。 此程序可以透過 Azure 入口網站中的存取控制 (IAM) 介面。 受管理的應用程式的名稱或**指派給使用者的身分識別**可搜尋要加入角色指派。

![新增受管理的應用程式角色指派](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>連結現有的 Azure 資源

> [!NOTE]
> A**指派給使用者的身分識別**必須[設定](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)之前部署受控應用程式。 此外，連結的資源部署受控應用程式才支援**marketplace**種類。

受控身分識別也可用來部署受控應用程式，在部署期間需要存取現有的資源。 客戶會佈建受管理的應用程式時**指派給使用者的身分識別**可以提供額外的授權，若要加入**mainTemplate**部署。

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>編寫連結的資源與 CreateUIDefinition

連結現有的資源，這兩個現有的 Azure 資源的受控應用程式部署時，**指派給使用者的身分識別**適用的角色，必須提供該資源上的指派。

 範例需要兩個輸入的 CreateUIDefinition： 網路介面資源識別碼和指派的使用者身分識別的資源識別碼。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

此 CreateUIDefinition.json 會產生有兩個欄位建立使用者經驗。 第一個欄位可讓使用者輸入中的 Azure 資源識別碼可以連結至受管理的應用程式部署的資源。 第二個是要輸入的消費者**指派給使用者的身分識別**已連結的 Azure 資源的存取權的 Azure 資源識別碼。 產生的體驗會像這樣：

![使用兩個的輸入範例 CreateUIDefinition： 資源識別碼和指派的使用者身分識別的資源識別碼的網路介面](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>撰寫 mainTemplate 與連結的資源

除了更新 CreateUIDefinition，主要的範本也需要更新，可接受傳入連結的資源識別碼。 可以更新主要的範本，以加入新的參數以接受新的輸出。 因為`managedIdentity`輸出覆寫上產生的受控應用程式範本的值，不會傳送到主要的範本，並不會包含在參數區段中。

設定現有的網路介面，CreateUIDefinition 提供的網路設定檔範例主要的範本。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>使用受控應用程式，使用連結的資源

受管理的應用程式封裝建立後，可以透過 Azure 入口網站來取用受控應用程式。 可以使用之前，有幾個必要條件步驟。

- 必須建立所需的連結 Azure 資源的執行個體。
- **指派給使用者的身分識別**必須[建立，並指定角色指派](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)連結的資源。
- 現有連結的資源識別碼和**指派給使用者的身分識別**識別碼提供給 CreateUIDefinition。

## <a name="accessing-the-managed-identity-token"></a>存取受管理的身分識別權杖

現在可以透過存取受管理的應用程式的語彙基元`listTokens`從發行者租用戶的 api。 要求範例如下：

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}?api-version=2018-09-01-preview HTTP/1.1
```

範例回應如下：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何設定受管理的應用程式使用自訂提供者](./custom-providers-overview.md)