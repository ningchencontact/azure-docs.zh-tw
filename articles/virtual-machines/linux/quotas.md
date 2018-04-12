---
title: Azure 的 vCPU 配額 | Microsoft Docs
description: 了解 Azure 的 vCPU 配額。
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: a4e0bbe1c6d9b121dfb422934cdd67ff19f80482
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machine-vcpu-quotas"></a>虛擬機器 vCPU 配額

虛擬機器和虛擬機器擴展集的 vCPU 配額依每個地區的每個訂用帳戶分兩層排列。 第一層是「區域 vCPU 總計」，第二層是不同的 VM 大小系列核心，例如「標準 D 系列 vCPU」。 每當部署新的 VM 時，新部署 VM 的 vCPU 不得超過特定 VM 大小系列的 vCPU 配額或區域 vCPU 總計配額。 如果超過這些配額，將不允許 VM 部署。 另外還有區域中虛擬機器整體數目的配額。 在 [Azure 入口網站](https://portal.azure.com) [訂用帳戶] 頁面的 [使用量 + 配額] 區段中可以看到這些配額的詳細資料，或者您可以使用 Azure CLI 查詢這些值。


## <a name="check-usage"></a>檢查使用量

您可以使用 [az vm list-usage](/cli/azure/vm#az_vm_list_usage)來檢查您配額使用量。

```azurecli-interactive
az vm list-usage --location "East US"
[
  …
  {
    "currentValue": 4,
    "limit": 260,
    "name": {
      "localizedValue": "Total Regional vCPUs",
      "value": "cores"
    }
  },
  {
    "currentValue": 4,
    "limit": 10000,
    "name": {
      "localizedValue": "Virtual Machines",
      "value": "virtualMachines"
    }
  },
  {
    "currentValue": 1,
    "limit": 2000,
    "name": {
      "localizedValue": "Virtual Machine Scale Sets",
      "value": "virtualMachineScaleSets"
    }
  },
  {
    "currentValue": 1,
    "limit": 10,
    "name": {
      "localizedValue": "Standard B Family vCPUs",
      "value": "standardBFamily"
    }
  },
```
## <a name="reserved-vm-instances"></a>保留的 VM 執行個體
保留的 VM 執行個體 (僅限於單一訂用帳戶) 將為 vCPU 配額新增新的層面。 這些值描述在訂用帳戶中必須是可部署的指定大小執行個體數目。 它們是配額系統中的預留位置，以確保該配額會被保留，使保留的執行個體可於訂用帳戶中部署。 例如，如果特定訂用帳戶有 10 個保留的執行個體 Standard_D1，則保留的執行個體 Standard_D1 的使用量限制將為 10。 這可讓 Azure 確保「區域 vCPU 總計」配額中永遠至少有 10 個 vCPU 可用於 Standard_D1 執行個體，「標準 D 系列 vCPU」配額中有至少 10 個 vCPU 可用於 Standard_D1 執行個體。

如果需要購買單一訂用帳戶 RI 來增加配額，您可以在訂用帳戶中[要求增加配額](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

## <a name="next-steps"></a>後續步驟

如需有關帳單和配額的詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json)。
