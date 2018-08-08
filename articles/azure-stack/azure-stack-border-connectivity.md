---
title: Azure Stack 整合式系統的邊界連線能力網路整合考量 | Microsoft Docs
description: 了解您可以執行哪些工作，來為資料中心邊界網路與多節點 Azure Stack 的連線做規劃。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 260c58ad9099a4532c8a6558cfcf5c13f0fc8d52
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282003"
---
# <a name="border-connectivity"></a>界限連線能力 
網路整合規劃是成功的 Azure Stack 整合式系統部署、作業及管理的重要先決條件。 邊界連線能力規劃是從選擇是否要搭配邊界閘道協定 (BGP) 使用動態路由開始。 這需要指派 16 位元 BGP 自治號碼 (公用或私人)，或使用靜態路由，這會為邊界裝置指派一個靜態預設路由。

> [!IMPORTANT]
> Top-of-Rack (TOR) 交換器需要實體介面上已設定具有點對點 IP (/30 網路) 的第 3 層上行連結。 支援 Azure Stack 作業的 TOR 交換器不支援搭配使用第 2 層上行連結。 

## <a name="bgp-routing"></a>BGP 路由
使用動態路由通訊協定 (例如 BGP) 可保證系統一律能夠察覺網路變更，而有助於系統管理。 

如下圖所示，在 TOR 交換器上會使用首碼清單來限制公告私人 IP 空間。 首碼清單會拒絕私人 IP 子網路，並在 TOR 與邊界之間的連線上套用該子網路作為路由對應。

在 Azure Stack 解決方案內執行的「軟體負載平衡器」(SLB) 會與 TOR 裝置對等互連，因此可以動態公告 VIP 位址。

為了確保使用者流量以立即且在背景執行的方式從失敗中復原，在 TOR 裝置之間設定的 VPC 或 MLAG 會允許使用對主機和 HSRP 或 VRRP 的多機箱鏈路聚合 (multi-chassis link aggregation)，來為 IP 網路提供網路備援。

![BGP 路由](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>靜態路由
靜態路由需要對邊界裝置進行額外的設定。 其需要更多的手動操作和管理以及徹底的分析，然後才能進行變更，而且視所進行的變更而定，設定錯誤時所造成的問題可能需要更長的時間才能復原。 這不是建議的路由方法，但也受到支援。

若要將 Azure Stack 整合到使用靜態路由的網路環境，邊界和 TOR 裝置之間的四個實體連結全都必須連接，而且靜態路由的運作方式會導致無法保證高可用性。

邊界裝置在設定時，必須針對目的地為外部網路或公用 VIP 和基礎結構網路的流量，將靜態路由指向 TOR 裝置 P2P。 為了部署，其需要通往 BMC 網路的靜態路由。 客戶可以選擇讓靜態路由留在邊界，以存取某些位於 BMC 網路上的資源。  客戶可以選擇將靜態路由新增至「交換器基礎結構」和「交換器管理」網路。

TOR 裝置上預先設定了一個會將所有流量傳送到邊界裝置的靜態預設路由。 此預設規則有一個針對私人空間的流量例外，使用會對邊界連線的 TOR 套用的「存取控制清單」即可封鎖此空間。

靜態路由只會套用於 TOR 和邊界交換器之間的上行連結。 在機架內會使用 BGP 動態路由，原因是它是 SLB 及其他元件不可或缺的工具，並無法停用或移除。

![靜態路由](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>Transparent Proxy
如果資料中心要求所有流量都使用 Proxy，您就必須設定 *Transparent Proxy* 以處理來自機架 (Rack) 的所有流量，進而根據原則進行處理，將您網路上各區域間的流量加以區分。

> [!IMPORTANT]
> Azure Stack 解決方案不支援一般 Web Proxy。  

Transparent Proxy (也稱為攔截、內嵌或強制 Proxy) 會攔截網路層上的一般通訊，而不需要任何特殊的用戶端設定。 用戶端不需要留意 Proxy 的存在。

![Transparent Proxy](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>後續步驟
[DNS 整合](azure-stack-integrate-dns.md)