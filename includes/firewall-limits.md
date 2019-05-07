---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 5/3/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8709d4d903bd31ff94d04ec61e226857e4190407
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150311"
---
| 資源 | 預設限制 |
| --- | --- |
| 資料輸送量 |30 Gbps<sup>1</sup> |
|規則|10000，有些規則類型結合。|
|AzureFirewallSubnet 大小下限 |/26|
|網路和應用程式規則中的連接埠範圍|0-64,000。 我們正努力放寬這項限制。|
|路由表|根據預設，AzureFirewallSubnet 具有 0.0.0.0/0 路由的 NextHopType 值設為**網際網路**。<br><br>Azure 的防火牆必須有直接的網際網路連線。 如果您 AzureFirewallSubnet 學習您的內部部署網路透過 BGP 的預設路由，您必須使用覆寫此使用 0.0.0.0/0 UDR **NextHopType**將值設為**網際網路**維護直接網際網路連線。 根據預設，Azure 防火牆不支援在內部部署網路的強制通道。<br><br>不過，如果您的組態需要到內部部署網路的強制通道，Microsoft 將支援它以案例為基礎。 如此我們可以檢閱您的案例，請連絡支援服務。 如果接受，我們會將您的訂用帳戶的白名單，並確保能維持必要的防火牆的網際網路連線能力。|

<sup>1</sup>如果您需要增加這些限制，請連絡 Azure 支援服務。
