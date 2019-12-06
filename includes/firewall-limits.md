---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/06/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a7b6867033e750f476b3d995926f0b670965a5d7
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875501"
---
| 資源 | 預設限制 |
| --- | --- |
| 資料輸送量 |30 Gbps<sup>1</sup> |
|規則|10000。 結合所有規則類型。|
|每個公用 IP 位址的 DNAT 規則|299|
|AzureFirewallSubnet 大小下限 |/26|
|網路和應用程式規則中的連接埠範圍|0-64,000。 我們正努力放寬這項限制。|
|公用 IP 位址|100最大值（目前只會針對前五個公用 IP 位址新增 SNAT 埠）。|
|路由表|根據預設，AzureFirewallSubnet 具有 0.0.0.0/0 路由，並將 NextHopType 值設為**Internet**。<br><br>「Azure 防火牆」必須能夠直接連線到網際網路。 如果您的 AzureFirewallSubnet 透過 BGP 學習到內部部署網路的預設路由，您必須以 0.0.0.0/0 UDR 覆寫它，並將**NextHopType**值設為 [**網際網路**]，以維持直接的網際網路連線能力。 根據預設，Azure 防火牆不支援對內部部署網路的強制通道。<br><br>不過，如果您的設定需要對內部部署網路的強制通道，Microsoft 將以個別案例為原則提供支援。 連絡支援人員，以便我們檢閱您的案例。 受理後，我們會允許您的訂用帳戶，並確實維持必要的防火牆網際網路連線。|

<sup>1</sup>如果您需要增加這些限制，請聯絡 Azure 支援。
