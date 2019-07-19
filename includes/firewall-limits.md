---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/16/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a42284765a46f4a000dc5b7fcf2867ef17d69570
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229242"
---
| Resource | 預設限制 |
| --- | --- |
| 資料輸送量 |30 Gbps<sup>1</sup> |
|規則|10000, 結合所有規則類型。|
|AzureFirewallSubnet 大小下限 |/26|
|網路和應用程式規則中的連接埠範圍|0-64,000。 我們正努力放寬這項限制。|
|公用 IP 位址|100 上限|
|路由表|根據預設, AzureFirewallSubnet 具有 0.0.0.0/0 路由, 並將 NextHopType 值設為**Internet**。<br><br>「Azure 防火牆」必須能夠直接連線到網際網路。 如果您的 AzureFirewallSubnet 透過 BGP 學習到內部部署網路的預設路由, 您必須以 0.0.0.0/0 UDR 覆寫它, 並將**NextHopType**值設為 [**網際網路**], 以維持直接的網際網路連線能力。 根據預設，Azure 防火牆不支援對內部部署網路的強制通道。<br><br>不過，如果您的設定需要對內部部署網路的強制通道，Microsoft 將以個別案例為原則提供支援。 連絡支援人員，以便我們檢閱您的案例。 若已接受, 我們將允許您的訂用帳戶, 並確保維持必要的防火牆網際網路連線能力。|

<sup>1</sup>如果您需要增加這些限制, 請聯絡 Azure 支援。
