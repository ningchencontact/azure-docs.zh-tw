---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/25/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 368a7cf1d372d79f062affe9b206d070afe5c2f7
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505881"
---
| 資源 | 預設限制 |
| --- | --- |
| 資料輸送量 |30 Gbps<sup>1</sup> |
|規則|10000，有些規則類型結合。|
|AzureFirewallSubnet 大小下限 |/26|
|網路和應用程式規則中的連接埠範圍|0-64,000。 我們正努力放寬這項限制。|
|路由表|根據預設，AzureFirewallSubnet 具有 0.0.0.0/0 路由的 NextHopType 值設為**網際網路**。<br><br>如果您啟用強制通道，在內部透過 ExpressRoute 或 VPN 閘道，您可能需要明確且 NextHopType 值設定為 網際網路設定 0.0.0.0/0 的使用者定義路由 (UDR)，並將它與您 AzureFirewallSubnet 產生關聯。 這會覆寫潛在的預設閘道回到您的內部部署網路的 BGP 公告。 如果您的組織需要 Azure 防火牆，以將預設閘道流量回到您的內部部署網路的強制通道，請連絡支援服務。 我們可以在維護您的訂用帳戶，以確保必要的網際網路連線防火牆的白名單。|

<sup>1</sup>如果您需要增加這些限制，請連絡 Azure 支援服務。
