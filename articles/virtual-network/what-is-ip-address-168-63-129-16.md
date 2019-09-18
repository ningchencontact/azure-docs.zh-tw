---
title: IP 位址 168.63.129.16 是什麼？ | Microsoft Docs
description: 了解 IP 位址 168.63.129.16 及其與您的資源搭配運作的方式。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 0ea8a8ec1a92a7dbc01dddc175f7116825ba00f9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067770"
---
# <a name="what-is-ip-address-1686312916"></a>IP 位址 168.63.129.16 是什麼？

IP 位址 168.63.129.16 是虛擬公用 IP 位址，有助於建構 Azure 平台資源的通訊通道。 客戶可以在 Azure 中為其私人虛擬網路定義任何位址空間。 因此，Azure 平台資源必須顯示為唯一的公用 IP 位址。 此虛擬公用 IP 位址有助於達成下列事項：

- 使 VM 代理程式能夠與平台通訊，藉以表示它處於「就緒」狀態。
- 啟用與 DNS 虛擬伺服器的通訊，以提供篩選後的名稱解析給沒有自訂 DNS 伺服器的資源 (例如 VM)。 此篩選可確保客戶只能解析其資源的主機名稱。
- 啟用[來自 Azure 負載平衡器的健康情況探查](../load-balancer/load-balancer-custom-probe-overview.md)，以判斷 vm 的健全狀況狀態。
- 允許 VM 從 Azure 中的 DHCP 服務取得動態 IP 位址。
- 為 PaaS 角色啟用客體代理程式活動訊號訊息。

## <a name="scope-of-ip-address-1686312916"></a>IP 位址 168.63.129.16 的範圍

公用 IP 位址168.63.129.16 用於所有區域和所有國家雲端。 這個特殊的公用 IP 位址是由 Microsoft 所擁有，不會變更。 預設網路安全性群組規則允許此位址。 我們建議您在輸入和輸出方向的任何本機防火牆原則中允許此 IP 位址。 此特殊 IP 位址與資源之間的通訊是安全的，因為只有內部 Azure 平台才可獲得來自此 IP 位址的訊息。 如果此位址遭到封鎖，可能在各種情況下發生非預期的行為。

[Azure Load Balancer 健康情況探查](../load-balancer/load-balancer-custom-probe-overview.md)源自于此 IP 位址。 如果您封鎖此 IP 位址，您的探查將會失敗。

在非虛擬網路案例（傳統）中，健康情況探查來自私人 IP，而不會使用168.63.129.16。

## <a name="next-steps"></a>後續步驟

- [安全性群組](security-overview.md)
- [建立、變更或刪除網路安全性群組](manage-network-security-group.md)
