---
title: 讓客戶在 Azure 委派的資源管理中上線 - Azure Lighthouse
description: 了解如何讓客戶在 Azure 委派的資源管理中上線，讓其資源可透過您自己的租用戶來管理。
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 09/30/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: b2e935a3a5ff2b6da99ad693f2d4e924ae811caf
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694838"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>讓客戶在 Azure 委派的資源管理中上線

此文章說明您 (服務提供者) 如何讓客戶在 Azure 委派的資源管理中上線，讓他們的委派資源 (訂用帳戶和/或資源群組) 可透過您自己的 Azure Active Directory (Azure AD) 租用戶存取及管理。 雖然我們在此所述的是服務提供者與其客戶，但管理多個租用戶的企業也可以使用相同的程序來合併其管理體驗。

如果您在管理多個客戶的資源，您可以重複此程序。 然後，當授權使用者登入您的租用戶時，該使用者可獲授與跨客戶租用戶範圍的權限，以便執行管理作業，而不需要登入每個個別客戶租用戶。

您可以將您的 Microsoft 合作夥伴網路 (MPN) 識別碼與已上線的訂用帳戶建立關聯，以追蹤您對客戶參與的整體影響及接受表彰。 如需詳細資訊，請參閱[將合作夥伴識別碼連結到您的 Azure 帳戶](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) \(部分機器翻譯\)。 請注意，您必須在服務提供者租用戶中執行此關聯。

> [!NOTE]
> 當客戶購買您發佈至 Azure Marketplace 的受控服務供應項目 (公用或私人) 時，系統也可讓他們上線。 如需詳細資訊，請參閱[將受控服務供應項目發佈到 Azure Marketplace](publish-managed-services-offers.md)。 您也可以搭配發佈至 Azure Marketplace 的供應項目來使用這裡描述的上線程序。

上線程序需要您在服務提供者的租用戶與客戶的租用戶內都採取動作。 此文章中描述所有這些步驟。

> [!IMPORTANT]
> 目前，如果訂用帳戶使用 Azure Databricks，您無法將訂用帳戶 (或資源群組內的訂用帳戶) 上線至 Azure 委派的資源管理。 同樣地，如果訂用帳戶已向 **Microsoft.ManagedServices** 資源提供者註冊要上線，您目前無法為該訂用帳戶建立 Databricks 工作區。

## <a name="gather-tenant-and-subscription-details"></a>收集租用戶與訂用帳戶詳細資料

若要讓客戶的租用戶上線，它必須有有效的 Azure 訂用帳戶。 您將需要知道下列項目：

- 服務提供者租用戶的租用戶識別碼 (您將在其中管理客戶的資源)
- 客戶租用戶的租用戶識別碼 (其中包含受服務提供者管理的資源)
- 客戶租用戶中將由服務提供者管理之每個特定訂用帳戶 (或所含資源群組將會由服務提供者管理之訂用帳戶) 的訂用帳戶識別碼

如果您還沒有此資訊，您可以使用下列其中一種方式來擷取此資訊。

### <a name="azure-portal"></a>Azure 入口網站

將滑鼠指標暫留在 Azure 入口網站右上角的帳戶名稱上，或選取 [切換目錄]  就能查看您的租用戶識別碼。 若要選取並複製您的租使用者識別碼，請在入口網站中搜尋 "Azure Active Directory"，然後選取 [屬性]  ，並複製 [目錄識別碼]  欄位中顯示的值。 若要尋找訂用帳戶的識別碼，請搜尋「訂用帳戶」，然後選取適當的訂用帳戶識別碼。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> 當您使用此處所述的程序讓訂用帳戶 (或訂用帳戶內的一或多個資源群組) 上線時，**Microsoft.ManagedServices** 資源提供者就會針對該訂用帳戶進行註冊。

## <a name="define-roles-and-permissions"></a>定義角色與權限

身為服務提供者，您可以對單一客戶使用多個供應項目，每個都要求存取不同範圍。

若要讓管理工作更容易，我們建議針對每個角色使用 Azure AD 使用者群組，而不是將權限直接指派給該使用者，讓您可將個別使用者新增至群組或從中移除。 您也可以將角色指派給服務主體。 請務必遵循最少特殊權限準則，讓使用者只具備完成其工作所需的權限，以協助減少發生意外錯誤的機會。 如需詳細資訊，請參閱[建議的安全性作法](../concepts/recommended-security-practices.md)。

> [!NOTE]
> 角色指派必須使用角色型存取控制 (RBAC) [內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) \(部分機器翻譯\)。 除了「擁有者」和具有 [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) \(部分機器翻譯\) 權限的任何內建角色以外，所有內建角色目前都支援 Azure 委派的資源管理。 「使用者存取系統管理員」內建角色支援下述有限使用方式。 自訂角色和[傳統訂用帳戶管理員角色](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) \(部分機器翻譯\) 也未受支援。

為了定義授權，您必須知道要授與存取權的每個使用者、使用者群組或服務主體的識別碼值。 您也會需要所要指派每個內建角色的角色定義識別碼。 如果您還沒有這些資訊，您可以使用下列其中一種方式來擷取這些資訊。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>建立 Azure Resource Manager 範本

若要讓客戶上線，您必須為供應項目建立包含下列資訊的 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) 範本。 當客戶在 Azure 入口網站的[服務提供者頁面](view-manage-service-providers.md)中檢視供應項目詳細資料時，**mspOfferName** 和 **mspOfferDescription** 值都會顯示。

|欄位  |定義  |
|---------|---------|
|**mspOfferName**     |說明此定義的名稱。 此值會以供應項目標題的形式向客戶顯示。         |
|**mspOfferDescription**     |您供應項目的簡短描述 (例如，「Contoso VM 管理供應項目」)      |
|**managedByTenantId**     |您的租用戶識別碼         |
|**authorizations**     |來自您租用戶使用者/群組/SPN 的 **principalId** 值，每個都包含 **principalIdDisplayName**，以協助您的客戶了解授權的目的，並且對應至內建 **roleDefinitionId** 值以指定存取層級         |

若要讓客戶的訂用帳戶上線，請使用我們在[範例存放庫](https://github.com/Azure/Azure-Lighthouse-samples/) \(英文\) 中提供的適當 Azure Resource Manager 範本，以及修改為符合您設定並定義您授權的相對應參數檔案。 取決於您是否是讓整個訂用帳戶、資源群組或訂用帳戶內多個資源群組上線，您必須提供個別範本。 如果您偏好使用範本讓已購買受控服務供應項目 (您發佈至 Azure Marketplace) 的客戶上線，我們也提供可用於此情況的範本。

|**若要讓項目上線**  |**使用此 Azure Resource Manager 範本**  |**並修改此參數檔案** |
|---------|---------|---------|
|訂用帳戶   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|資源群組   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|訂用帳戶內的多個資源群組   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|訂用帳戶 (使用發佈至 Azure Marketplace 的供應項目時)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> 這裡描述的程序針對每個要上線的訂用帳戶，都需要個別部署。 如果您要將不同訂用帳戶中的多個資源群組上線，您也需要個別部署。 不過，將單一訂用帳戶中的多個資源群組上線，是可在一次部署中完成的。
>
> 將多個供應項目套用至相同的訂用帳戶 (或訂用帳戶內的資源群組) 時，也需要個別部署。 所套用的每個供應專案必須使用不同的 **mspOfferName**。

下列範例顯示將會用於訂用帳戶上線的已修改 **resourceProjection.parameters.json**。 與資源群組參數檔案 (位於 [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) 資料夾) 類似，但還包含 **rgName** 參數，以識別要上線的特定資源群組。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
上述範例的最後一個授權會新增具有「使用者存取系統管理員」角色 (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9) 的 **principalId**。 當您指派此角色時，必須包含 **delegatedRoleDefinitionIds** 屬性與一或多個內建角色。 在此授權中建立的使用者將能夠將這些內建角色指派至受控識別。 請注意，不會有其他一般與「使用者存取系統管理員」角色相關聯的權限套用至此使用者。

## <a name="deploy-the-azure-resource-manager-templates"></a>部署 Azure Resource Manager 範本

更新您的參數檔案之後，客戶必須將其客戶租用戶中的資源管理範本部署為訂用帳戶層級部署。 您要上線至 Azure 委派的資源管理的每個訂用帳戶 (或包含您要上線之資源群組的每個訂用帳戶)，都需要個別部署。

> [!IMPORTANT]
> 部署必須由客戶租用戶中的非來賓帳戶執行，且該租用戶對於要上線的訂用帳戶必須有[「擁有者」內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) \(部分機器翻譯\) (或包含要上線的資源群組)。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>確認上線成功

當客戶訂用帳戶已經成功地上線至 Azure 委派的資源管理時，服務提供者租用戶中的使用者將能夠查看訂用帳戶與其資源 (如果他們已透過上述程序獲授與其存取權，無論是以個人身分或以有適當權限的 Azure AD 群組成員身分)。 若要確認，請檢查並確定訂用帳戶以下列其中一種方式出現。  

### <a name="azure-portal"></a>Azure 入口網站

在服務提供者的租用戶中：

1. 瀏覽至 [[我的客戶] 頁面](view-manage-customers.md)。
2. 選取 [客戶]  。
3. 請確認您可以看到訂用帳戶顯示為您在 Resource Manager 範本中所提供的名稱。

在客戶租用戶中：

1. 瀏覽至 [[服務提供者] 頁面](view-manage-service-providers.md)。
2. 選取 [服務提供者供應項目]  。
3. 請確認您可以看到訂用帳戶顯示為您在 Resource Manager 範本中所提供的名稱。

> [!NOTE]
> 您部署完成之後可能需要幾分鐘，更新才會反映在 Azure 入口網站。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>後續步驟

- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。
- 前往 Azure 入口網站中的 [我的客戶]  ，以[檢視及管理客戶](view-manage-customers.md)。
