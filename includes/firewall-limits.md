---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: f1fd32fc02bee47d229093680166e6a13a5ba721
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2018
ms.locfileid: "49458819"
---
| 資源 | 預設限制 |
| --- | --- |
| 已處理的資料量 |1000 TB/防火牆/月 <sup>1</sup> |
|規則|10k - 結合所有規則類型|
|VNet 對等互連|針對中樞和輪輻實作，上限為 50 個輪輻 VNET。|
|全域對等互連|不支援。 您應該在每個區域都至少部署一個防火牆。|
|單一網路規則中的最大連接埠|15<br>連接埠範圍 (例如：2 - 10) 會計算為兩個。


<sup>1</sup> 如需增加這些限制，請連絡 Azure 支援中心。
