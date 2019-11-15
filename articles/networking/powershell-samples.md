---
title: Azure PowerShell 範例-網路
description: Azure PowerShell 範例
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: fb4b63d8329f0a175252b819653e192c746489b6
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092026"
---
# <a name="azure-powershell-samples-for-networking"></a>Azure PowerShell 網路範例

下表包含使用 Azure PowerShell 所建置指令碼的連結。

| | |
|-|-|
|**Azure 資源之間的連線**||
| [建立多層式應用程式的虛擬網路](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | 建立具有前端和後端子網路的虛擬網路。 傳送到前端子網路的流量會限制為 HTTP，而傳送到後端子網路的流量則限制為 SQL 且連接埠為 1433。 |
| [對等互連兩個虛擬網路](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | 在相同區域中建立和連線兩個虛擬網路。 |
| [透過網路虛擬設備路由傳送流量](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | 建立具有前端和後端子網路的虛擬網路，以及可在兩個子網路間路由傳送流量的 VM。 |
| [篩選輸入和輸出 VM 網路流量](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | 建立具有前端和後端子網路的虛擬網路。 傳送到前端子網路的輸入網路流量限制為 HTTP 和 HTTPS。 不允許從後端子網路傳輸至網際網路的輸出流量。 |
|**負載平衡和流量方向**||
| [使用 VM 平衡流量負載以達到高可用性](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | 依據高可用性和負載平衡組態建立數個虛擬機器。 |
| [在 VM 上平衡多個網站的負載](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | 建立具有多個 IP 設定的兩個 VM。這兩個 VM 會加入 Azure 可用性設定組，並可透過 Azure Load Balancer 來存取。 |
| [跨多個區域導向流量以達到高應用程式可用性](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  建立兩個 App Service 方案、兩個 Web 應用程式、一個流量管理員設定檔和兩個流量管理員端點。 |
| | |
