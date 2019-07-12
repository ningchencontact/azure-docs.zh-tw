---
title: 使用 Vm 和 Nsg 則位於 Azure 的防禦 |Microsoft Docs
description: 本文說明如何整合 Azure 防禦 NSG 存取
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 5312ad2593e732f4c84eb67ed263bc9e4666a67a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594183"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>使用 NSG 的存取和 Azure 防禦 （預覽）

當使用 Azure 防禦，您可以使用網路安全性群組 (Nsg)。 如需詳細資訊，請參閱 <<c0> [ 安全性群組](../virtual-network/security-overview.md)。 

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

* **AzureBastionSubnet:** 特定 AzureBastionSubnet 中部署 azure 的防禦。  
    * **從公用網際網路的輸入流量：** Azure 防禦會建立需要連接埠 443 的輸入流量的公用 IP 上啟用公用 IP。 不需要在 AzureBastionSubnet 上開啟連接埠 3389/22。
    * **目標 Vm 的輸出流量：** Azure 的防禦會到達目標 Vm 透過私人 IP。 Nsg 必須允許輸出至其他目標 VM 子網路的流量。
* **目標 VM 子網路：** 這是包含您想將 RDP/ssh 連線到目標虛擬機器的子網路。
    * **從 Azure 的防禦的輸入流量：** Azure 的防禦將可透過私人 ip 位址連線到目標 VM。 RDP/SSH 連接埠 (連接埠 3389 和 22 日，分別) 必須在目標端 VM 上開啟透過私人 IP。

## <a name="apply"></a>將 Nsg 套用至 AzureBastionSubnet

如果您套用到 Nsg **AzureBastionSubnet**，允許下列兩個服務標籤，為 Azure 控制平面和基礎結構：

* **(僅限 Resource Manager) GatewayManager**:此標記代表 Azure Gateway Manager 服務的位址前置詞。 如果您指定 GatewayManager 值時，允許或拒絕流量至 GatewayManager。  如果您要建立 Nsg AzureBastionSubnet 上，啟用 GatewayManager 標記的輸入流量。

* **AzureCloud (僅限 Resource Manager)** :這個標籤代表 azure 包括所有資料中心的公用 IP 位址的 IP 位址空間。 如果您指定 AzureCloud 值時，允許或拒絕流量到 Azure 的公用 IP 位址。 如果您想要只允許存取 AzureCloud 特定區域中，您可以指定地區。 例如，如果您想要只允許存取 Azure 的 AzureCloud 美國東部區域中，您可以指定 AzureCloud.EastUS 作為服務標籤。 如果您要建立 Nsg AzureBastionSubnet 上，啟用 AzureCloud 標記輸出流量。

## <a name="next-steps"></a>後續步驟

如需有關 Azure 防禦的詳細資訊，請參閱[常見問題集](bastion-faq.md)
