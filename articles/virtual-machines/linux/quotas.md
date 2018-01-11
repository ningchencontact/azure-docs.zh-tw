---
title: "Azure 的 vCPU 配額 |Microsoft 文件"
description: "了解 vCPU 配額適用於 Azure。"
keywords: 
services: virtual-machines-linux
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: 8b99fd92d9031b7172e698cf5db1f776387bdfb9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="virtual-machine-vcpu-quotas"></a>虛擬機器 vCPU 配額

虛擬機器和虛擬機器擴展集的 vCPU 配額會排列在兩個層級，每個訂用帳戶，在每個區域。 第一層是總地區 Vcpu，而第二層不同 VM 大小系列核心標準 D 系列 Vcpu 例如。 每當新的 VM 時部署 Vcpu 新部署的 VM 必須不會超過特定 VM 大小系列的 vCPU 配額或總地區 vCPU 配額。 如果超過這些配額其中，VM 部署都不允許。 也是在區域中的虛擬機器的整體數目的配額。 中可以看到每個這些配額的詳細資料**使用量 + 配額**區段**訂用帳戶**頁面[Azure 入口網站](https://portal.azure.com)，或者您可以使用 Azure CLI 值查詢.


## <a name="check-usage"></a>查看使用方式

您可以檢查您配額使用量使用[az vm 清單使用量](/cli/azure/vm#az_vm_list_usage)。

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
保留的 VM 執行個體，都只限於單一訂用帳戶，且會將新的長寬加入 vCPU 配額。 這些值描述執行個體必須是可部署的訂用帳戶中的指定大小的數目。 他們在做為預留位置配額系統，以確保該配額會保留以確保可部署的訂用帳戶中的保留執行個體。 比方說，如果特定的訂用帳戶有 10 個 Standard_D1 保留的使用方式限制針對 Standard_D1 保留執行個體的執行個體將會是 10。 這會導致 Azure，以確保永遠至少 10 個 Vcpu 中有可用總地區 Vcpu 配額用於 Standard_D1 執行個體，並至少 10 個 Vcpu 中有可用的標準 D 系列 vCPU 配額，以用於 Standard_D1 執行個體。

如果需要可能購買單一的訂用帳戶 RI 增加配額，您可以[要求增加配額](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)訂用帳戶。

## <a name="next-steps"></a>後續步驟

如需計費和配額的詳細資訊，請參閱[Azure 訂用帳戶和服務限制、 配額和條件約束](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json)。
