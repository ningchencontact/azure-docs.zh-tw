---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 7d905550114bb76a0a091146b3972bab4a652022
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429935"
---
| 資源 | 預設限制 |
| --- | --- |
| 已處理的資料量 |1000 TB/防火牆/月 <sup>1</sup> |
|規則|10k - 結合所有規則類型|
|全域對等互連|不支援。 您應該在每個區域都至少部署一個防火牆。|
|單一網路規則中的最大連接埠|15<br>連接埠範圍 (例如：2 - 10) 會計算為兩個。
|AzureFirewallSubnet 大小下限 |/26|
|網路和應用程式規則中的連接埠範圍|0-64,000。 我們正努力放寬這項限制。|
|


<sup>1</sup> 如需增加這些限制，請連絡 Azure 支援中心。
