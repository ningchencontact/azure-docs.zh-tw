---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 2/4/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 9097f2b70a80431cf3302555ad2c835338cf8d8e
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736004"
---
| 資源 | 預設限制 |
| --- | --- |
| 已處理的資料量 |1000 TB/防火牆/月 <sup>1</sup> |
|規則|10k - 結合所有規則類型|
|全域對等互連|不支援。 您應該在每個區域都至少部署一個防火牆。|
|AzureFirewallSubnet 大小下限 |/26|
|網路和應用程式規則中的連接埠範圍|0-64,000。 我們正努力放寬這項限制。|
|


<sup>1</sup> 如需增加這些限制，請連絡 Azure 支援中心。
