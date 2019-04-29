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
ms.openlocfilehash: b050d0cd5f6a21757685f9bc0991f8ce0a971e3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456414"
---
如果您無法透過 VPN 連線與虛擬機器連線，請檢查下列各項：

- 验证 VPN 连接是否成功。
- 確認您是連線至 VM 的私人 IP 位址。
- 如果您可以使用私人 IP 位址 (而非電腦名稱) 來連線至 VM，請確認您已正確設定 DNS。 若要详细了解如何对 VM 进行名称解析，请参阅[针对 VM 的名称解析](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

當您透過點對站連線時，請檢查下列其他項目︰

- 請使用 'ipconfig' 來檢查指派給所連線電腦上的乙太網路介面卡之 IPv4 位址。 如果该 IP 地址位于要连接到的 VNet 的地址范围内，或者位于 VPNClientAddressPool 的地址范围内，则称为地址空间重叠。 当地址空间以这种方式重叠时，网络流量不会抵达 Azure，而是呆在本地网络中。
- 验证是否在为 VNet 指定 DNS 服务器 IP 地址之后，才生成 VPN 客户端配置包。 如果您已更新 DNS 伺服器 IP 位址，請產生並安裝新的 VPN 用戶端設定套件。

如需針對 RDP 連線進行疑難排解的詳細資訊，請參閱[針對 VM 的遠端桌面連線進行疑難排解](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)。