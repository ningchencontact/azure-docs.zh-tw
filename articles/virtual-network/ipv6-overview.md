---
title: Azure 虛擬網路 （預覽） 的 IPv6 概觀
titlesuffix: Azure Virtual Network
description: IPv6 的 Azure 虛擬網路中的 IPv6 端點和資料路徑的描述。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130877"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>什麼是 Azure 虛擬網路的 IPv6？ (預覽)

IPv6 的 Azure 虛擬網路 (VNET) 可讓您能夠在 Azure 中裝載應用程式，同時在虛擬網路內和至網際網路上往返的 IPv6 和 IPv4 連線。 公用 IPv4 位址耗盡，因為通常是在 IPv6 上建置行動和物聯網 (IoT) 的新網路。 甚至是長時間建立 ISP 和行動裝置的網路會被轉換為 IPv6。 僅支援 IPv4 的服務可以發現自己在實際的缺點現有與新興市場。 雙重堆疊 IPv4/IPv6 連線功能可讓 Azure 託管服務周遊此全球通用、 雙重堆疊輕易地與現有 IPv4 與這些新的 IPv6 裝置和網路連線的服務使用的技術落差。

Azure 的原始 IPv6 連線能力可讓您輕鬆地在 Azure 中裝載的應用程式提供雙重堆疊 (IPv4/IPv6) 網際網路連線。 它可讓簡單的部署 Vm 的輸入和輸出的初始連線的負載平衡 IPv6 連線。 這項功能仍然可用，並提供更多資訊[此處](../load-balancer/load-balancer-ipv6-overview.md)。
Azure 虛擬網路的 IPv6 是更完整精選-啟用在 Azure 中部署完整的 IPv6 解決方案架構。

> [!Important]
> Azure 虛擬網路的 IPv6 目前處於公開預覽狀態。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

下圖說明簡單的雙重堆疊 (IPv4/IPv6) 部署在 Azure 中：

![IPv6 網路部署圖表](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>優點

Azure 虛擬網路的 IPv6 優點：

- 可協助展開您的 Azure 裝載的應用程式持續成長的行動和物聯網市場的範圍。
- 雙重堆疊的 IPv4/IPv6 Vm 提供最高的服務部署的彈性。 單一服務執行個體可以使用 IPv4 和 IPv6 的網際網路用戶端連線。
- 根據長時間建立的穩定 Azure VM-到-網際網路 IPv6 連線。
- 預設保護，因為明確地在您的部署要求時才建立 IPv6 連線到網際網路。

## <a name="capabilities"></a>功能

IPv6 支援的虛擬機器包括下列功能：

- Azure 客戶可以定義自己 IPv6 虛擬網路位址空間以符合其應用程式，客戶的需求，或完美地整合到其內部部署 IP 空間。
- 雙重堆疊 （IPv4 和 IPv6） 虛擬網路具有雙重堆疊子網路可讓應用程式與 IPv4 和 IPv6 的資源，在他們的虛擬網路或網際網路連線。
- 網路安全性群組保護您的資源具有 IPv6 規則
- 自訂路由的 IPv6 流量在虛擬網路與使用者定義的路由-尤其是利用網路虛擬設備來增強您的應用程式。
- IPv6 負載平衡器支援，以建立彈性、 可調整的應用程式，其中包括 Azure DNS 支援 AAAA 記錄的 IPv6 公用 ip。
- 使用就地升級現有的僅 IPv4 部署，輕鬆地新增 IPv6 連線。
- 建立自動調整與虛擬機器擴展集負載的雙重堆疊應用程式。

## <a name="limitations"></a>限制
Azure 虛擬網路的 IPv6 的預覽版本具有下列限制：
- IPv6，Azure 虛擬網路 （預覽） 的所有全域 Azure 區域，但僅限全域 Azure-位於 government 雲端。   
- 預覽入口網站的支援僅限於檢視只適用於多，但並非所有的 IPv6 設定，但虛擬網路的 IPv6 已完整支援，以及 （含範例） 的文件使用 Azure Powershell 和命令列介面 (CLI) 的 IPv6 部署。
- 預覽版網路監看員支援僅限於 NSG 流量記錄並擷取網路封包。
- 一開始就只能在基本 Load Balancer 預覽版的負載平衡支援。
- 執行個體層級公用 Ip (直接在 VM 上的公用 Ip) 不支援預覽。  
- 虛擬網路對等互連 （也出現些許或全域） 不支援預覽。 

## <a name="pricing"></a>價格

IPv6 的 Azure 資源及頻寬的費用依 IPv4 相同費率收費。 沒有任何其他或不同的費用，適用於 IPv6。 您可以找到有關定價的詳細資料[公用 IP 位址](https://azure.microsoft.com/pricing/details/ip-addresses/)，[網路頻寬](https://azure.microsoft.com/pricing/details/bandwidth/)，或[Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)。

## <a name="next-steps"></a>後續步驟

- 了解如何[部署 IPv6 雙重堆疊應用程式使用 Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md)。
- 了解如何[部署 IPv6 雙重堆疊應用程式使用 Azure CLI](virtual-network-ipv4-ipv6-dual-stack-cli.md)。
