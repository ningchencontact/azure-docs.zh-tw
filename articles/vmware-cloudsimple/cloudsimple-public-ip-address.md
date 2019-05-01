---
title: VMware 的解決方案，由 CloudSimple-Azure 公用 IP 位址
description: 深入了解公用 IP 位址以及由 CloudSimple VMware 解決方案及其優點
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a366ec821e5e9663908f790ab3a88424fa956075
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576966"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple 公用 IP 位址概觀

公用 IP 位址可讓您輸入時，私用雲端資源的私人 IP 位址進行通訊的網際網路資源。 虛擬機器或軟體負載平衡器的私人 IP 位址。 私人 IP 位址是私用雲端 vCenter 上。 公用 IP 位址可讓您公開到網際網路在您的私人雲端上執行的服務。

直到您取消指派的公用 IP 位址被專用的私人 IP 位址。 公用 IP 位址只可指派給一個私人 IP 位址。

一律使用公用 IP 位址相關聯的資源使用的公用 IP 位址來存取網際網路。 根據預設，公用 IP 位址允許輸出網際網路存取。  公用 IP 位址上的連入流量遭到拒絕。  若要允許輸入的流量，請建立公用 IP 位址的特定連接埠的防火牆規則。

## <a name="benefits"></a>優點

使用公用 IP 位址進行輸入通訊提供：

* 分散式的阻斷服務 (DDoS) 攻擊防護的詳細資訊。 公用 IP 位址，會自動啟用這項保護。
* 永遠可用流量監視和即時安全防護功能的常見網路層級的攻擊。 這些防禦功能是 Microsoft online services 使用的相同防禦。
* Azure 的全球網路的整個小數位數。 網路可用於分散和減少跨區域的攻擊流量。  

## <a name="next-steps"></a>後續步驟

* 了解如何[配置公用 IP 位址](https://docs.azure.cloudsimple.com/publicips/)
