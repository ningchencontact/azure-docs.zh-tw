---
title: 包含檔案
description: 包含檔案
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 04/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: ff6e08929d5ede86ba0632e7cfb38d81d21ca7ec
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59803567"
---
<a name="virtual-networking-limits-classic"></a>下列限制僅適用於透過每個訂用帳戶的傳統部署模型所管理的網路資源。 深入了解如何[根據您的訂用帳戶限制檢視目前資源使用量](../articles/networking/check-usage-against-limits.md)。

| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 虛擬網路 |50 |100 |
| 區域網路網站 |20 |請連絡支援人員。 |
| 每个虚拟网络的 DNS 服务器数 |20 |20 |
| 每个虚拟网络的专用 IP 地址数 |4,096 |4,096 |
| 虛擬機器或角色執行個體之每個 NIC 的並行 TCP 或 UDP 流程 |如果 NIC 至少有两个，则为 500,000（至多 1,000,000）。 |如果 NIC 至少有两个，则为 500,000（至多 1,000,000）。 |
| 網路安全性群組 (NSG) |100 |200 |
| 每个 NSG 的 NSG 规则数 |200 |1,000 |
| 用户定义路由表数 |100 |200 |
| 每个路由表的用户定义的路由数 |100 |400 |
| 公用 IP 位址 (動態) |5 |請連絡支援人員。 |
| 保留的公用 IP 位址 |20 |請連絡支援人員。 |
| 每個部署的公用 VIP |5 |請連絡支援人員。 |
| 每个部署的专用 VIP（内部负载均衡） |1 |1 |
| 终结点访问控制列表 (ACL) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>網路限制-Azure Resource Manager
下列限制僅適用於透過每個訂用帳戶每一區域的 Azure Resource Manager 所管理的網路資源。 深入了解如何[根據您的訂用帳戶限制檢視目前資源使用量](../articles/networking/check-usage-against-limits.md)。

> [!NOTE]
> 我們最近已將所有預設限制提升至其最大限制。 如果没有最大限制列，则资源没有可调整的限制。 如果过去已通过客户支持提高了这些上限，因此在以下表中看不到更新的限制，可[免费提交联机客户支持请求](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| 資源 | 預設限制 | 
| --- | --- |
| 虛擬網路 |1,000 |
| 每一虛擬網路的子網路 |3,000 |
| 每个虚拟网络的虚拟网络对等互连数 |500 |
| 每个虚拟网络的 DNS 服务器数 |20 |
| 每个虚拟网络的专用 IP 地址数 |65,536 |
| 每个网络接口的专用 IP 地址数 |256 |
| 每个虚拟机的专用 IP 地址数 |256 |
| 虛擬機器或角色執行個體之每個 NIC 的並行 TCP 或 UDP 流程 |500,000 |
| 网络接口卡数 |65,536 |
| 網路安全性群組 |5,000 |
| 每一 NSG 的 NSG 規則 |1,000 |
| 針對安全性群組中的來源或目的地所指定的 IP 位址和範圍 |4,000 |
| 應用程式安全性群組 |3,000 |
| 每個 NIC、每個 IP 組態的應用程式安全性群組 |20 |
| 每個應用程式安全性群組的 IP 組態 |4,000 |
| 可在網路安全性群組的所有安全性規則內指定的應用程式安全性群組 |100 |
| 用户定义路由表数 |200 |
| 每个路由表的用户定义的路由数 |400 |
| 每个 Azure VPN 网关的点到站点根证书数 |20 |
| 虛擬網路 TAP |100 |
| 每個虛擬網路 TAP 的網路介面 TAP 設定 |100 |

#### <a name="publicip-address"></a>公用 IP 位址限制
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 公用 IP 位址 - 動態 | 基本版为 1,000。 |請連絡支援人員。 |
| 公用 IP 位址 - 靜態 | 基本版为 1,000。 |請連絡支援人員。 |
| 公用 IP 位址 - 靜態 | 标准版为 200。|請連絡支援人員。 |
| 公用 IP 前置詞大小 （預覽） | /28 | /28 |

#### <a name="load-balancer"></a>負載平衡器限制
下列限制僅適用於透過每個訂用帳戶每一區域的 Azure Resource Manager 所管理的網路資源。 深入了解如何[根據您的訂用帳戶限制檢視目前資源使用量](../articles/networking/check-usage-against-limits.md)。

| 資源 | 預設限制 |
| --- | --- |
| 負載平衡器 | 1,000 | 
| 每個資源的規則，基本 | 250 |
| 每個資源的規則，標準 | 1,500 | 
| 每個 IP 設定的規則 | 299 |
| 每個 NIC 的規則 | 500 |
| 前端 IP 設定基本 | 200 |
| 前端 IP 設定標準 | 600 |
| 後端集區基本 | 100，單一可用性設定組 |
| 後端集區標準 | 1000，單一虛擬網路 |
| 每個負載平衡器，標準的後端資源<sup>1</sup> | 150 |
| 高可用性連接埠標準 | 每 1 個內部前端 |

<sup>1</sup>限制是最多 150 种资源，采用独立虚拟机资源、可用性集资源和虚拟机规模集资源的任意组合。

