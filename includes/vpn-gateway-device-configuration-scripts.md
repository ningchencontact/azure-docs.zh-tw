---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9cdb608505e594e0020eb33abc869c6bf4b6b263
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
| **廠商** | **裝置系列** | **韌體版本** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (預覽)|
|Cisco | ASA | 適用於版本低於 9.8 之 ASA 的 ASA (*) RouteBased (IKEv2 - 沒有 BGP) |
|Cisco | ASA | 適用於 9.8 版以上之 ASA 的 ASA RouteBased (IKEv2 - 沒有 BGP) |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) 必要：NarrowAzureTrafficSelectors 和 CustomAzurePolicies (IKE/IPsec)
>