---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "64859082"
---
* **PolicyBased：** PolicyBased VPN 以前在经典部署模型中称为“静态路由网关”。 原則式 VPN 會根據使用內部部署網路與 Azure VNet 之間的位址首碼組合所設定的 IPsec 原則，透過 IPsec 通道加密和導向封包。 原則 (或流量選取器) 通常定義為 VPN 裝置組態中的存取清單。 原則式 VPN 類型的值是 PolicyBased 。 使用 PolicyBased VPN，請記住下列限制︰
  
  * PolicyBased VPN「只有」  在「基本」閘道 SKU 上才能使用。 這個 VPN 類型與其他閘道 SKU 不相容。
  * 使用 PolicyBased VPN 時，您只能有 1 個通道。
  * 您只能將 PolicyBased VPN 用於 S2S 連線，而且僅限用於特定組態。 大多數「VPN 閘道」組態都需要一個 RouteBased VPN。
* **RouteBased**：RouteBased VPN 以前在经典部署模型中称为“动态路由网关”。 路由式 Vpn 會使用 IP 轉送或路由表中的「路由」，直接封包至其對應的通道介面。 然後，通道介面會加密或解密輸入和輸出通道的封包。 路由式 VPN 的原則或流量選取器會設定為任何對任何 (或萬用字元)。 路由式 VPN 類型的值是 RouteBased 。