---
title: 將 Azure 資源移至新的訂用帳戶或資源群組 |Microsoft Docs
description: 使用 Azure Resource Manager 將資源移到新的資源群組或訂用帳戶。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 69cd6031111c72d54cb87975c2040078a9965821
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035543"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>將資源移到新的資源群組或訂用帳戶

本文示範如何將 Azure 資源移至其他 Azure 訂用帳戶或相同訂用帳戶內的其他資源群組。 您可以使用 Azure 入口網站、Azure PowerShell、Azure CLI 或 REST API 來移動資源。

移動作業期間會同時鎖定來源群組和目標群組。 資源群組上的寫入和刪除作業將會封鎖，直到移動完成。 此鎖定表示您無法新增、更新或刪除資源群組中的資源，但不表示資源已遭到凍結。 例如，如果您將 SQL Server 和其資料庫移至新的資源群組，使用該資料庫的應用程式不會發生停機時間。 它仍可對資料庫讀取和寫入。

移動資源只會將其移到新的資源群組或訂用帳戶。 它不會變更資源的位置。

## <a name="checklist-before-moving-resources"></a>移動資源前的檢查清單

在移動資源之前，有幾個重要步驟需要進行。 藉由驗證這些條件，您可以避免錯誤。

1. 您想要移動的資源必須支援移動作業。 如需哪些資源支援移動的清單, 請參閱[資源的移動作業支援](move-support-resources.md)。

1. 某些服務在移動資源時有特定的限制或需求。 如果您已移動下列任何服務, 請在移動前檢查該指引。

   * [應用程式服務移動指引](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services 移動指引](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [傳統部署模型移動指引](./move-limitations/classic-model-move-limitations.md)-傳統計算、傳統儲存體、傳統虛擬網路和雲端服務
   * [網路移動指引](./move-limitations/networking-move-limitations.md)
   * [復原服務移動指引](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [虛擬機器移動指引](./move-limitations/virtual-machines-move-limitations.md)

1. 來源和目的地訂用帳戶必須為作用中。 如果您在啟用已停用的帳戶時遇到問題，請[建立 Azure 支援要求](../azure-supportability/how-to-create-azure-support-request.md)。 針對問題類型選取 [訂用帳戶管理]。

1. 來源和目的地的訂用帳戶必須存在於相同的 [Azure Active Directory 租用戶](../active-directory/develop/quickstart-create-new-tenant.md)內。 若要檢查這兩個訂用帳戶都有相同的租用戶識別碼，請使用 Azure PowerShell 或 Azure CLI。

   如果是 Azure PowerShell，請使用：

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   對於 Azure CLI，請使用：

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   如果來源和目的地訂用帳戶的租用戶識別碼不相同，請使用下列方法來協調租用戶識別碼：

   * [將 Azure 訂用帳戶的擁有權轉移給另一個帳戶](../billing/billing-subscription-transfer.md)
   * [如何將 Azure 訂用帳戶關聯或新增至 Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 必須針對要移動之資源的資源提供者註冊其目的地訂用帳戶。 否則，您會收到錯誤，指出 **未針對資源類型註冊訂用帳戶**。 將資源移至新的訂用帳戶時，可能會因為不曾以指定的資源類型使用過該訂用帳戶，因而出現此錯誤。

   針對 PowerShell，使用下列命令來取得註冊狀態：

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   若要註冊資源提供者，請使用：

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   針對 Azure CLI，使用下列命令來取得註冊狀態：

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   若要註冊資源提供者，請使用：

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. 移動資源的帳戶至少必須有下列權限：

   * 來源資源群組上的 **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action**。
   * 來源資源群組上的 **Microsoft.Resources/subscriptions/resourceGroups/write**。

1. 移動資源之前，請針對您要將資源移入的訂用帳戶，檢查其訂用帳戶配額。 如果移動資源表示訂用帳戶會超出限制，那麼您必須檢閱您是否可以要求增加配額。 如需限制清單和如何要求增加配額的資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。

1. **針對跨訂用帳戶移動, 資源和其相依資源必須位於相同的資源群組中, 而且必須一起移動。** 例如, 具有受控磁片的 VM 需要將 VM 和受控磁片一起移動, 以及其他相依資源。

   如果您要將資源移到新的訂用帳戶, 請查看資源是否有任何相依資源, 以及它們是否位於相同的資源群組中。 如果資源不在相同的資源群組中, 請檢查資源是否可以合併到相同的資源群組中。 若是如此, 請在資源群組之間使用移動作業, 將所有這些資源帶入相同的資源群組。

   如需詳細資訊, 請參閱跨訂用帳戶[移動的案例](#scenario-for-move-across-subscriptions)。

## <a name="scenario-for-move-across-subscriptions"></a>跨訂用帳戶移動的案例

將資源從一個訂用帳戶移至另一個訂用帳戶是三個步驟的程式:

![跨訂用帳戶移動案例](./media/resource-group-move-resources/cross-subscription-move-scenario.png)

為了方便說明, 我們只有一個相依資源。

* 步驟 1:如果相依資源分散到不同的資源群組, 請先將它們移到一個資源群組中。
* 步驟 2:將資源和相依資源與來源訂用帳戶一起移至目標訂用帳戶。
* 步驟 3：(選擇性) 將相依資源重新散發至目標訂用帳戶中的不同資源群組。 

## <a name="validate-move"></a>驗證移動

[驗證移動作業](/rest/api/resources/resources/validatemoveresources)可讓您直接測試移動案例，而不需要實際移動資源。 使用此作業來檢查移動是否成功。 當您傳送移動要求時, 會自動呼叫驗證。 只有當您需要預先確定結果時, 才使用此作業。 若要執行這項作業，您需要：

* 來源資源群組的名稱
* 目標資源群組的資源識別碼
* 要移動的每個資源所具備的資源識別碼
* 帳戶的[存取權杖](/rest/api/azure/#acquire-an-access-token)

傳送下列要求：

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

使用要求本文：

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

如果要求的格式正確，此作業將會傳回：

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 狀態碼指出已接受驗證要求，但尚未判斷移動作業是否會成功。 `location` 值包含一個 URL，可讓您用來檢查長時間執行作業的狀態。  

若要檢查狀態，請傳送下列要求：

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

當作業仍在執行時，您會持續收到 202 狀態碼。 請等候 `retry-after` 值中指出的秒數，再重新嘗試。 如果移動作業驗證成功，您會收到 204 狀態碼。 如果移動驗證失敗，則會收到錯誤訊息，例如：

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>使用入口網站

若要移動資源，請選取具有這些資源的資源群組，然後選取 [移動] 按鈕。

![移動資源](./media/resource-group-move-resources/select-move.png)

選擇將資源移動到新的資源群組或新的訂用帳戶。

選取要移動的資源和目的地資源群組。 認可您需要更新這些資源的指令碼，然後選取 [確定]。 如果您在上一個步驟中選取了 [編輯訂用帳戶] 圖示，則也必須選取目的地訂用帳戶。

![選取目的地](./media/resource-group-move-resources/select-destination.png)

在 [通知]中，您會看到移動作業正在執行。

![顯示移動狀態](./media/resource-group-move-resources/show-status.png)

完成後，您會收到結果的通知。

![顯示移動結果](./media/resource-group-move-resources/show-result.png)

如果您收到錯誤, 請參閱針對[將 Azure 資源移至新的資源群組或訂用帳戶進行疑難排解](troubleshoot-move.md)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 [Move-AzResource](/powershell/module/az.resources/move-azresource) 命令。 下列範例示範了如何將多個資源移動到新的資源群組。

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

若要移動到新的訂用帳戶，請包含 `DestinationSubscriptionId`參數的值。

如果您收到錯誤, 請參閱針對[將 Azure 資源移至新的資源群組或訂用帳戶進行疑難排解](troubleshoot-move.md)。

## <a name="use-azure-cli"></a>使用 Azure CLI

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) 命令。 提供要移動之資源的資源識別碼。 下列範例示範了如何將多個資源移動到新的資源群組。 請在 `--ids` 參數中，為要移動的資源識別碼提供以空格分隔的清單。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

若要移動到新的訂用帳戶，請提供 `--destination-subscription-id` 參數。

如果您收到錯誤, 請參閱針對[將 Azure 資源移至新的資源群組或訂用帳戶進行疑難排解](troubleshoot-move.md)。

## <a name="use-rest-api"></a>使用 REST API

若要將現有的資源移至另一個資源群組或訂用帳戶, 請使用[移動資源](/rest/api/resources/Resources/MoveResources)作業。

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

在要求主體中，您可以指定目標資源群組以及要移動的資源。

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

如果您收到錯誤, 請參閱針對[將 Azure 資源移至新的資源群組或訂用帳戶進行疑難排解](troubleshoot-move.md)。

## <a name="next-steps"></a>後續步驟

如需哪些資源支援移動的清單, 請參閱[資源的移動作業支援](move-support-resources.md)。
