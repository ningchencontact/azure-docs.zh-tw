---
title: Azure Resource Manager 部署模式 | Microsoft Docs
description: 說明如何指定是否要透過 Azure Resource Manager 使用完整或累加部署模式。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2019
ms.author: tomfitz
ms.openlocfilehash: c0875861a90080c649596880804fe6538c04bc51
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497602"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager 部署模式

部署您的資源時，您可以指定部署是累加式更新或完整更新。  這兩種模式的主要差異在於 Resource Manager 如何處理資源群組中未在範本內的現有資源。 預設模式為累加。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="incremental-and-complete-deployments"></a>累加部署與完整部署

在這兩種模式中，Resource Manager 都會嘗試建立範本內指定的所有資源。 如果資源已存在於資源群組中，且其設定保持不變，則不會對該資源執行任何作業。 如果您變更資源的屬性值，則會以這些新值來更新資源。 如果您嘗試更新現有資源的位置或類型，部署會失敗並發生錯誤。 相反地，請以您需要的位置或類型部署新的資源。

在完整模式中，Resource Manager 會**刪除**現存於資源群組中但未在範本內指定的資源。 資源如果是範本中已指定的資源，但因為某個[條件](resource-manager-templates-resources.md#condition)評估為 false 而未部署，則不會被刪除。

在累加模式中，Resource Manager 會讓現存於資源群組中但未在範本內指定的資源**保持不變**。 以累加模式重新部署資源時，指定資源的所有屬性值，而不只是您正在更新的屬性值。 如果您未指定特定屬性，Resource Manager 會將更新解譯為覆寫這些值。

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

使用[已連結或巢狀範本](resource-group-linked-templates.md)時，您必須將 `mode` 屬性設為 `Incremental`。 只有根層級範本支援完整部署模式。

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
