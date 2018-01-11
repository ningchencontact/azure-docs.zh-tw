---
title: "Azure 的 vCPU 配額 |Microsoft 文件"
description: "了解 vCPU 配額適用於 Azure。"
keywords: 
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: b481299b62d452bc306c1f9c1fa2cdccd49b818e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="virtual-machine-vcpu-quotas"></a>虛擬機器 vCPU 配額

虛擬機器和虛擬機器擴展集的 vCPU 配額會排列在兩個層級，每個訂用帳戶，在每個區域。 第一層是總地區 Vcpu，而第二層不同 VM 大小系列核心標準 D 系列 Vcpu 例如。 每當新的 VM 時部署 Vcpu 新部署的 VM 必須不會超過特定 VM 大小系列的 vCPU 配額或總地區 vCPU 配額。 如果超過這些配額其中，VM 部署都不允許。 也是在區域中的虛擬機器的整體數目的配額。 中可以看到每個這些配額的詳細資料**使用量 + 配額**區段**訂用帳戶**頁面[Azure 入口網站](https://portal.azure.com)，或者您可以使用的值查詢PowerShell。

 
## <a name="check-usage"></a>查看使用方式

您可以使用[Get AzureRmVMUsage](/powershell/module/azurerm.compute/get-azurermvmusage) cmdlet 來檢查您的配額使用量。

```azurepowershell-interactive
Get-AzureRmVMUsage -Location "East US"
```

輸出看起來像這樣：

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count

```


## <a name="reserved-vm-instances"></a>保留的 VM 執行個體
保留的 VM 執行個體，都只限於單一訂用帳戶，且會將新的長寬加入 vCPU 配額。 這些值描述執行個體必須是可部署的訂用帳戶中的指定大小的數目。 他們在做為預留位置配額系統，以確保該配額會保留以確保可部署的訂用帳戶中的保留執行個體。 比方說，如果特定的訂用帳戶有 10 個 Standard_D1 保留的使用方式限制針對 Standard_D1 保留執行個體的執行個體將會是 10。 這會導致 Azure，以確保永遠至少 10 個 Vcpu 中有可用總地區 Vcpu 配額用於 Standard_D1 執行個體，並至少 10 個 Vcpu 中有可用的標準 D 系列 vCPU 配額，以用於 Standard_D1 執行個體。

如果需要可能購買單一的訂用帳戶 RI 增加配額，您可以[要求增加配額](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)訂用帳戶。

## <a name="next-steps"></a>後續步驟

如需計費和配額的詳細資訊，請參閱[Azure 訂用帳戶和服務限制、 配額和條件約束](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json)。