---
title: 使用 Vm 和 Nsg 則位於 Azure 的防禦 |Microsoft Docs
description: 本文說明如何整合 Azure 防禦 NSG 存取
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: e7210b2b1be072f5326070d768d5fe12c386ee0b
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191599"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>使用 NSG 的存取和 Azure 防禦 （預覽）

當使用 Azure 防禦，您可以使用網路安全性群組 (Nsg)。 如需詳細資訊，請參閱 <<c0> [ 安全性群組](../virtual-network/security-overview.md)。 

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

![架構](./media/bastion-nsg/nsg_architecture.png)

在此圖中：

* 虛擬網路中部署的防禦主機。
* 使用者連線至 Azure 入口網站中使用任何 HTML5 瀏覽器。
* 使用者選取要連接到虛擬機器。
* 只要按一下，RDP/SSH 工作階段會在瀏覽器中開啟。
* 不含公用 IP 必須在 Azure VM 上。

## <a name="nsg"></a>網路安全性群組

* **AzureBastionSubnet:** 特定 AzureBastionSubnet 中部署 azure 的防禦。  
    * **從公用網際網路的輸入流量：** Azure 防禦會建立需要連接埠 443 的輸入流量的公用 IP 上啟用公用 IP。 不需要在 AzureBastionSubnet 上開啟連接埠 3389/22。
    * **目標 Vm 的輸出流量：** Azure 的防禦會到達目標 Vm 透過私人 IP。 Nsg 必須允許輸出至其他目標 VM 子網路的流量。
* **目標 VM 子網路：** 這是包含您想將 RDP/ssh 連線到目標虛擬機器的子網路。
    * **從 Azure 的防禦的輸入流量：** Azure 的防禦將可透過私人 ip 位址連線到目標 VM。 RDP/SSH 連接埠 (連接埠 3389 和 22 日，分別) 必須在目標端 VM 上開啟透過私人 IP。

## <a name="apply"></a>將 Nsg 套用至 AzureBastionSubnet

如果您套用到 Nsg **AzureBastionSubnet**，允許下列兩個服務標籤，為 Azure 控制平面和基礎結構：

* **(僅限 Resource Manager) GatewayManager**:此標記代表 Azure Gateway Manager 服務的位址前置詞。 如果您指定 GatewayManager 值時，允許或拒絕流量至 GatewayManager。

* **AzureCloud (僅限 Resource Manager)** :這個標籤代表 azure 包括所有資料中心的公用 IP 位址的 IP 位址空間。 如果您指定 AzureCloud 值時，允許或拒絕流量到 Azure 的公用 IP 位址。 如果您只想允許存取 AzureCloud 特定區域中，您可以指定地區。 例如，如果您想要只允許存取 Azure 的 AzureCloud 美國東部區域中，您可以指定 AzureCloud.EastUS 作為服務標籤。

## <a name="next-steps"></a>後續步驟

如需有關 Azure 防禦的詳細資訊，請參閱[常見問題集](bastion-faq.md)