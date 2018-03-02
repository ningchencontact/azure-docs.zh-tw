---
title: "關於 Azure 中使用 Azure Site Recovery 進行 Azure 災害復原的網路功能 | Microsoft Docs"
description: "提供使用 Azure Site Recovery 複寫 Azure VM 的網路功能概觀。"
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/08/2018
ms.author: sujayt
ms.openlocfilehash: 5ce85761df4e0ad62c22a829f67464a3145fd827
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2018
---
# <a name="about-networking-in-azure-to-azure-replication"></a>關於 Azure 中進行 Azure 複寫的網路功能

>[!NOTE]
> Azure 虛擬機器的 Site Recovery 複寫目前為預覽狀態。

本文提供您使用 [Azure Site Recovery](site-recovery-overview.md)，將 Azure VM 從一個區域複寫和復原到另一個區域時的網路功能指引。 

## <a name="before-you-start"></a>開始之前

了解 Site Recovery 如何針對[這種情況](azure-to-azure-architecture.md)提供災害復原。

## <a name="typical-network-infrastructure"></a>一般網路基礎結構

下圖說明適用於 Azure VM 上執行之應用程式的一般 Azure 環境：

![客戶環境](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

如果您使用 Azure ExpressRoute 或從內部部署網路到 Azure 的 VPN 連線，環境將如下所示：

![客戶環境](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

通常會使用防火牆和網路安全性群組 (NSG) 來保護網路。 防火牆會使用以 URL 或 IP 為基礎的白名單來控制網路連線能力。 NSG 提供規則，使用 IP 位址範圍來控制網路連線能力。

>[!IMPORTANT]
> Site Recovery 不支援使用經過驗證的 Proxy 來控制網路連線能力，而且無法啟用複寫。


## <a name="outbound-connectivity-for-urls"></a>URL 的輸出連線能力

如果您使用以 URL 為基礎的防火牆 Proxy 來控制輸出連線能力，請允許這些 Site Recovery URL：


**URL** | **詳細資料**  
--- | ---
*.blob.core.windows.net | 需要此項目方可從 VM 將資料寫入來源地區的快取儲存體帳戶中。
login.microsoftonline.com | 需要此項目方可進行 Site Recovery 服務 URL 的授權和驗證。
*.hypervrecoverymanager.windowsazure.com | 需要此項目方可從 VM 進行 Site Recovery 服務通訊。
*.servicebus.windows.net | 需要此項目方可從 VM 寫入 Site Recovery 監視和診斷資料。

## <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 位址範圍的輸出連線能力

如果您使用以 IP 為基礎的防火牆 Proxy 或 NSG 規則來控制輸出連線能力，就必須允許這些 IP 範圍。

- 對應於來源位置的所有 IP 位址範圍。
    - 您可以下載 [IP 位址範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653) \(英文\)。
    - 您需要允許這些位址，方可從 VM 將該資料寫入到快取儲存體帳戶。
- 對應至 Office 365 [驗證與身分識別 IP V4 端點](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)的所有 IP 位址範圍。
    - 如果未來將新的位址新增至 Office 365 範圍，您必須建立新的 NSG 規則。
- Site Recovery 服務端點 IP 位址。 您可以在 [XML 檔案](https://aka.ms/site-recovery-public-ips)中找到這些位址，而其相依於您的目標位置。
-  您可以[下載並使用此指令碼](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) \(英文\)，在 NSG 上自動建立所需的規則。 
- 建議您在測試 NSG 上建立必要的 NSG 規則，並確認沒有問題後，再於生產 NSG 上建立規則。
- 若要建立所需的 NSG 規則數目，請確定您的訂用帳戶位於允許清單中。 請連絡 Azure 支援人員，以提高您訂用帳戶中的 NSG 規則限制。

IP 位址範圍如下：

>
   **目標** | **Site Recovery IP** |  **Site Recovery 監視 IP**
   --- | --- | ---
   東亞 | 52.175.17.132 | 13.94.47.61
   東南亞 | 52.187.58.193 | 13.76.179.223
   印度中部 | 52.172.187.37 | 104.211.98.185
   印度南部 | 52.172.46.220 | 104.211.224.190
   美國中北部 | 23.96.195.247 | 168.62.249.226
   北歐 | 40.69.212.238 | 52.169.18.8
   西歐 | 52.166.13.64 | 40.68.93.145
   美國東部 | 13.82.88.226 | 104.45.147.24
   美國西部 | 40.83.179.48 | 104.40.26.199
   美國中南部 | 13.84.148.14 | 104.210.146.250
   美國中部 | 40.69.144.231 | 52.165.34.144
   美國東部 2 | 52.184.158.163 | 40.79.44.59
   日本東部 | 52.185.150.140 | 138.91.1.105
   日本西部 | 52.175.146.69 | 138.91.17.38
   巴西南部 | 191.234.185.172 | 23.97.97.36
   澳洲東部 | 104.210.113.114 | 191.239.64.144
   澳大利亞東南部 | 13.70.159.158 | 191.239.160.45
   加拿大中部 | 52.228.36.192 | 40.85.226.62
   加拿大東部 | 52.229.125.98 | 40.86.225.142
   美國中西部 | 52.161.20.168 | 13.78.149.209
   美國西部 2 | 52.183.45.166 | 13.66.228.204
   英國西部 | 51.141.3.203 | 51.141.14.113
   英國南部 | 51.140.43.158 | 51.140.189.52
   英國南部 2 | 13.87.37.4| 13.87.34.139
   英國北部 | 51.142.209.167 | 13.87.102.68
   韓國中部 | 52.231.28.253 | 52.231.32.85
   韓國南部 | 52.231.298.185 | 52.231.200.144
   
   
  

## <a name="example-nsg-configuration"></a>範例 NSG 設定

這個範例示範如何針對要複寫的 VM 設定 NSG 規則。 

- 如果您使用 NSG 規則來控制輸出連線能力，請針對所有必要的 IP 位址範圍使用「允許 HTTPS 輸出」規則。
- 此範例假設 VM 來源位置為「美國東部」，而目標位置為「美國中部」。

### <a name="nsg-rules---east-us"></a>NSG 規則：美國東部

1. 建立對應至[美國東部 IP 位址範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653) \(英文\) 的規則。 需要此項目方可從 VM 將資料寫入快取儲存體帳戶中。
2. 對於對應至 Office 365 [驗證和身分識別 IP V4 端點](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)的所有 IP 位址範圍建立規則。
3. 建立對應於目標位置的規則：

   **位置** | **Site Recovery 位址** |  **Site Recovery 監視 IP 位址**
    --- | --- | ---
   美國中部 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG 規則：美國中部 

需要這些規則，才能啟用從目標區域到來源地區容錯移轉後的複寫：

* 對應於[美國中部 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)的規則。 需要此項目方可從 VM 將資料寫入快取儲存體帳戶中。

* 針對對應於 Office 365 [驗證和身分識別 IP V4 端點](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)的所有 IP 範圍規則。

* 對應於來源位置的規則：
    - 美國東部
    - Site Recovery IP 位址：13.82.88.226
    - Site Recovery 監視 IP 位址：104.45.147.24


## <a name="expressroutevpn"></a>ExpressRoute/VPN 

如果您在內部部署與 Azure 位置之間具有 ExpressRoute 或 VPN 連線，請遵循本節中的指引。

### <a name="forced-tunneling"></a>強制通道

通常，您會定義預設路由 (0.0.0.0/0)，強制輸出網際網路流量流經內部部署位置。 我們不建議這麼做。 複寫流量及 Site Recovery 服務通訊不應該留下 Azure 界限。 解決方案是針對[這些 IP 範圍](#outbound-connectivity-for-azure-site-recovery-ip-ranges)新增使用者定義的路由 (UDR)，以讓複寫流量不會在內部部署傳送。

### <a name="connectivity"></a>連線能力 

對於目標位置與內部部署位置之間的連線，請遵循下列指導方針：
- 如果您的應用程式需要連線到內部部署機器，或者，如果有用戶端透過 VPN/ExpressRoute 連線到應用程式，請確定目標 Azure 區域與內部部署資料中心之間至少有[網站間連線](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)。

- 如果您預期目標 Azure 區域與內部部署資料中心之間有許多流量，應該在目標 Azure 區域與內部部署資料中心之間建立另一個 [ExpressRoute 連線](../expressroute/expressroute-introduction.md)。

- 如果您想要對於容錯移轉後的虛擬機器保留 IP，請將目標區域的網站間/ExpressRoute 連線維持在中斷連線的狀態。 這是為了確定來源區域的 IP 範圍和目標區域的 IP 範圍之間沒有範圍衝突。

### <a name="expressroute-configuration"></a>ExpressRoute 設定
請遵循 ExpressRoute 組態的下列最佳做法：

- 您需要在來源和目標區域中建立 ExpressRoute 線路。 然後，您需要在下列兩者之間建立連線：
  - 來源虛擬網路與 ExpressRoute 線路。
  - 目標虛擬網路與 ExpressRoute 線路。

- 按照 ExpressRoute 標準，您可以在同一個地理政治區域中建立線路。 若要在不同的地理政治區域建立 ExpressRoute 線路，需要 Azure ExpressRoute Premium，這需要增加成本。 (如果您已經使用 ExpressRoute Premium，則不需要額外的成本。)如需詳細資訊，請參閱 [ExpressRoute 位置文件](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region)和 [ExpressRoute 定價](https://azure.microsoft.com/pricing/details/expressroute/)。

- 建議您在來源和目標區域中使用不同的 IP 範圍。 ExpressRoute 線路無法同時連線相同 IP 範圍的兩個 Azure 虛擬網路。

- 您可以在兩個地區建立相同 IP 範圍的虛擬網路，並在兩個區域中建立 ExpressRoute 線路。 發生容錯移轉事件時，中斷來源虛擬網路的線路，然後連線目標的虛擬網路中的線路。

 >[!IMPORTANT]
 > 如果主要區域已完全關閉，中斷連線作業可能會失敗。 這可讓目標虛擬網路無法取得 ExpressRoute 連線能力。

## <a name="next-steps"></a>後續步驟
[複寫 Azure 虛擬機器](site-recovery-azure-to-azure.md)來開始保護您的工作負載。
