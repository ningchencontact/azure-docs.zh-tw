---
title: 包含檔案
description: 包含檔案
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 388d6641fb77d09c1706623b84ec256573807747
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664423"
---
### <a name="network-interfaces"></a>網路介面

|  |  |
|---------|---------|
| [對每個 NIC 使用 NSG X](../articles/azure-policy/scripts/nsg-on-nic.md) | 要求搭配每個虛擬網路介面使用特定的網路安全性群組。 您需指定要使用之網路安全性群組的識別碼。 |
| [針對 VM 網路介面使用已核准的子網路](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) | 要求網路介面使用已核准的子網路。 您需指定已核准之子網路的識別碼。 |
| [針對 VM 網路介面使用已核准的 vNet](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) | 要求網路介面使用已核准的虛擬網路。 您需指定已核准之虛擬網路的識別碼。 |