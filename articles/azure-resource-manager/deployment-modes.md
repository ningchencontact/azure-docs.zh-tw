---
title: Azure Resource Manager 部署模式 | Microsoft Docs
description: 說明如何指定是否要透過 Azure Resource Manager 使用完整或累加部署模式。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: tomfitz
ms.openlocfilehash: 10a9917d8ed763b133fbd33aedd16da399a224b2
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881647"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager 部署模式

部署您的資源時，您可以指定部署是累加式更新或完整更新。  這兩種模式的主要差異在於 Resource Manager 如何處理資源群組中未在範本內的現有資源。 預設模式為累加。

在這兩種模式中，Resource Manager 都會嘗試建立範本內指定的所有資源。 如果資源已存在於資源群組中，且其設定保持不變，則不會對該資源執行任何作業。 如果您變更資源的屬性值，則會以這些新值來更新資源。 如果您嘗試更新現有資源的位置或類型，部署會失敗並發生錯誤。 相反地，請以您需要的位置或類型部署新的資源。

## <a name="complete-mode"></a>完整模式

在完整模式中，Resource Manager 會**刪除**現存於資源群組中但未在範本內指定的資源。

如果您的範本包含未部署的資源，因為[條件](conditional-resource-deployment.md)評估為 false，則結果會取決於您用來部署範本的 REST API 版本。 如果您使用早于2019-05-10 的版本，則**不會刪除**資源。 使用2019-05-10 或更新版本時，**會刪除**資源。 Azure PowerShell 和 Azure CLI 的最新版本會刪除資源。

請小心使用完整模式搭配[複製迴圈](resource-group-create-multiple.md)。 在解析複製迴圈之後，未在範本中指定的任何資源都會被刪除。

資源類型處理完整模式刪除的方式有一些差異。 當不在以完整模式部署的範本中時，將自動刪除父代資源。 當不在範本中時，不會自動刪除某些子系資源。 不過，如果刪除父資源，則會刪除這些子資源。 

例如，如果您的資源群組包含 DNS 區域 (Microsoft.Network/dnsZones 資源類型) 和 CNAME 記錄 (Microsoft.Network/dnsZones/CNAME 資源類型)，則 DNS 區域是 CNAME 記錄的父代資源。 如果您使用完整模式部署，且範本中不包含 DNS 區域，則 DNS 區域和 CNAME 記錄都將同時刪除。 如果您在範本中包含 DNS 區域，但未包含 CNAME 記錄，則不會刪除 CNAME。 

如需資源類型如何處理刪除的清單，請參閱[刪除完整模式部署的 Azure 資源](complete-mode-deletion.md)。

如果資源群組已[鎖定](resource-group-lock-resources.md)，[完成] 模式不會刪除資源。

> [!NOTE]
> 只有根層級範本支援完整部署模式。 針對[連結或巢狀的範本](resource-group-linked-templates.md)，您必須使用累加模式。 
>
> [訂用帳戶層級部署](deploy-to-subscription.md)不支援完整模式。
>
> 目前，入口網站不支援完整模式。
>

## <a name="incremental-mode"></a>累加模式

在累加模式中，Resource Manager 會讓現存於資源群組中但未在範本內指定的資源**保持不變**。

不過，在累加模式中重新部署現有的資源時，結果會不同。 指定資源的所有內容，而不只是您要更新的屬性。 常見的誤解是認為未指定的屬性會保持不變。 如果您未指定特定屬性，Resource Manager 會將更新解譯為覆寫這些值。

## <a name="example-result"></a>範例結果

若要說明增量和完整模式之間的差異，請考慮下列案例。

**資源群組**包含：

* 資源 A
* 資源 B
* 資源 C

**範本**包含：

* 資源 A
* 資源 B
* 資源 D

部署於**累加**模式時，資源群組具有：

* 資源 A
* 資源 B
* 資源 C
* 資源 D

若部署在**完整**模式中，資源 C 會遭到刪除。 資源群組具有：

* 資源 A
* 資源 B
* 資源 D

## <a name="set-deployment-mode"></a>設定部署模式

若要在使用 PowerShell 部署時設定部署模式，使用 `Mode` 參數。

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

若要在使用 Azure CLI 部署時設定部署模式，使用 `mode` 參數。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

下列範例示範設定為累加部署模式的已連結範本：

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>後續步驟

* 若要了解如何建立資源管理員範本，請參閱 [編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
* 若要了解如何部署資源，請參閱 [使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。
* 若要檢視資源提供者的作業，請參閱 [Azure REST API](/rest/api/)。
