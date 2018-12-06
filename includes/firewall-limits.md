---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/26/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8b236bc1f0089b89aca90e7c69e9b445b01a374b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440020"
---
| 資源 | 預設限制 |
| --- | --- |
| 已處理的資料量 |1000 TB/防火牆/月 <sup>1</sup> |
|規則|10k - 結合所有規則類型|
|全域對等互連|不支援。 您應該在每個區域都至少部署一個防火牆。|
|單一網路規則中的最大連接埠|15<br>連接埠範圍 (例如：2 - 10) 會計算為兩個。
|AzureFirewallSubnet 大小下限 |/26


<sup>1</sup> 如需增加這些限制，請連絡 Azure 支援中心。
