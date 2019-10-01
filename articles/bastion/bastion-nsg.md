---
title: 使用 Azure 防禦中的 Vm 和 Nsg |Microsoft Docs
description: 本文說明如何將 NSG 存取納入 Azure 防禦
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: cherylmc
ms.openlocfilehash: 4f99b24435998fc4d0c7ab724c66a318586a80d4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694937"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>使用 NSG access 和 Azure 防禦（預覽）

使用 Azure 防禦時，您可以使用網路安全性群組（Nsg）。 如需詳細資訊，請參閱[安全性群組](../virtual-network/security-overview.md)。 

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

![架構](./media/bastion-nsg/nsg_architecture.png)

在此圖表中：

* 堡壘主機部署在虛擬網路中。
* 使用者使用任何 HTML5 瀏覽器連線到 Azure 入口網站。
* 使用者選取要連線的虛擬機器。
* 只要按一下，RDP/SSH 工作階段就會在瀏覽器中開啟。
* Azure VM 上不需要公用 IP。

## <a name="nsg"></a>網路安全性群組

* **AzureBastionSubnet:** Azure 防禦會部署在特定 AzureBastionSubnet 中。  
    * **來自公用網際網路的輸入流量：** Azure 防禦會建立公用 IP，其必須在公用 IP 上啟用埠443，以供輸入流量使用。 不需要在 AzureBastionSubnet 上開啟埠3389/22。
    * **目標 Vm 的輸出流量：** Azure 防禦會透過私人 IP 到達目標 Vm。 Nsg 需要允許其他目標 VM 子網的輸出流量。
* **目標 VM 子網：** 這是包含您想要 RDP/SSH 的目標虛擬機器的子網。
    * **來自 Azure 防禦的輸入流量：** Azure 防禦會透過私人 IP 到達目標 VM。 RDP/SSH 埠（分別是埠3389和22）必須在目標 VM 端透過私人 IP 開啟。

## <a name="apply"></a>將 Nsg 套用至 AzureBastionSubnet

如果您將 Nsg 套用至**AzureBastionSubnet**，請允許 Azure 控制平面和基礎結構的下列兩個服務標記：

* **GatewayManager （僅限 Resource Manager）** ：此標記代表 Azure Gateway Manager 服務的位址前置詞。 如果您指定 GatewayManager 作為值，就會允許或拒絕 GatewayManager 的流量。  如果您要在 AzureBastionSubnet 上建立 Nsg，請為輸入流量啟用 GatewayManager 標記。

* **AzureCloud （僅限 Resource Manager）** ：此標記代表包含所有資料中心公用 IP 位址的 Azure IP 位址空間。 如果您指定 AzureCloud 作為值，就會允許或拒絕 Azure 公用 IP 位址的流量。 如果您只想要在特定區域中允許存取 AzureCloud，您可以指定區域。 例如，如果您只想要允許美國東部區域中的 Azure AzureCloud 存取，您可以指定 AzureCloud. EastUS 做為服務標記。 如果您要在 AzureBastionSubnet 上建立 Nsg，請針對輸出流量啟用 AzureCloud 標記。 如果您開啟埠443供輸入到網際網路，您應該不需要啟用輸入流量的 AzureCloud 標記。

## <a name="next-steps"></a>後續步驟

如需 Azure 防禦的詳細資訊，請參閱[常見問題](bastion-faq.md)
