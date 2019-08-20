---
title: 將 Azure 網路資源移到新的訂用帳戶或資源群組 |Microsoft Docs
description: 使用 Azure Resource Manager 將虛擬網路和其他網路資源移到新的資源群組或訂用帳戶。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: c41ab1c3309243fa3a96d907f620ffeff10376a1
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626258"
---
# <a name="move-guidance-for-networking-resources"></a>移動網路資源的指導方針

本文說明如何在特定案例中移動虛擬網路和其他網路資源。

## <a name="dependent-resources"></a>相依的資源

當您移動虛擬網路時，也必須移動其相依資源。 針對 VPN 閘道，您必須移動 IP 位址、虛擬網路閘道和所有相關聯的連線資源。 區域網路閘道可位於不同的資源群組。

若要移動具有網路介面卡的虛擬機器, 您必須移動所有相依資源。 移動網路介面卡的虛擬網路、虛擬網路的所有其他網路介面卡, 以及 VPN 閘道。

## <a name="state-of-dependent-resources"></a>相依資源的狀態

如果來源或目標資源群組包含虛擬網路, 則會在移動期間檢查虛擬網路的所有相依資源的狀態。 如果其中任何一項資源處於失敗狀態, 則會封鎖移動。 例如, 如果使用虛擬網路的虛擬機器失敗, 則移動會遭到封鎖。 即使虛擬機器不是其中一項要移動的資源, 而且不在移動的其中一個資源群組中, 移動也會遭到封鎖。 若要避免這個問題, 請將您的資源移至沒有虛擬網路的資源群組。

## <a name="peered-virtual-network"></a>對等互連虛擬網路

若要移動對等虛擬網路，您必須先停用虛擬網路對等互連。 停用之後，您可以移動虛擬網路。 移動之後，重新啟用虛擬網路對等互連。

## <a name="subnet-links"></a>子網連結

如果虛擬網路包含有資源導覽連結的子網路，則無法將虛擬網路移動到其他訂用帳戶。 例如，如果 Azure Cache for Redis 資源部署到子網路，該子網路具有資源導覽連結。

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../resource-group-move-resources.md)。
