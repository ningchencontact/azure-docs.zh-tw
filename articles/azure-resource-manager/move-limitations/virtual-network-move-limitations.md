---
title: 將 Azure 虛擬網路的資源移動到新的訂用帳戶或資源群組 |Microsoft Docs
description: 您可以使用 Azure Resource Manager 來將虛擬網路移至新的資源群組或訂用帳戶。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 809d403a463048910a284e7f8fcdc18cf7c65b69
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723489"
---
# <a name="move-guidance-for-virtual-networks"></a>移動虛擬網路的指導方針

本文說明如何移動虛擬網路的特定案例。

## <a name="dependent-resources"></a>相依資源

當您移動虛擬網路時，也必須移動其相依資源。 針對 VPN 閘道，您必須移動 IP 位址、虛擬網路閘道和所有相關聯的連線資源。 區域網路閘道可位於不同的資源群組。

若要移動虛擬機器網路介面卡，您必須移動所有的相依資源。 移動網路介面卡的虛擬網路中，所有其他網路介面卡的虛擬網路和 VPN 閘道。

## <a name="peered-virtual-network"></a>對等互連的虛擬網路

若要移動對等虛擬網路，您必須先停用虛擬網路對等互連。 停用之後，您可以移動虛擬網路。 移動之後，重新啟用虛擬網路對等互連。

## <a name="subnet-links"></a>子網路連結

如果虛擬網路包含有資源導覽連結的子網路，則無法將虛擬網路移動到其他訂用帳戶。 例如，如果 Azure Cache for Redis 資源部署到子網路，該子網路具有資源導覽連結。

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../resource-group-move-resources.md)。
