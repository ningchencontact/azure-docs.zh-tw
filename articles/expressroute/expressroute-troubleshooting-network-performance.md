---
title: "Azure 虛擬網路效能的疑難排解 |Microsoft 文件"
description: "此頁面提供的測試 Azure 網路連結效能標準化的方法。"
services: expressroute
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2017
ms.author: jonor
ms.openlocfilehash: 56f011632a2aa3ef0632efd5ace472c0fc79a329
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="troubleshooting-network-performance"></a>網路效能的疑難排解
## <a name="overview"></a>概觀
Azure 提供穩定且快速的方式，從您的內部部署網路連接到 Azure。 等站台對站 VPN 的方法和 ExpressRoute 成功用於大型和小型的客戶在 Azure 中執行其企業。 但當效能不符合您預期的情況下或先前的經驗時，會發生什麼事？ 這份文件可協助您的特定環境標準化您測試的方式和基準。

本文件說明如何輕鬆且一致地測試網路延遲和兩部主機之間的頻寬。 這份文件也提供一些建議的方式來查看 Azure 網路，並且幫助您隔離問題點。 PowerShell 指令碼和討論的工具需要 （任一端的測試良好與否的連結） 網路上的兩部主機。 一部主機都必須是 Windows Server 或桌面，另可為 Windows 或 Linux。 

>[!NOTE]
>疑難排解的方法、 工具和使用的方法是個人的喜好設定。 本文件說明通常需要的方法和工具。 可能會因您的方法，並沒有任何問題使用不同的方法來解決問題。 不過，如果您沒有建立的方法，您可以使用這份文件取得要建置您自己的方法、 工具和疑難排解網路問題的喜好設定的路徑上啟動。
>
>

## <a name="network-components"></a>網路元件
之前深入疑難排解，讓我們來討論一些常見的詞彙和元件。 本討論內容可確保我們正在考慮每個元件，可讓在 Azure 中的連線端對端鏈結中。
[![1]][1]

在最高的層級，說明三種主要的網路路由網域。

- Azure 網路 （在右邊的藍色雲端）
- 網際網路或 WAN （中央綠色雲端）
- 公司網路 （在左側 peach 雲端）

由右至左查看圖表，讓我們來討論簡短的每個元件：
 - **虛擬機器**-伺服器可能有多個 Nic，確保任何靜態路由，預設路由和作業系統設定正在傳送並接收流量的方式，您認為是。 此外，每個 VM SKU 都有頻寬限制。 如果您使用較小的 VM SKU，您的流量會受到至 nic。 可用的頻寬 通常使用 DS5v2 測試 （然後一次已完成測試，以節省成本的 delete） 以確保有足夠的頻寬，在 VM。
 - **NIC** -請確定您知道有問題的 NIC 指派私人 IP。
 - **NIC 的 NSG** -那里可能會在 NIC 層級套用特定 Nsg，確定 NSG 規則集是否適用於您想要傳送的流量。 比方說，確保 iPerf 的 5201 連接埠 3389 rdp，或 ssh 22 abierto para permitir las 測試流量通過。
 - **VNet 子網路**-NIC 會指派給特定子網路，請確定您知道哪些一個與規則相關聯的子網路。
 - **子網路 NSG** -NIC，Nsg 可套用於子網路也一樣。 確定 NSG 規則集適用於您想要傳送的流量。 （流量輸入至 NIC 的子網路，NSG 會套用第一次，然後 NIC 的 nsg 關聯，相反地從 VM 的輸出流量 NIC NSG 會套用第一次然後子網路 NSG 派上用場）。
 - **子網路 UDR** -使用者定義的路由可以將中繼的躍點 （例如防火牆或負載平衡器） 的流量導向。 請確定您知道是否有 UDR 中位置，以讓您的流量，如果因此何處的下一個躍點將會執行到您的流量。 （比方說，防火牆可能將一些流量和拒絕其他相同的兩部主機之間的流量）。
 - **閘道子網路 / NSG / UDR** -一樣的 VM 子網路，閘道子網路可以擁有 Nsg 和 UDRs。 請確定您知道是否要有什麼效果他們對您的流量。
 - **VNet 閘道 (ExpressRoute)** -對等 (ExpressRoute) 或 VPN 啟用之後，不會影響的許多設定如何或是否流量路由。 如果您有多個 ExpressRoute 電路或 VPN 通道連線至相同的 VNet 閘道，您應該了解連線權數設定為這個設定會影響連線喜好設定，而且會影響您的流量所採用的路徑。
 - **路由篩選**（未顯示）-路由篩選器只適用於 Microsoft 對等互連 expressroute，但請務必核取 是否您未看見您預期的路由上 Microsoft 對等互連。 

此時，您的 WAN 連結部份。 這個路由網域可以是您的服務提供者、 您公司的 WAN 或網際網路。 多躍點、 技術和公司使用這些連結可以讓有點難以進行疑難排解。 通常，您會出 Azure 和您的公司網路的規則先前跳至這個集合的公司和躍點。

在上圖中，最左邊是您的公司網路。 取決於您公司的規模、 此路由網域可以是幾個您與 WAN 或多個圖層的校園/企業網路中的裝置之間的網路裝置。

它提供了下列三種不同高層級網路環境的複雜性，通常是最佳的作法是開始邊緣並再試一次顯示在效能很好的而且會降低的位置。 這種方法可協助識別問題的三個路由網域，然後焦點放在該特定環境排解疑難。

## <a name="tools"></a>工具
大部分的網路問題可以分析和隔離使用 ping 和追蹤路由等基本工具。 很少，您必須為 Wireshark 類似的封包分析深度移。 為了協助進行疑難排解，Azure 連接性工具組 (AzureCT) 所開發，這些工具置於簡單的封裝。 進行效能測試，我要使用 iPerf 和 PSPing。 iPerf 是常用的工具，並在大部分的作業系統上執行。 iPerf 適用於基本效能測試，是相當容易使用。 PSPing 是 SysInternals 所開發的 ping 工具。 PSPing 是簡單的方式，來執行 ICMP 和 TCP ping 一個也更容易使用的命令。 這兩種工具是輕量且 「 安裝 」 只要處理主機上的目錄檔案。

所有這些工具和方法的已包裝成 PowerShell 模組 (AzureCT)，您可以安裝和使用。

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT-Azure 連接性工具組
AzureCT PowerShell 模組有兩個元件[可用性測試][ Availability Doc]和[效能測試][Performance Doc]。 這份文件只注重效能測試，因此可讓專注於此 PowerShell 模組中的兩個連結效能命令。

有三個基本步驟，以用於效能測試中使用此工具組。 1） 安裝的 PowerShell 模組，2） 安裝支援的應用程式 iPerf 和 PSPing 3） 執行效能測試。

1. 安裝 PowerShell 模組

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    此命令會下載 PowerShell 模組，並將它安裝在本機。

2. 安裝支援的應用程式
    ```powershell
    Install-LinkPerformance
    ```
    此 AzureCT 命令安裝 iPerf 和 PSPing"C:\ACTTools"的新目錄中時，它也會開啟 Windows 防火牆連接埠以允許 ICMP 和連接埠 5201 (iPerf) 流量。

3. 執行效能測試

    首先，遠端主機上必須安裝並執行 iPerf 伺服器模式。 也請確定遠端主機接聽任一 3389 (RDP Windows) 或 22 (適用於 Linux SSH) 和允許流量在連接埠的 iPerf 5201。 如果遠端主機是 windows 安裝 AzureCT 和執行安裝 LinkPerformance 命令來設定 iPerf 以及已成功在伺服器模式下啟動 iPerf 所需的防火牆規則。 
    
    遠端電腦準備就緒之後，本機電腦上開啟 PowerShell 並啟動測試：
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    此命令會執行一系列的並行載入和延遲的測試，以協助您預估的頻寬容量和延遲的網路連結。

4. 檢閱測試的輸出

    PowerShell 的輸出格式如下所示：

    [![4]][4]

    所有 iPerf 和 PSPing 測試的詳細的結果是以個別文字的目錄中檔案 AzureCT 工具在 「 C:\ACTTools。 」

## <a name="troubleshooting"></a>疑難排解
如果未提供的效能測試您預期的結果，了解為什麼應該漸進式逐步程序。 在路徑中指定的元件數目，系統化的方法通常會提供比浪費與潛在不必要地執行相同的測試多次解析的快速路徑。

>[!NOTE]
>以下的案例是效能問題，不是連線問題。 步驟會不同，如果未完全傳遞流量。
>
>

首先，是假設的挑戰。 是您預期的情況下合理的？ 比方說，如果您有 1 Gbps ExpressRoute 電路和 100 毫秒是流量的延遲的不合理的 TCP 的效能特性，透過高延遲連結完整 1 Gbps。 請參閱[參考區段](#references)的多個在效能假設。

我接下來，建議之間路由網域的邊緣處開始，並嘗試將問題隔離到單一的主要路由網域。公司網路、 WAN 或 Azure 網路。 人們通常時 blaming 黑箱指責 「 黑色方塊 」 在路徑中，很容易執行，它可能會大幅延遲解析特別是如果問題是實際的區域中，您可以進行變更。 請確定您執行您審慎再交由服務提供者或 ISP。

一旦您已經識別了可能包含問題的主要路由網域，您應該建立區域的圖表有問題。 在白板、 「 記事本 」 或以圖表的 Visio 上提供的具體 「 戰鬥對應 」 允許有系統的方法，若要進一步找出問題。 您可以計劃的測試點，並清除 區域或在測試進行發掘時更新對應。

有一個圖表之後，開始將網路分成區段並縮小問題。 了解其運作位置，以及不是其中。 保留移動以隔離造成問題的元件向您測試點。

此外，別忘了查看 OSI 模型的其他圖層。 所以可以輕鬆地專注於網路及層級 1-3 （實體、 資料和網路層級），但問題也最長可能會在 7 層應用程式層。 保持開啟的事項，並確認假設。

## <a name="advanced-expressroute-troubleshooting"></a>進階 ExpressRoute 疑難排解
如果您不確定的邊緣雲端實際上是，隔離 Azure 元件可以是一項挑戰。 使用 ExpressRoute 時，邊緣是名為 Microsoft Enterprise Edge (MSEE) 的網路元件。 **使用 ExpressRoute 時**，MSEE 是連絡人到 Microsoft 的網路和最後一個躍點離開 Microsoft 網路的第一個點。 當您建立 VNet 閘道與 ExpressRoute 循環之間的連接物件時，您實際上正在連接 MSEE。 辨識 MSEE （視方向而定好） 的第一個或最後一個躍點很重要隔離至 Azure 網路問題時證明您是在 Azure 中的問題，或下游進一步 WAN 或公司網路中。 

[![2]][2]

>[!NOTE]
> 請注意，MSEE 未出現在 Azure 的雲端。 ExpressRoute 實際上是實際上不會在 Azure 中的 Microsoft 網路的邊緣。 一旦您與 MSEE 的 ExpressRoute 連線，您已經連接到 Microsoft 的網路，從這裡您可以再移至的任何雲端服務，例如 Office 365 （搭配 Microsoft 對等互連） 或 Azure （使用私用和/或 Microsoft 對等互連）。
>
>

如果兩個 Vnet （Vnet A 和 B 在圖表中的） 連線到**相同**ExpressRoute 循環，您可以執行一系列的測試，以找出問題，在 Azure 中的 （或證明它不是在 Azure 中）
 
### <a name="test-plan"></a>測試計劃
1. Get LinkPerformance 測試和之間執行 VM1 vm2 的情況。 這項測試會提供深入資訊，如果此問題為本機。 可接受的延遲和頻寬結果就會產生這項測試，您可以標示區域 VNet 網路為良好。
2. 假設在本機的 VNet 流量是很好，執行 Get LinkPerformance 測試 VM1 和 VM3 之間。 這項測試執行透過 Microsoft network MSEE 下並放回 Azure 連線。 可接受的延遲和頻寬結果就會產生這項測試，您可以為良好標示 Azure 網路。
3. 如果 Azure 予排除時，您可以在您公司網路上執行類似的一連串的測試。 如果，它也可測試也，則時間來處理您的服務提供者或 ISP 診斷程式的 WAN 連線。 範例： 在兩個分公司，或您的支援人員及資料中心伺服器間執行這項測試。 根據您要測試什麼，找出端點 (伺服器、 電腦等)，可以執行該路徑。

>[!IMPORTANT]
> 務必每一項測試將執行測試的時間，並將結果記錄 （我喜歡 OneNote 或 Excel） 的一般位置。 每個測試回合應該有相同的輸出，讓您可以在測試回合中比較結果的資料，並沒有資料中的 「 洞 」。 有多項測試的一致性是我使用 AzureCT 進行疑難排解的主要原因。 識別常數不在執行，而是完全負載情節中*magic*是我所取得的事實*一致的測試和資料輸出*從每個測試。 錄製的時間，以及每一次取得一致的資料是如果稍後發現的問題，則偶爾會特別有用。 也必須努力與最前面位置的資料收集，您便可避免小時的測試 （我已了解此硬碟的方式多年前） 的相同案例。
>
>

## <a name="the-problem-is-isolated-now-what"></a>問題遭到隔離，接下來怎麼辦？
多您可以找出問題修正，但是通常您到達您無法瀏覽更深層或進一步您疑難排解的點是容易。 範例： 您可以看到您採取透過歐洲的躍點的服務提供者的連結，但預期的路徑是全部位於亞洲。 此端點時，您應該連絡的說明。 您要求者是取決於路由網域隔離問題，或如果您可以縮小至特定元件的更佳。

針對公司網路的問題，內部的 IT 部門或支援您的網路 （這可能是硬體製造商） 的服務提供者可能能夠協助進行裝置設定或硬體修復。

對於 WAN，與您的服務提供者或 ISP 共用您的測試結果可能有助於取得啟動它們，並避免涵蓋一些相同起已測試過。 不過，不被感冒，如果他們想要確認您本身的結果。 「 信任，但驗證 」 時，很好的座右銘疑難排解根據其他人的報告結果。

有了 Azure，一旦您找出您可以、 盡可能詳細的問題就可以檢閱[Azure 網路文件][ Network Docs] ，則仍然需要[開啟支援票證][Ticket Link].

## <a name="references"></a>參考
### <a name="latencybandwidth-expectations"></a>延遲/頻寬期望
>[!TIP]
> 您要測試的端點之間的地理延遲 （英哩或公里） 是最延遲的最大的元件。 雖然沒有相關的設備延遲 （實體和虛擬元件，等躍點數目），地理位置發生下列問題的最大的元件的整體延遲處理 WAN 連線時。 它也是很重要的一點的距離是執行不直線之 fiber 的距離或路段圖地圖距離。 這個距離是很難取得精確的。 如此一來，我通常使用網際網路上的 縣 （市） 距離計算機，並了解大致不正確的量值，這個方法，但足以設定的一般預期。
>
>

我在西雅圖，華盛頓在美國有 ExpressRoute 安裝程式。 下表顯示的延遲和頻寬我了解測試，以各種不同的 Azure 位置。 我已估計每個測試結束時的地理距離。

測試設定：
 - 實體伺服器執行 Windows Server 2016 10 Gbps NIC 連線到 ExpressRoute 電路。
 - 在識別與啟用私人互連位置 10Gbps Premium ExpressRoute 電路。
 - 指定區域中 UltraPerformance 閘道與 Azure VNet。
 - VNet 上執行 Windows Server 2016 DS5v2 VM。 VM 已非網域聯結時，預設值從 Azure 映像 （沒有最佳化或自訂） 以建置 AzureCT 安裝。
 - 所有測試已使用 AzureCT Get LinkPerformance 命令與 5 分鐘負載測試每個六個測試回合。 例如︰

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - 針對每個測試資料流程的資料必須從內部部署實體伺服器 （位於西雅圖 iPerf 用戶端） 至 Azure VM （iPerf 伺服器中所列的 Azure 地區） 的負載。
 - 「 延遲 」 資料行的資料是從無負載測試 （TCP 延遲不 iPerf 執行測試）。
 - 「 最大頻寬 」 資料行的資料是從 1 Mb 視窗大小 16 的 TCP 流量負載測試。

[![3]][3]

### <a name="latencybandwidth-results"></a>延遲/頻寬結果
>[!IMPORTANT]
> 這些數字是一般的參考。 許多因素都會影響延遲，並經過一段時間，這些值是通常一致，而 Azure 或服務提供者網路中的條件可以在任何時間傳送流量透過不同的路徑，因而延遲和頻寬可能會受到影響。 一般而言，這些變更的效果不造成顯著的差異。
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>位置|Azure<br/>區域|估計時間<br/>距離 （公里）|Latency|1 的工作階段<br/>頻寬|最大值<br/>頻寬|
| Seattle | 美國西部 2        |    191 金鑰管理 |   5 毫秒 | 262.0 Mbits/sec |  3.74 Gbits/sec | 21
| Seattle | 美國西部          |  1,094 金鑰管理 |  18 ms |  82.3 Mbits/sec |  3.70 Gbits/sec | 20
| Seattle | 美國中部       |  2,357 金鑰管理 |  40 ms |  38.8 Mbits/sec |  2.55 Gbits/sec | 17
| Seattle | 美國中南部 |  2,877 金鑰管理 |  51 ms |  30.6 Mbits/sec |  2.49 Gbits/sec | 19
| Seattle | 美國中北部 |  2,792 金鑰管理 |  55 ms |  27.7 Mbits/sec |  2.19 Gbits/sec | 18
| Seattle | 美國東部 2        |  3,769 金鑰管理 |  73 ms |  21.3 Mbits/sec |  1.79 Gbits/sec | 16
| Seattle | 美國東部          |  3,699 金鑰管理 |  74 ms |  21.1 Mbits/sec |  1.78 Gbits/sec | 15
| Seattle | 日本東部       |  7,705 金鑰管理 | 106 ms |  14.6 Mbits/sec |  1.22 Gbits/sec | 28
| Seattle | 英國南部         |  7,708 金鑰管理 | 146 ms |  10.6 Mbits/sec |   896 Mbits/sec | 24
| Seattle | 西歐      |  7,834 金鑰管理 | 153 ms |  10.2 Mbits/sec |   761 Mbits/sec | 23
| Seattle | 澳洲東部   | 12,484 金鑰管理 | 距離上邊緣 165 ms |   9.4 Mbits/sec |   794 Mbits/sec | 26
| Seattle | 東南亞   | 12,989 金鑰管理 | 170 ms |   9.2 Mbits/sec |   756 Mbits/sec | 25
| Seattle | 巴西南部 *   | 10,930 金鑰管理 | 189 ms |   8.2 Mbits/sec |   699 Mbits/sec | 22
| Seattle | 印度南部      | 12,918 金鑰管理 | 202 ms |   7.7 Mbits/sec |   634 Mbits/sec | 27

\*巴西延遲是不錯的範例，其中的直線距離明顯不同於執行距離 fiber。 但我本來預期延遲會在約為 160 毫秒，但是實際 189 ms。 這項差異，對我預期的情況下，可能表示網路問題的地方，但最有可能 fiber 執行不是以直線移巴西，有額外的 1,000 km 左右移動前往巴西從西雅圖。

## <a name="next-steps"></a>後續步驟
1. 從 GitHub 下載 Azure 連接性工具組[http://aka.ms/AzCT][ACT]
2. 請依照指示[連結效能測試][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "azure 網路元件"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute 疑難排解"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "效能測試環境"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell 輸出"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index#pivot=services&panel=network
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: http://aka.ms/AzCT











