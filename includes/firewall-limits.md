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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174354"
---
| 資源 | 預設限制 |
| --- | --- |
| 資料輸送量 |30 Gbps<sup>1</sup> |
|規則|10000，有些規則類型結合。|
|AzureFirewallSubnet 大小下限 |/26|
|網路和應用程式規則中的連接埠範圍|0-64,000。 我們正努力放寬這項限制。|
|路由表|根據預設，AzureFirewallSubnet 具有 0.0.0.0/0 路由的 NextHopType 值設為**網際網路**。<br><br>「Azure 防火牆」必須能夠直接連線到網際網路。 如果您的 AzureFirewallSubnet 學習到透過 BGP 連至您內部部署網路的預設路由，您必須將其覆寫為 0.0.0.0/0 UDR，且 **NextHopType** 值必須設為 [網際網路]  ，以保有直接網際網路連線。 根據預設，Azure 防火牆不支援對內部部署網路的強制通道。<br><br>不過，如果您的設定需要對內部部署網路的強制通道，Microsoft 將以個別案例為原則提供支援。 連絡支援人員，以便我們檢閱您的案例。 受理後，我們會將您的訂用帳戶列入白名單，並確實維持必要的防火牆網際網路連線。|

<sup>1</sup>如果您需要增加這些限制，請連絡 Azure 支援服務。
