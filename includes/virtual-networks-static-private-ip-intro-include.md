---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 5c1caf87f32ddd827b85263ee634d3e15d821124
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52269336"
---
您的 IaaS 虛擬機器 (VM) 和虛擬網路中的 PaaS 角色執行個體，會根據其所連線的子網路，從您指定的範圍自動接收私人 IP 位址。 在受到解除委任之前，VM 和角色執行個體會保留該位址。 您藉由從 PowerShell、Azure CLI 或 Azure 入口網站停止 VM 或角色執行個體來加以解除委任。 在這些情況下，一旦 VM 或角色執行個體再次開始，即會收到來自 Azure 基礎結構的可用 IP 位址，其可能與先前的不同。 如果您從客體作業系統關閉 VM 或角色執行個體時，它會保留其擁有的 IP 位址。  

在某些情況下，您希望 VM 或角色執行個體具有靜態 IP 位址，例如，如果您的 VM 即將執行 DNS 或將成為網域控制站。 您可以設定靜態的私人 IP 位址來這麼做。

