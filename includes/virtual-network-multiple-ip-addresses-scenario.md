---
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 11/09/2018
ms.date: 01/21/2019
ms.author: v-yeche
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742226"
---
## <a name="scenario"></a>案例
建立具有單一 NIC 的 VM，並連接至虛擬網路。 VM 需要三個不同的「私人」IP 位址和兩個「公用」IP 位址。 IP 位址會指派給下列 IP 組態︰

* **IPConfig-1：** 分配一个静态专用 IP 地址和一个静态公共 IP 地址。
* **IPConfig-2：** 分配一个静态专用 IP 地址和一个静态公共 IP 地址。
* **IPConfig-3：** 分配一个静态专用 IP 地址，不分配公共 IP 地址。

    ![多個 IP 位址](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

建立 NIC 時，IP 組態會與 NIC 產生關聯，而建立 VM 時，NIC 會連結至 VM。 此案例使用的 IP 位址類型只是舉例說明。 您可以指派您需要的任何 IP 位址和作業類型。

> [!NOTE]
> 雖然本文中的步驟會將所有 IP 組態指派給單一 NIC，您也可以指派多個 IP 組態給多個 NIC VM 中的任何 NIC。 若要了解如何创建具有多个 NIC 的 VM，请阅读[创建具有多个 NIC 的 VM](../articles/virtual-machines/windows/multiple-nics.md)一文。
