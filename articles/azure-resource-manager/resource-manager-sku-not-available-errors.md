---
title: Azure SKU 無法使用錯誤 | Microsoft Docs
description: 描述如何針對部署期間 SKU 無法使用錯誤進行疑難排解。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.author: tomfitz
ms.openlocfilehash: 1dd0532452c3558e53f0236998953d2055ed328c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390733"
---
# <a name="resolve-errors-for-sku-not-available"></a>解決 SKU 無法使用的錯誤

本文說明如何解決 **SkuNotAvailable** 錯誤。 如果您在該區域或符合您業務需求的替代區域中找不到適當的 SKU，請向 Azure 支援服務提交 [SKU 要求](https://aka.ms/skurestriction)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>徵狀

在部署資源 (通常是虛擬機器) 時，您可能會收到下列錯誤碼和錯誤訊息︰

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>原因

當您選取的資源 SKU (例如 VM 大小) 不適用於您選取的位置時，就會收到這個錯誤。

## <a name="solution-1---powershell"></a>解決方案 1：PowerShell

若要判斷區域中有哪些可用的 SKU，請使用 [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) 命令。 依據位置篩選結果。 您必須擁有最新版 PowerShell 才能執行此命令。

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

結果包括該位置的 SKU 清單，以及該 SKU 的任何限制。 請注意，SKU 可能會列出為 `NotAvailableForSubscription`。

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>解決方案 2：Azure CLI

若要判斷區域中可以使用哪些 SKU，請使用 `az vm list-skus` 命令。 使用 `--location` 參數可將輸出篩選至您要使用的位置。 使用 `--size` 參數可依部分大小名稱進行搜尋。

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

此命令會傳回類似以下的結果：

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>解決方案 3：Azure 入口網站

若要判斷區域中可以使用哪些 SKU，請使用[入口網站](https://portal.azure.com)。 登入入口網站，然後透過介面新增資源。 當您設定值時，您會看到該資源可用的 SKU。 您不需要完成部署。

例如，請開始建立虛擬機器的程序。 若要查看其他可用的大小，請選取 [變更大小]。

![建立 VM](./media/resource-manager-sku-not-available-errors/create-vm.png)

您可以篩選及捲動瀏覽可用的大小。

![可用的 SKU](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>解決方案 4：REST

若要判斷區域中可用的 SKU，請使用[資源 SKU - 列出](/rest/api/compute/resourceskus/list)作業。

它會以下列格式傳回可用的 SKU 和區域︰

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

