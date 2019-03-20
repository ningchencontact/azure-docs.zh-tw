---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/14/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 3e6bde63c5f759ec711b243fc25fa0ed8bde634a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58016492"
---
| 資源 | 預設限制 |
| --- | --- |
| 資料輸送量 |30 Gbps<sup>1</sup> |
|規則|10000，有些規則類型結合。|
|全域對等互連|不支援。 您應該在每個區域都至少部署一個防火牆。|
|AzureFirewallSubnet 大小下限 |/26|
|網路和應用程式規則中的連接埠範圍|0-64,000。 我們正努力放寬這項限制。|
|


<sup>1</sup>如果您需要增加這些限制，請連絡 Azure 支援服務。
