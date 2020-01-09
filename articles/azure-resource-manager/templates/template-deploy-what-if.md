---
title: 範本部署假設（預覽）
description: 在部署 Azure Resource Manager 範本之前，請先判斷您的資源會發生哪些變更。
author: mumian
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jgao
ms.openlocfilehash: edb9f5e35008b1270031d8e2d5c8a5efa37cb554
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484164"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Resource Manager 範本部署假設作業（預覽）

在部署範本之前，您可能會想要預覽即將發生的變更。 Azure Resource Manager 提供「假設」作業，讓您在部署範本時查看資源的變更方式。 「假設」作業不會對現有的資源進行任何變更。 相反地，它會在部署指定的範本時預測變更。

> [!NOTE]
> 「假設」作業目前為預覽狀態。 若要使用它，您必須[註冊預覽版](https://aka.ms/armtemplatepreviews)。 作為預覽版本，結果有時可能會顯示資源將會變更，但實際上不會發生任何變更。 我們正努力減少這些問題，但我們需要您的協助。 請在[https://aka.ms/whatifissues](https://aka.ms/whatifissues)報告這些問題。

您可以搭配 `New-AzDeploymentWhatIf` PowerShell 命令或[部署 What If](/rest/api/resources/deployments/whatif) REST 作業來使用「假設」作業。

在 PowerShell 中，輸出看起來會像這樣：

![Resource Manager 範本部署假設作業 fullresourcepayload 和變更類型](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>變更類型

「假設」作業會列出六種不同類型的變更：

- **建立**：資源目前不存在，但在範本中定義。 將會建立資源。

- **刪除**：只有在使用[完整模式](deployment-modes.md)進行部署時，才會套用此變更類型。 資源存在，但未在範本中定義。 使用完整模式時，將會刪除資源。 只有[支援完整模式刪除](complete-mode-deletion.md)的資源才會包含在此變更類型中。

- **忽略**：資源存在，但未在範本中定義。 將不會部署或修改資源。

- **NoChange**：資源存在，並定義于範本中。 資源將會重新部署，但資源的屬性不會變更。 當[ResultFormat](#result-format)設定為 `FullResourcePayloads`（這是預設值）時，就會傳回這種變更類型。

- **修改**：資源存在，並定義于範本中。 資源將會重新部署，而且資源的屬性將會變更。 當[ResultFormat](#result-format)設定為 `FullResourcePayloads`（這是預設值）時，就會傳回這種變更類型。

- **部署**：資源存在，並定義于範本中。 資源將會重新部署。 資源的屬性不一定會變更。 當作業沒有足夠的資訊可判斷是否有任何屬性變更時，作業會傳回此變更類型。 當[ResultFormat](#result-format)設定為 `ResourceIdOnly`時，您只會看到此條件。

## <a name="deployment-scope"></a>部署範圍

您可以在訂用帳戶或資源群組層級上，使用部署的假設作業。 您可以使用 `-ScopeType` 參數來設定部署範圍。 接受的值為 `Subscription` 和 `ResourceGroup`。 本文示範資源群組部署。

若要瞭解訂用帳戶層級部署，請參閱在訂用帳戶[層級建立資源群組和資源](deploy-to-subscription.md#)。

## <a name="result-format"></a>結果格式

您可以控制針對預測變更所傳回的詳細資料層級。 將 [`ResultFormat`] 參數設定為 [`FullResourcePayloads`]，以取得將會變更之資源的清單，以及將變更之屬性的詳細資料。 將 [`ResultFormat`] 參數設定為 [`ResourceIdOnly`]，以取得將會變更的資源清單。 預設值是 `FullResourcePayloads`。  

下列螢幕擷取畫面顯示兩種不同的輸出格式：

- 完整資源承載

    ![Resource Manager 範本部署假設作業 fullresourcepayloads 輸出](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- 僅限資源識別碼

    ![Resource Manager 範本部署假設作業 resourceidonly 輸出](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>執行假設作業

### <a name="set-up-environment"></a>設定環境

若要查看假設的運作方式，讓我們執行一些測試。 首先，從[可建立儲存體帳戶的 Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json)部署範本。 預設儲存體帳戶類型為 `Standard_LRS`。 您將使用此儲存體帳戶來測試如何回報變更。

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>測試修改

部署完成之後，您就可以測試「假設」作業。 執行 [假設] 命令，但將儲存體帳戶類型變更為 [`Standard_GRS`]。

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

假設輸出類似于：

![Resource Manager 範本部署假設作業輸出](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

請注意，輸出的頂端會定義色彩來指出變更的類型。

在輸出的底部，它會顯示 sku 名稱（儲存體帳戶類型）會從**Standard_LRS**變更為**Standard_GRS**。

列為 deleted 的部分屬性實際上不會變更。 在上圖中，這些屬性是 accessTier、encryption Keysource filename 和該區段中的其他內容。 當屬性不在範本中時，可能會錯誤地回報為已刪除，但會在部署期間自動設定為預設值。 此結果會被視為「假設」回應中的「雜訊」。 最後部署的資源將會有為屬性設定的值。 當假設作業成熟時，這些屬性將會從結果中篩選掉。

### <a name="test-deletion"></a>測試刪除

「假設」作業支援使用[部署模式](deployment-modes.md)。 當設定為完成模式時，不在範本中的資源會被刪除。 下列範例會部署未在完整模式中[定義任何資源的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json)。

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

因為範本中未定義任何資源，且部署模式設定為 [完成]，所以將會刪除儲存體帳戶。

![Resource Manager 範本部署假設作業輸出部署模式完成](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

請務必記住，什麼是不進行實際的變更。 儲存體帳戶仍存在於您的資源群組中。

## <a name="next-steps"></a>後續步驟

- 如果您注意到「假設」的預覽版本中有不正確的結果，請在[https://aka.ms/whatifissues](https://aka.ms/whatifissues)報告問題。
- 若要使用 Azure PowerShell 部署範本，請參閱[使用 Resource Manager 範本和 Azure PowerShell 來部署資源](deploy-powershell.md)。
- 若要使用 REST 部署範本，請參閱[使用 Resource Manager 範本部署資源和 Resource Manager REST API](deploy-rest.md)。
- 當您收到錯誤時，若要回復為成功的部署，請參閱[發生錯誤時回復至部署成功](rollback-on-error.md)。
