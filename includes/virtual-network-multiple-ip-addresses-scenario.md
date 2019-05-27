---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170780"
---
## <a name="scenario"></a>案例
建立具有單一 NIC 的 VM，並連接至虛擬網路。 VM 需要三個不同的「私人」IP 位址和兩個「公用」IP 位址。 IP 位址會指派給下列 IP 組態︰

* **Ipconfig-1:** 指派*靜態*私人 IP 位址並*靜態*公用 IP 位址。
* **Ipconfig-2︰** 指派*靜態*私人 IP 位址並*靜態*公用 IP 位址。
* **Ipconfig-3:** 指派*靜態*私人 IP 位址並沒有公用 IP 位址。
  
    ![多個 IP 位址](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

建立 NIC 時，IP 組態會與 NIC 產生關聯，而建立 VM 時，NIC 會連結至 VM。 此案例使用的 IP 位址類型只是舉例說明。 您可以指派您需要的任何 IP 位址和作業類型。

> [!NOTE]
> 雖然本文中的步驟會將所有 IP 組態指派給單一 NIC，您也可以指派多個 IP 組態給多個 NIC VM 中的任何 NIC。 閱讀[建立具有多個 NIC 的 VM](../articles/virtual-machines/windows/multiple-nics.md) 文章以了解如何建立具有多個 NIC 的 VM。