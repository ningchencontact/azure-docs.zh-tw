---
title: Azure 虛擬網路的 IPv6 總覽 (預覽)
titlesuffix: Azure Virtual Network
description: Ipv6 端點的 IPv6 描述和 Azure 虛擬網路中的資料路徑。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 33078439e8f055d746fad9949a9b0d7651e120f7
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543817"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>什麼是適用于 Azure 虛擬網路的 IPv6？ (預覽)

適用于 Azure 虛擬網路 (VNet) 的 IPv6 可讓您在 Azure 中裝載應用程式, 並在虛擬網路內和網際網路之間使用 IPv6 和 IPv4 連線能力。 由於公用 IPv4 位址耗盡, 行動性和物聯網 (IoT) 的新網路通常是以 IPv6 為基礎。 即使是長期建立的 ISP 和行動網路也會轉換為 IPv6。 僅適用于 IPv4 的服務在現有和新興市場中都可以真正的缺點尋找自己。 雙重堆疊 IPv4/IPv6 連線能力可讓 Azure 託管的服務利用全球可用的雙堆疊服務, 輕鬆地與現有的 IPv4 和這些新的 IPv6 裝置和網路連線, 以跨越這項技術缺口。

Azure 的原始 IPv6 連線能力可讓您輕鬆地為裝載于 Azure 中的應用程式提供雙重堆疊 (IPv4/IPv6) 網際網路連線能力。 它可讓您使用負載平衡的 IPv6 連線, 針對輸入和輸出起始的連線進行簡單的 Vm 部署。 這項功能仍可供使用, 並可在[這裡](../load-balancer/load-balancer-ipv6-overview.md)取得詳細資訊。
Azure 虛擬網路的 IPv6 有更完整的功能, 可讓您在 Azure 中部署完整的 IPv6 解決方案架構。

> [!Important]
> 適用于 Azure 虛擬網路的 IPv6 目前為公開預覽版。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

下圖說明 Azure 中的簡單雙重堆疊 (IPv4/IPv6) 部署:

![IPv6 網路部署圖表](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>優點

Azure 虛擬網路 IPv6 優點:

- 協助將 Azure 託管應用程式的範圍擴充到不斷成長的行動和物聯網市場。
- 雙堆疊的 IPv4/IPv6 Vm 提供最大的服務部署彈性。 單一服務實例可以與支援 IPv4 和 IPv6 的網際網路用戶端連接。
- 建置於長時間建立、穩定的 Azure VM 對網際網路 IPv6 連線能力。
- 預設是安全的, 因為只有在您的部署中明確要求時, 才會建立網際網路的 IPv6 連線能力。

## <a name="capabilities"></a>功能

適用于 VNet 的 IPv6 包含下列功能:

- Azure 客戶可以定義自己的 IPv6 虛擬網路位址空間, 以符合其應用程式、客戶或緊密整合到其內部部署 IP 空間的需求。
- 雙重堆疊 (IPv4 和 IPv6) 具有雙重堆疊子網的虛擬網路, 可讓應用程式與虛擬網路或網際網路中的 IPv4 和 IPv6 資源連接。
    > [!IMPORTANT]
    > IPv6 的子網的大小必須完全為/64。  如果您決定啟用子網到內部部署網路的路由, 這可確保相容性, 因為某些路由器只能接受/64 IPv6 路由。  
- 使用網路安全性群組的 IPv6 規則來保護您的資源。
- 使用使用者定義的路由來自訂虛擬網路中的 IPv6 流量路由, 特別是在利用網路虛擬裝置來擴充您的應用程式時。
- 讓網際網路用戶端使用其選擇的通訊協定, 順暢地存取雙重堆疊應用程式, 並 Azure DNS IPv6 (AAAA) 記錄的支援。 
- 標準 IPv6 公開負載平衡器支援建立有彈性、可調整的應用程式，並包括：
    - 選擇性的 IPv6 健全狀況探查, 用來判斷哪個後端集區實例是健全狀況, 因此可以接收新的流程。 .  
    - 選擇性輸出規則, 可提供對輸出連線能力的完整宣告式控制, 以調整並微調此功能以滿足您的特定需求。
    - 選擇性多個前端設定, 可讓單一負載平衡器使用多個 IPv6 公用 IP 位址-相同的前端通訊協定和埠可以跨前端位址重複使用。
- 實例層級的公用 IP 可直接對個別 Vm 提供 IPv6 網際網路連線能力。
- 使用就地升級, 輕鬆地將 IPv6 連線能力新增至現有的僅 IPv4 部署。
- 建立雙重堆疊應用程式, 以使用虛擬機器擴展集自動調整為您的負載。
- 預覽版的入口網站支援包括互動式建立/編輯/刪除雙重堆疊 (IPv4 + IPv6) 虛擬網路和子網、IPv6 網路安全性群組規則、IPv6 使用者定義的路由, 以及 IPv6 公用 Ip。  

## <a name="limitations"></a>限制
Azure 虛擬網路的 IPv6 預覽版本具有下列限制:
- 適用于 Azure 虛擬網路的 IPv6 (預覽) 可在所有全球 Azure 區域中使用, 但僅限於全球 Azure (不是政府雲端)。
- Standard Load Balancer 元件的入口網站支援僅供視圖。  不過, 您可以使用 Azure Powershell 和命令列介面 (CLI) 來進行 Standard Load Balancer 部署的完整支援和檔 (含範例)。   
- 預覽版的網路監看員支援僅限於 NSG 流量記錄和網路封包捕獲。
- 預覽中不支援虛擬網路對等互連 (區域內或全域)。
- 使用標準 IPv6 外部 Load Balancer 時, 適用下列限制: 
  - 輸出規則可以參考多個前端公用 Ip, 但可能**不**會參考 IPv6 公用首碼。 IP 公用首碼僅支援 IPv4 首碼。
  - IPv6 負載平衡規則可能**不**會使用*浮動 IP*功能。 只有 IPv4 才支援在後端實例上重複使用埠。
- Azure 公用 IP 位址首碼功能不支援保留網際網路對向 IPv6 位址的區塊。

## <a name="pricing"></a>定價

IPv6 Azure 資源和頻寬的收費方式與 IPv4 的費率相同。 IPv6 沒有額外或不同的費用。 您可以在 [[公用 IP 位址](https://azure.microsoft.com/pricing/details/ip-addresses/)]、[[網路頻寬](https://azure.microsoft.com/pricing/details/bandwidth/)] 或 [ [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)] 中找到定價的詳細資料。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用 Azure PowerShell 部署 IPv6 雙重堆疊應用程式](virtual-network-ipv4-ipv6-dual-stack-powershell.md)。
- 瞭解如何[使用 Azure CLI 部署 IPv6 雙重堆疊應用程式](virtual-network-ipv4-ipv6-dual-stack-cli.md)。
