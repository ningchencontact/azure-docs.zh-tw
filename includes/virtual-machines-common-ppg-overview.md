---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9832ae6010f262322328ec22876a35c89e1bedca
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850323"
---
將 Vm 放在單一區域中可減少實例之間的實體距離。 將它們放在單一可用性區域中, 也會使它們實體在一起。 不過, 隨著 Azure 使用量的增加, 單一可用性區域可能會跨越多個實體資料中心, 這可能會導致網路延遲影響您的應用程式。 

若要盡可能接近 Vm, 達到最低可能的延遲, 您應該將它們部署在鄰近放置群組內。

鄰近放置群組是一種邏輯群組, 用來確保 Azure 計算資源實際位於彼此接近的位置。 鄰近放置群組適用于需要低延遲的工作負載。


- 獨立 Vm 之間的低延遲。
- 單一可用性設定組或虛擬機器擴展集中的 Vm 之間的低延遲。 
- 獨立 Vm、多個可用性設定組中的 Vm, 或多個擴展集之間的低延遲。 您可以在單一放置群組中有多個計算資源, 將多層式應用程式整合在一起。 
- 使用不同硬體類型的多個應用層之間的低延遲。 例如, 在可用性設定組中使用 M 系列來執行後端, 以及在擴展集中, 于單一鄰近放置群組中執行 D 系列實例的前端。

> [!IMPORTANT]
> 鄰近放置群組目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 在預覽期間, 這些區域無法使用鄰近放置群組:**日本東部**、**澳大利亞東部**和**印度中部**。


## <a name="best-practices"></a>最佳作法 
- 針對最低延遲, 請將鄰近放置群組與加速網路搭配使用。 如需詳細資訊, 請參閱[使用加速網路建立 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)或[使用加速網路建立 Windows 虛擬機器](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
- 在單一 resource manager 範本中部署所有 VM 大小。 為了避免登陸不支援您所需之所有 VM Sku 和大小的硬體, 請在單一範本中包含所有應用層, 使其全部都部署在同一時間。
- 如果您使用 PowerShell、CLI 或 SDK 來撰寫部署腳本, 可能會收到配置錯誤`OverconstrainedAllocationRequest`。 在此情況下, 您應該停止/解除配置所有現有的 Vm, 然後將部署腳本中的順序變更為從失敗的 VM SKU/大小開始。 
- 重複使用已刪除 Vm 的現有放置群組時, 請等候刪除作業完整完成, 再將 Vm 新增至其中。
- 如果延遲是您的第一個優先順序, 請將 Vm 放在鄰近放置群組中, 並將整個解決方案放在可用性區域中。 但是, 如果復原是您的最高優先順序, 請將您的實例分散到多個可用性區域 (單一鄰近放置群組不能跨越區域)。