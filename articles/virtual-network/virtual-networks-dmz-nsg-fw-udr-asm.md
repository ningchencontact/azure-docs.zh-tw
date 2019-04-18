---
title: 周邊網路範例 – 周邊網路包含防火牆、 UDR 和 Nsg 保護網路 |Microsoft Docs
description: ： 建置周邊網路 (也稱為 DMZ) 防火牆、 使用者定義路由 (UDR)，與網路安全性群組 (Nsg)。
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: 668862714b416bd89d3b5f82caf8b0305fccae54
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426523"
---
# <a name="example-3-build-a-perimeter-network-to-protect-networks-with-a-firewall-udr-and-nsgs"></a>範例 3：： 建置周邊網路，以透過防火牆、 UDR 和 Nsg 保護網路

[返回 [安全性界限最佳作法] 頁面][HOME]

在此範例中，您會建立周邊網路 （也稱為 DMZ、 非軍事區域和遮蔽式子網路）。 此範例會實作防火牆、 四個 Windows 伺服器、 使用者定義路由 (UDR)、 IP 轉送和網路安全性群組 (Nsg)。 這篇文章會引導您透過每個相關的命令，以提供更深入的了解每個步驟。 流量案例一節也說明深入了解如何流量流經周邊網路中的防禦層。 最後，[參考] 區段包含所有的程式碼和指示，來建置此環境，讓您能夠測試和試驗各種案例。

![包含 NVA、NSG 和 UDR 的双向外围网络][1]

## <a name="environment-setup"></a>環境設定

此範例使用的訂用帳戶包含下列元件：

* 三個雲端服務：SecSvc001、 FrontEnd001 和 BackEnd001
* 虛擬網路 (CorpNetwork) 包含三個子網路：SecNet、 FrontEnd 和 BackEnd
* 網路虛擬設備： 防火牆連接到 SecNet 子網路
* 一個代表應用程式 web 伺服器的 Windows server:IIS01
* 代表應用程式後端伺服器的兩個 Windows server:AppVM01, AppVM02
* 一個代表 DNS 伺服器的 Windows server:DNS01

[參考區段](#references)包含組建的大部分環境此處所述的 PowerShell 指令碼。 否則，這篇文章不提供詳細的指示來建立虛擬機器 (Vm) 和虛擬網路。

若要建置環境：

1. 儲存網路組態 XML 檔案中包含[參考章節](#references)。 您將需要更新名稱、 位置、 與 IP 位址，以符合指定的案例。
1. 更新完整的指令碼，以符合您特定環境 （例如，訂用帳戶、 服務名稱等等） 中的使用者變數。
1. 在 PowerShell 中執行指令碼。

> [!NOTE]
> PowerShell 指令碼中指定的區域必須符合網路組態 XML 檔中指定的區域。

指令碼順利執行之後，執行下列步驟：

1. 設定防火牆規則。 請參閱[防火牆規則](#firewall-rules)一節。
1. （選擇性） 使用兩個指令碼參考 區段中，若要設定 web 應用程式在 web 伺服器和應用程式伺服器上允許此 DMZ 組態的測試。

## <a name="user-defined-routing"></a>使用者定義的路由

根據預設，下列系統路由的定義如下：

    Effective routes :
     Address Prefix    Next hop type    Next hop IP address Status   Source
     --------------    -------------    ------------------- ------   ------
     {10.0.0.0/16}     VNETLocal                            Active   Default
     {0.0.0.0/0}       internet                             Active   Default
     {10.0.0.0/8}      Null                                 Active   Default
     {100.64.0.0/10}   Null                                 Active   Default
     {172.16.0.0/12}   Null                                 Active   Default
     {192.168.0.0/16}  Null                                 Active   Default

VNETLocal 一律為該特定的虛擬網路定義的位址首碼。 例如，將會變更虛擬網路從虛擬網路，根據每個特定的虛擬網路的定義方式。 其餘系統路由是靜態，而且預設值如下所示。

與優先順序，路由會透過最長首碼比對 (LPM) 方法來處理。 因此最明確的路由表中適用於給定的目的地位址。

因此，流量給伺服器，例如 DNS01 (10.0.2.4) 在本機網路 (10.0.0.0/16) 會在虛擬網路之間路由傳送因為 10.0.0.0/16 路由。  針對 10.0.2.4，10.0.0.0/16 路由是最明確的路由。 即使 10.0.0.0/8/8 以及 0.0.0.0/0 也可能適用，就會套用此規則。 它們是較不明確，不過，因此他們不會影響此流量。 往 10.0.2.4 的流量會路由傳送至目的地，因此其下一個躍點具有本機虛擬網路。

比方說，10.0.0.0/16 路由不適用於是用於 10.1.1.1 的流量。 10.0.0.0/8 系統路由是最特定的因此捨棄流量，或 「 黑洞化 」 因為下一個躍點是 Null。

如果目的地不適用於任何 Null 前置詞或 VNETLocal 前置詞，流量就會遵循最不特定的路由 (0.0.0.0/0)。 它會路由傳送網際網路做為下一個躍點，並從 Azure 的網際網路邊緣。

如果路由表中有兩個相同的前置詞，偏好的順序為基礎的路由來源屬性：

1. VirtualAppliance:使用者定義路由，以手動方式加入至資料表。
1. VPNGateway:新增動態網路通訊協定的動態路由 (BGP 與混合式網路搭配使用時)。 這些路由會隨著時間改變，因為動態的通訊協定會自動反映在對等互連的網路中的變更。
1. 預設值：系統路由、 本機虛擬網路和路由表所示的靜態項目。

> [!NOTE]
> 您現在可以使用 ExpressRoute 與 VPN 閘道的使用者定義路由 (UDR)，以強制在傳出和傳入的跨單位流量路由傳送至網路虛擬設備 (NVA)。

### <a name="create-local-routes"></a>建立本機路由

此範例使用兩個路由表，分別用於前端和後端的子網路。 每個資料表都會載入適用於給定子網路的靜態路由。 為進行此範例，每個資料表內有三個路由：

1. 定義沒有下一個躍點的本機子網路流量。 此途徑可讓您略過防火牆的本機子網路流量。
2. 定義為防火牆的下一個躍點虛擬網路流量。 此路由會覆寫允許本機虛擬網路流量直接路由的預設規則。
3. 所有其餘流量 (0/0) 定義為防火牆的下一個躍點。

建立路由表之後，它們會繫結至其子網路。 前端子網路路由表看起來應該類似：

    Effective routes :
     Address Prefix    Next hop type       Next hop IP address  Status   Source
     --------------    ------------------  -------------------  ------   ------
     {10.0.1.0/24}     VNETLocal                                Active
     {10.0.0.0/16}     VirtualAppliance    10.0.0.4             Active
     {0.0.0.0/0}       VirtualAppliance    10.0.0.4             Active

此範例會使用下列命令來建置路由表、 新增使用者定義的路由，然後將路由表繫結至子網路。 開頭的項目`$`，例如`$BESubnet`，是使用者定義的變數，從 [參考] 區段中的指令碼。

1. 首先，建立基底的路由表。 下列程式碼片段會建立後端子網路的資料表。 完整的指令碼也會建立對應的資料表前, 端子網路。

   ```powershell
   New-AzureRouteTable -Name $BERouteTableName `
       -Location $DeploymentLocation `
       -Label "Route table for $BESubnet subnet"
   ```

1. 您已建立路由表之後，您可以新增特定使用者定義的路由。 下列程式碼片段會指定所有流量 (0.0.0.0/0) 透過虛擬設備路由都傳送。 變數`$VMIP[0]`用來傳入指派稍早在指令碼中建立的虛擬應用裝置的 IP 位址。 完整的指令碼也會建立前端的資料表中對應的規則。

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. 先前的路由項目會覆寫預設的"0.0.0.0/0"路由，但是預設 10.0.0.0/16 規則仍允許直接至目的地而非網路虛擬設備路由傳送虛擬網路內的流量。 若要更正這個問題，您需要新增下列規則：

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. 此時，您必須做出的選擇。 兩個的上一個規則會將所有流量都路由至防火牆進行評估，包括單一子網路內的流量。 您可能希望這個行為。 如果您不知道，不過，您可以允許不需要防火牆涉入在本機路由傳送子網路內的流量。 新增第三，直接路由傳送目的地的本機子網路的任何位址的特定規則 (NextHopType = VNETLocal)。

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
           -NextHopType VNETLocal
   ```

1. 最後，建立並填入使用者定義的路由的路由表之後，您需要繫結至子網路的表格。 下列程式碼片段會繫結後端子網路的資料表。 完整的指令碼也會將繫結的前端的路由表與前端子網路。

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
       -SubnetName $BESubnet `
       -RouteTableName $BERouteTableName
   ```

## <a name="ip-forwarding"></a>IP 轉送

IP 轉送是 UDR 的隨附功能。 虛擬應用裝置上的此設定可讓它接收未定址到設備的流量，然後將轉送到其最終目的地的流量。

例如，如果來自 AppVM01 的流量對 DNS01 伺服器提出要求，UDR 會將流量路由至防火牆。 啟用 IP 轉送，DNS01 目的地 (10.0.2.4) 的流量是防火牆應用裝置 (10.0.0.4) 所接受的並接著轉送到其最終目的地 (10.0.2.4)。 而不需要在防火牆上啟用 IP 轉送，流量不接受由設備即使路由表防火牆做為下一個躍點。

> [!IMPORTANT]
> 請記得啟用 IP 轉送，搭配使用者定義的路由。

在建立 VM 時，可以使用單一命令啟用 IP 轉送。 您可以呼叫您的防火牆虛擬設備和啟用 IP 轉送功能的 VM 執行個體。 請記住，項目開頭的紅色`$`，例如`$VMName[0]`，是使用者定義的變數，從這份文件的參考 > 一節中的指令碼。 在方括號，零`[0]`，代表 Vm 陣列中的第一個 VM。 範例指令碼無須修改即可運作，第一個 VM (VM 0) 必須是防火牆。 在完整的指令碼，相關的程式碼片段被分組與 UDR 命令即將結束。

```powershell
Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
    Set-AzureIPForwarding -Enable
```

## <a name="network-security-groups"></a>網路安全性群組

在此範例中，您建立網路安全性群組 (NSG)，然後將它載入單一規則。 此範例接著會繫結 NSG 只前端和後端子網路 (不到 SecNet)。 您載入 NSG 的規則如下所示：

* 拒絕的任何流量 （所有連接埠） 從網際網路到整個虛擬網路 （所有子網路）

雖然在此範例中使用 Nsg，其主要目的是作為一道防線，手動設定不正確的第二層。 您想要封鎖所有傳入的流量從網際網路到其中一個前端或後端的子網路。 流量只應流經 SecNet 子網路到防火牆之後, 才適用的流量應該會路由到前端或後端的子網路。 此外，UDR 規則路由傳送任何到達防火牆的前端或後端的子網路的流量。 防火牆會將它視為非對稱流量，並卸除的輸出流量。

三個層級的安全性保護的前端和後端 」 子網路：

1. FrontEnd001 和 BackEnd001 雲端服務上沒有開放的端點
1. Nsg 拒絕來自網際網路的流量
1. 防火牆會捨棄非對稱流量

在此範例中的 NSG 相關有趣的是，它會包含只有一個規則，如下所示。 此規則會拒絕網際網路流往整個虛擬網路，包括安全性子網路流量。

```powershell
Get-AzureNetworkSecurityGroup -Name $NSGName | `
    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
    from the internet" `
    -Type Inbound -Priority 100 -Action Deny `
    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
    -DestinationAddressPrefix VIRTUAL_NETWORK `
    -DestinationPortRange '*' `
    -Protocol *
```

由於 NSG 只會繫結到前端和後端的子網路，不會對流往安全性子網路的輸入流量套用的規則。 如此一來，流量就會傳送到安全性子網路。

```powershell
Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $FESubnet -VirtualNetworkName $VNetName

Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $BESubnet -VirtualNetworkName $VNetName
```

## <a name="firewall-rules"></a>防火牆規則

您必須在防火牆上建立轉送規則。 因為防火牆封鎖或轉送所有輸入、 輸出及內 virtual 網路流量，您會需要許多防火牆規則。 此外，防火牆必須處理所有輸入的流量的安全性服務公用 IP 位址 （在不同的連接埠）。 若要避免日後發生重新作業，請由圖表之前設定的子網路和防火牆規則的邏輯流程遵循的最佳做法。 下圖是此範例中的防火牆規則的邏輯視圖：

![防火牆規則的邏輯視圖][2]

> [!NOTE]
> 管理連接埠會因網路虛擬設備而有所不同。 此範例會顯示為 Barracuda NextGen 防火牆使用連接埠 22、 801 和 807。 請參閱設備廠商的說明文件來尋找來管理裝置的確切連接埠。

### <a name="logical-rule-description"></a>邏輯規則說明

上述邏輯視圖未顯示安全性子網路，因為防火牆是該子網路上唯一的資源。 下圖顯示的防火牆規則，如何在邏輯上允許或拒絕流量的流動，而不是是實際的路由路徑。 此外，遠端桌面通訊協定 (RDP) 流量選取的外部連接埠是較高的遠距通訊埠 (8014 – 8026)，以方便閱讀，以配合本機 IP 位址的最後兩個八位元的選擇。 比方說，本機伺服器位址 10.0.1.4 會與外部連接埠 8014 相關聯。 不過，您可以使用任何更高的非衝突連接埠。

您需要下列類型的規則，此範例中：

* 外部輸入流量規則：
  1. 防火牆管理規則： 允許流量傳遞至網路虛擬設備的管理連接埠。
  2. 針對每個 windows 伺服器的 RDP 規則： 可讓個別的伺服器，透過 RDP 管理。  根據您的網路虛擬設備的功能，您可以組合成一個的規則。
  3. 應用程式流量規則： 一個用於前端 web 流量，另一個用於後端流量 （比方說，web 伺服器流往資料層）。 這些規則的設定取決於網路架構及流量流程。

     * 第一個規則允許實際的應用程式流量抵達應用程式伺服器。 不同的安全性、 管理和等等的規則，於應用程式規則會允許外部使用者或服務存取的應用程式。 此範例中會有單一 web 伺服器連接埠 80，可讓單一防火牆應用程式規則，將目的地為外部 IP 位址給改為將路由傳送至 web 伺服器的內部 IP 位址的流量重新導向。 內部伺服器重新導向的流量工作階段重新 nat 對應。
     * 第二個應用程式流量規則是後端規則，以允許 web 伺服器來路由傳送流量，AppVM02 伺服器 AppVM01 伺服器，但不是使用任何通訊埠。

* 內部內 virtual 網路流量規則：
  1. 輸出到網際網路規則： 允許來自任何傳遞至所選的網路的網路流量。 此規則通常是預設值的防火牆上，但是已停用狀態。 啟用此規則，此範例中。
  2. DNS 規則： 只允許 DNS （連接埠 53） 流量傳遞至 DNS 伺服器。 在這種環境中，大部分從前端到後端流量都會被封鎖。 這個規則特別允許來自任何本機子網路的 DNS。
  3. 子網路的子網路規則： 可讓後端子網路來連接到前端子網路，但不是能反向上任何伺服器上的任何伺服器。

* 保全不符合上述準則的流量規則：
  1. 拒絕所有流量規則： 一律最終的規則依據優先順序。 如果流量流動行為無法比對任何上述規則，此規則會封鎖它。 它是預設規則。 因為經常啟動它時，會不需要進行任何修改。

> [!TIP]
> 在第二個應用程式流量規則中，為了方便說明此範例允許任何連接埠。 在真實的案例中，您應該使用特定連接埠和位址範圍，以減少受攻擊面，此規則。


> [!IMPORTANT]
> 您已建立規則之後，很重要，您會檢閱以確保流量會允許或拒絕所需的每個規則的優先順序。 在此範例中，規則是依照優先順序排列。 它很容易遭到鎖定而無法在防火牆，如果規則順序若錯誤。 請務必將防火牆管理規則設定為絕對最高的優先權。

### <a name="rule-prerequisites"></a>規則的必要條件

需要執行防火牆之虛擬機器的公用端點。 這些公用端點必須開啟，讓防火牆能夠處理流量。 有三種類型的流量，在此範例中：

1. 管理流量，用以控制防火牆與防火牆規則
1. 若要控制的 windows 伺服器的 RDP 流量
1. 應用程式流量

流量類型會出現在的防火牆規則上述邏輯視圖上半部。

> [!IMPORTANT]
> 請記住*所有*流量會通過防火牆。 桌面遠端連線至 IIS01 伺服器，您需要連接到連接埠 8014 上的防火牆，然後允許防火牆在內部將 RDP 要求路由至 IIS01 RDP 連接埠。 在 Azure 入口網站**Connect**按鈕將無法運作，因為沒有任何直接 RDP 路徑到 IIS01 就可以看到入口網站。 所有來自網際網路的連線，就是安全性服務和連接埠 (例如 secscv001.cloudapp.net:xxxx)。 參考上圖中的外部連接埠和內部 IP 和連接埠對應。

您可以在建立 VM 時或在建置之後，開啟端點。 範例指令碼和下列程式碼開啟端點之後建立 VM。

請記住，項目開頭`$`，例如`$VMName[$i]`，是使用者定義的變數，從 [參考] 區段中的指令碼。 `[$i]`代表 Vm 陣列中特定 VM 的陣列數目。

```powershell
Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
    Update-AzureVM
```

雖然它此處未清楚顯示因為變數，您應該只會開啟安全性雲端服務上的端點。 此預防措施可協助確保，防火牆會處理所有的輸入的流量，是否已路由傳送、 由 NAT，重新對應或卸除。

安裝管理用戶端的電腦來管理防火牆及建立必要的設定。 如需如何管理您的防火牆或其他 NVA 的詳細資訊，請參閱廠商的文件。 本節的其餘部分，以及**防火牆規則建立**節將說明設定防火牆。 使用廠商的管理用戶端，Azure 入口網站或 PowerShell 無法使用。

移至[Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)下載的管理用戶端，並了解如何連接到 Barracuda 防火牆。

您登入防火牆後之前建立的防火牆規則,，定義網路和服務物件。 這兩個必要的物件類別可以讓您建立的規則更容易。

針對此範例，請針對每一個定義三個具名的網路物件：

* 前端子網路
* 後端子網路
* DNS 伺服器 IP 位址

若要從 Barracuda NG Admin 用戶端儀表板中建立的具名的網路：

1. 移至**設定 索引標籤**。
1. 選取  **Ruleset**中**作業組態**區段
1. 選取 **網路**下方**防火牆物件**功能表。
1. 選取 **的新**中**編輯網路**功能表。
1. 藉由新增名稱和前置詞建立的網路物件：

   ![建立 front tech-ed 網路物件][3]

上述步驟建立前端子網路的具名的網路。 建立類似的物件後, 端子網路。 現在您可以更輕鬆地在防火牆規則中依名稱參考子網路。

DNS 伺服器物件：

![建立 DNS 伺服器物件][4]

在本文件稍後 DNS 規則中用於這個單一 IP 位址參考。

另一個物件類別包含代表每一部伺服器的 RDP 連線連接埠的服務物件。 現有的 RDP 服務物件會繫結至預設 RDP 連接埠 3389。 因此，您可以建立新的服務，以允許從外部連接埠 (8014-8026) 的流量。 您也可以新增新的連接埠，以既有 RDP 服務。 不過，為了便於示範，您可以讓個別規則的每一部伺服器。 若要從 Barracuda NG Admin 用戶端儀表板中建立新的 RDP 規則的伺服器：

1. 移至**設定 索引標籤**。
1. 選取  **Ruleset**中**作業組態**一節。
1. 選取  **Services**下方**防火牆物件**功能表。
1. 向下捲動的服務，然後選取清單**RDP**。
1. 以滑鼠右鍵按一下選取 [複製]，然後以滑鼠右鍵按一下並選取貼。
1. 目前沒有可編輯的 RDP-Copy1 服務物件。 以滑鼠右鍵按一下**RDP-Copy1** ，然後選取**編輯**。
1. **編輯服務物件**視窗此時會出現，如下所示：

   ![預設 RDP 規則的複本][5]

1. 編輯的值來表示特定伺服器的 RDP 服務。 針對 AppVM01，應該修改預設 RDP 規則以反映新的服務**名稱**，**描述**，和 圖 8 圖表中使用的外部 RDP 連接埠。 請記住，連接埠會從 RDP 預設值 3389 變更為此特定伺服器的外部連接埠。 例如，針對 AppVM01 的外部連接埠是 8025。 已修改之服務的規則如下所示：

   ![AppVM01 規則][6]

重複此程序來建立其餘伺服器的 RDP 服務：AppVM02、 DNS01 和 IIS01。 這些服務會讓規則在下一節中建立的工作變得更容易且更明顯。

> [!NOTE]
> 防火牆 RDP 服務不需要，因為防火牆 VM 是以 Linux 為基礎的映像，因此 SSH 連接埠 22 上而不是 RDP 的 VM 管理。 此外，連接埠 22，而其他兩個連接埠允許進行管理連線。 請參閱**防火牆管理規則**下一節。

### <a name="firewall-rules-creation"></a>建立防火牆規則

有三種類型的防火牆規則，在此範例中，所有具有不同的圖示中使用：

應用程式重新導向規則：![應用程式重新導向圖示][7]

目的地 NAT 規則：![目的地 NAT 圖示][8]

傳遞規則：![傳遞圖示][9]

Barracuda 網站可以找到這些規則的詳細資訊。

若要建立下列規則或驗證現有的預設規則：

1. 從 Barracuda NG Admin 用戶端儀表板，請移至**組態** 索引標籤。
1. 在 **作業組態**區段中，選取**Ruleset**。
1. **主要規則**方格顯示現有的使用中狀態，並停用此防火牆規則。 選取綠色**+** 中建立新規則的右上角。 如果您的防火牆已鎖定的變更，您會看到按鈕標示**鎖定**而且無法建立或編輯規則。 選取 **鎖定**解除鎖定的規則集，並允許 編輯 按鈕。 以滑鼠右鍵按一下您想要編輯和選取的規則**編輯規則**。

您建立或修改任何規則之後，將其推送至防火牆並加以啟動。 否則，規則的變更才會生效。 推送和啟用程序所述[規則啟用](#rule-activation)。

以下是完成此範例中所需的每個規則的詳細資訊：

* **防火牆管理規則**:此應用程式重新導向規則允許流量傳遞至網路虛擬設備，但在此範例中為 Barracuda NextGen 防火牆管理連接埠。 管理連接埠是 801、 807，以及選擇性地 22。 外部和內部連接埠是相同的沒有連接埠轉譯。 此規則會呼叫設定管理存取。 它是預設規則，並在 Barracuda NextGen 防火牆 6.1 版中預設啟用。
  
    ![防火牆管理規則][10]

  > [!TIP]
  > 此規則中的來源位址空間**任何**。 如果已知管理 IP 位址範圍，減少此範圍也會減少管理連接埠的受攻擊面。

* **RDP 規則**:這些目的地 NAT 規則允許透過 RDP 的個別伺服器的管理。 這些規則的重要欄位如下：
  * 來源。 若要允許從任何地方的 RDP，請使用參考**任何**來源欄位中。
  * 服務。 使用您稍早建立的 RDP 服務物件：**AppVM01 RDP**。 外部連接埠重新導向至伺服器的本機 IP 位址和預設 RDP 連接埠 3386。 此特定規則用於 AppVM01 的 RDP 存取。
  * 目的端。 使用本機連接埠在防火牆上：**DCHP 1 本機 IP**或是**eth0**如果您使用靜態 Ip。 序號 （eth0 eth1 等等） 可能會不同，如果您的網路應用裝置有多個本機介面。 防火牆會使用此連接埠進行傳送，而且它可能與接收的連接埠相同。 實際的路由的目的地處於**目標清單**欄位。
  * 重新導向。 設定此選項，以告知虛擬應用裝置最終將此流量重新導向的位置。 最簡單的重新導向是放置在目標清單 欄位中的 IP。 您也可以指定為連接埠，以及 NAT device-mapper 連接埠和 IP 位址。 如果您未指定連接埠，虛擬應用裝置會使用目的地連接埠上傳入的要求。

    ![防火牆 RDP 規則][11]

    建立四個 RDP 規則：

    | 規則名稱 | 伺服器 | 服務 | 目標清單 |
    | --- | --- | --- | --- |
    | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
    | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
    | RDP-to-AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
    | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

  > [!TIP]
  > [來源和服務] 欄位的範圍縮小，減少攻擊面。 使用可讓功能的最小的範圍。

* **應用程式流量規則**:有兩個應用程式流量規則。 其中一個是前端 web 流量。 另涵蓋像是 web 伺服器流往資料層的後端流量。 這些規則取決於網路架構和流量流動行為。
  
  * 適用於 web 流量的前端規則：
  
    ![防火牆 Web 規則][12]
  
    此目的地 NAT 規則允許實際的應用程式流量抵達應用程式伺服器。 不同的安全性、 管理和烈焰規則中，於應用程式規則會允許外部使用者或服務存取的應用程式。 此範例中會有單一 web 伺服器連接埠 80，可讓單一防火牆應用程式規則，將目的地為外部 IP 位址給改為將路由傳送至 web 伺服器的內部 IP 位址的流量重新導向。 內部伺服器重新導向的流量工作階段重新 nat 對應。

    > [!NOTE]
    > 沒有指派連接埠**目標清單**欄位。 因此，輸入連接埠 80 （或使用 443 進行所選取的服務） 會在 web 伺服器的重新導向。 如果 web 伺服器正在接聽不同的連接埠 8080 類似，您可以更新 10.0.1.4:8080 以允許連接埠重新導向至的目標清單 欄位。
  
  * 後端規則可讓 web 伺服器透過與 AppVM01 伺服器，而不是 AppVM02**任何**服務：
  
    ![防火牆 AppVM01 規則][13]
  
    此傳遞規則允許前端子網路，連線到 AppVM01 上的任何 IIS 伺服器 (10.0.2.5) 上使用任何通訊協定，如此一來，web 應用程式可以存取資料的任何連接埠。
  
    在此螢幕擷取畫面中，`<explicit-dest>`用於**目的地**欄位來表示以 10.0.2.5 做為目的地。 您可以在螢幕擷取畫面所示的明確指定的 IP 位址。 您也可以使用具名的網路物件，例如，在 DNS 伺服器的必要條件。 防火牆系統管理員可以選擇要使用的方法。 若要將 10.0.2.5 新增為明確目的地中，按兩下底下的第一個空白資料列`<explicit-dest>`並在開啟的對話方塊中輸入的位址。
  
    使用此傳遞規則中，不需要 NAT，因為它處理內部的流量。 您可以設定**連線方法**至`No SNAT`。
  
    > [!NOTE]
    > 此規則的 [來源] 網路是 frontend 子網路上的任何資源，如果只有一個。 如果您的架構會指定已知的數個 web 伺服器，您可以建立更針對這些確切 IP 位址，而不是整個的前端子網路的網路物件資源。

    > [!TIP]
    > 此規則使用的服務**任何**讓您更輕鬆地設定和使用範例應用程式。 它允許 ICMPv4 (ping) 中的單一規則。 不過，若要減少攻擊介面跨越此界限，建議可能允許應用程式作業的最小限制的連接埠和通訊協定服務。

    > [!TIP]
    > 雖然此範例規則使用`<explicit-dest>`參考，您應該使用一致的方法，在整個防火牆設定。 我們建議使用更容易閱讀和支援的具名的網路物件。 `<explicit-dest>`僅為顯示替代參考方法如下所示。 我們通常不建議，特別是針對複雜的組態。

* **輸出到網際網路規則**:此傳遞規則允許來自任何來源的網路傳遞至所選目的地網路流量。 根據預設，但停用狀態，Barracuda NextGen 防火牆會將此規則通常具有 「 開啟 」。 若要存取此規則上按一下滑鼠右鍵**啟用的規則**命令。 修改螢幕擷取畫面顯示將後端與前端的子網路的網路物件新增至來源屬性，此規則的規則。 您在這篇文章的必要條件 > 一節中建立這些網路物件。
  
    ![防火牆輸出規則][14]

* **DNS 規則**:此傳遞規則只會允許 DNS （連接埠 53） 流量傳遞至 DNS 伺服器。 此環境，讓此規則特別允許 DNS 流量，會封鎖大部分從前端到後端流量。
  
    ![防火牆 DNS 規則][15]
  
    > [!NOTE]
    > **連線方法**設定為`No SNAT`因為此規則為內部 IP 位址流量的內部 ip 位址，而且不應透過 NAT 是必要。

* **子網路對子規則**:此預設傳遞規則經過啟用和修改，以允許後端子網路連接到前端子網路上的任何伺服器上的任何伺服器。 此規則只 coves 內部流量因此**連線方法**可以設為`No SNAT`。

    ![防火牆內部 VNet 規則][16]
  
    > [!NOTE]
    > **雙向**因此這項規則也適用於只有一個方向未在此選取核取方塊。 連接可以起始從後端子網路的前端網路，但不是能反向。 如果已選取該核取方塊，此規則會啟用雙向流量，我們已經為我們的邏輯圖，不需要指定。

* **拒絕所有流量規則**:此規則應該一律是最終的規則，依據優先順序。 如果流量不符合任何先前的規則，此規則會導致卸除。 通常，根據預設來啟用規則，因此不需要進行任何修改。
  
    ![防火牆拒絕規則][17]

> [!IMPORTANT]
> 所有上述規則建立之後，請檢閱以確保流量允許或拒絕所需的每個規則的優先順序。 針對此範例，其應該出現在 Barracuda 管理用戶端的主要方格中的轉送規則的順序會列出的規則。

## <a name="rule-activation"></a>規則啟用

您已修改的規則集以符合邏輯圖的規格之後，您需要上傳至防火牆的規則集並將它啟用。

![防火牆規則啟用][18]

在右上角的 [管理用戶端] 視窗中尋找並選取**傳送變更**上傳至防火牆的修改過的規則。 選取 [ **啟用**]。

當您啟用防火牆規則集合時，這個範例環境中已完成。

## <a name="traffic-scenarios"></a>流量案例

> [!IMPORTANT]
> 請記住*所有*流量會通過防火牆。 桌面遠端連線至 IIS01 伺服器，您需要連接到連接埠 8014 上的防火牆，然後允許防火牆在內部將 RDP 要求路由至 IIS01 RDP 連接埠。 在 Azure 入口網站**Connect**按鈕將無法運作，因為沒有任何直接 RDP 路徑到 IIS01 就可以看到入口網站。 所有來自網際網路的連線，就是安全性服務和連接埠 (例如 secscv001.cloudapp.net:xxxx)。 參考上圖中的外部連接埠和內部 IP 和連接埠對應。

針對這些案例，應備妥下列防火牆規則：

1. 防火牆管理 (FW Mgmt)
2. RDP 到 IIS01
3. RDP 到 DNS01
4. RDP 到 AppVM01
5. RDP 到 AppVM02
6. 到 Web 的應用程式流量
7. 到 AppVM01 的應用程式流量
8. 輸出至網際網路
9. 前端到 DNS01
10. 內部子網路流量 (僅限 Backend 到 Frontend)
11. 全部拒絕

您實際的防火牆規則集很可能會要求比在此範例中的多個規則。 它們可能會有不同的優先順序數字。 您應該將此清單和相關聯的數字，其彼此的相對優先順序。 比方說，「 RDP 到 IIS01 」 規則可能會是數字 5 上實際的防火牆，但只要它的下面的 「 防火牆管理 」 規則並高於 「 RDP 到 DNS01 」 規則，集合將對齊此清單的目的。 這份清單也有助於簡化的案例，請依照下列指示。 比方說，「 防火牆規則 9 (DNS)。 」 請注意，四個 RDP 規則統稱為 「 RDP 規則 」 當流量案例與 RDP 無關。

也重新叫用中的前端和後端的子網路上的輸入網際網路流量的網路安全性群組 (Nsg)。

### <a name="allowed-internet-to-web-server"></a>（允許）網際網路到 web 伺服器

1. 網際網路使用者從 SecSvc001.CloudApp.Net （網際網路面向雲端服務） 要求 HTTP 頁面。
1. 雲端服務會將流量透過開放的端點連接埠 80 上傳遞至 10.0.0.4: 80 上的防火牆介面。
1. 沒有 NSG 指派給安全性子網路中，因此系統 NSG 規則允許流量流往防火牆。
1. 流量會叫用內部 IP 位址的防火牆 (10.0.1.4)。
1. 防火牆會執行處理規則：
   1. 防火牆規則 1 (FW Mgmt) 不適用。 移至下一步 的規則。
   1. 防火牆規則 2-5 （RDP 規則） 不適用。 移至下一步 的規則。
   1. 防火牆規則 6 (應用程式：適用 web)。 允許的流量。 防火牆 device-mapper 透過 NAT 至 10.0.1.4 (IIS01) 的流量。
1. 前端子網路會執行處理輸入的規則：
   1. NSG 規則 1 （封鎖網際網路） 不適用。 因此，來源位址現在是防火牆，防火牆重新路由傳送此流量透過 NAT。 因為防火牆位於安全性子網路，而且會顯示為本機 NSG 與前端子網路的流量，允許流量。 移至下一步 的規則。
   1. 預設的 NSG 規則允許至子網路的子網路的流量，因此允許此流量。 停止處理 NSG 規則。
1. IIS01 正在接聽 web 流量。 它會收到此要求，並開始處理要求。
1. IIS01 嘗試初始化 FTP 工作階段到 AppVM01 上的後端 」 子網路。
1. 前端子網路上的 UDR 路由讓防火牆成為下一個躍點。
1. 因此允許流量前端子網路上沒有輸出規則。
1. 防火牆開始處理規則：
   1. 防火牆規則 1 (FW Mgmt) 不適用。 移至下一步 的規則。
   1. 防火牆規則 2-5 （RDP 規則） 不適用。 移至下一步 的規則。
   1. 防火牆規則 6 (應用程式：Web) 不適用。 移至下一步 的規則。
   1. 防火牆規則 7 (應用程式： backend) 適用。 允許的流量。 防火牆轉送流量到 10.0.2.5 (AppVM01)。
1. 後端子網路會執行處理輸入的規則：
    1. NSG 規則 1 （封鎖網際網路） 不適用。 移至下一步 的規則。
    1. 預設的 NSG 規則允許至子網路的子網路的流量。 允許的流量。 停止處理 NSG 規則。
1. AppVM01 接收要求、 起始工作階段中，而會回應。
1. 在後端子網路的 UDR 路由讓防火牆成為下一個躍點。
1. 所以允許回應，因此後端的子網路上沒有輸出 NSG 規則。
1. 因為它會傳回已建立的工作階段上的流量，防火牆就會將回應傳回給網頁伺服器 (IIS01)。
1. 前端子網路會執行處理輸入的規則：
    1. NSG 規則 1 （封鎖網際網路） 不適用。 移至下一步 的規則。
    1. 預設的 NSG 規則允許至子網路的子網路的流量，因此允許此流量。 停止處理 NSG 規則。
1. IIS 伺服器接收回應，並完成 AppVM01 的交易。 然後伺服器會完成建置好 HTTP 回應，並將它傳送給要求者。
1. 因此所以允許回應前端子網路上沒有輸出 NSG 規則。
1. HTTP 回應抵達防火牆。 因為它是建立的 NAT 工作階段的回應，防火牆就會接受它。
1. 防火牆會將回應傳回給網際網路使用者重新導向。
1. 沒有輸出 NSG 規則或 UDR 躍點上的前端子網路，所以允許回應。 網際網路使用者會收到要求的網頁。

### <a name="allowed-internet-rdp-to-back-end"></a>（允許）網際網路 RDP 到後端

1. 在網際網路上的伺服器系統管理員要求透過 secsvc001.cloudapp.net:8025 啟動 AppVM01 的 RDP 工作階段。 8025 是防火牆規則 4 (RDP AppVM01) 的使用者指派的通訊埠編號。
1. 雲端服務會將流量透過開放的端點連接埠 8025 上傳遞至 10.0.0.4:8025 上的防火牆介面。
1. 沒有 NSG 指派給安全性子網路中，因此系統 NSG 規則允許流量流往防火牆。
1. 防火牆會執行處理規則：
   1. 防火牆規則 1 (FW Mgmt) 不適用。 移至下一步 的規則。
   1. 防火牆規則 2 (RDP IIS) 不適用。 移至下一步 的規則。
   1. 防火牆規則 3 (RDP DNS01) 不適用。 移至下一步 的規則。
   1. 防火牆規則 4 (RDP AppVM01) 適用，允許流量。 防火牆 device-mapper 它透過 NAT 至 10.0.2.5: 3386 （AppVM01 上的 RDP 連接埠）。
1. 後端子網路會執行處理輸入的規則：
   1. NSG 規則 1 （封鎖網際網路） 不適用。 因此，來源位址現在是位於安全性子網路上的防火牆，防火牆重新路由傳送此流量透過 NAT。 它由後端的子網路 NSG 認為是本機的流量，並允許。 移至下一步 的規則。
   1. 預設的 NSG 規則允許至子網路的子網路的流量，因此允許此流量。 停止處理 NSG 規則。
1. AppVM01 正在接聽 RDP 流量，並回應。
1. 沒有輸出 NSG 規則，因此會套用預設規則。 允許傳回的流量。
1. UDR 路由輸出流量流往防火牆作為下一個躍點。
1. 因為它會傳回已建立的工作階段上的流量，防火牆就會將回應傳回給網際網路使用者。
1. 已啟用 RDP 工作階段。
1. AppVM01 會提示輸入使用者名稱密碼。

### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>（允許）DNS 伺服器上的 web 伺服器 DNS 查閱

1. Web 伺服器 IIS01 要求資料，在 http 摘要\:\/\/www.data.gov，但需要解析位址。
1. 網路組態的虛擬網路清單 DNS01 (後端的子網路上的 10.0.2.4) 做為主要的 DNS 伺服器。 IIS01 將 DNS 要求傳送至 DNS01 中。
1. UDR 路由輸出流量流往防火牆作為下一個躍點。
1. 沒有輸出 NSG 規則會繫結至前端子網路。 允許的流量。
1. 防火牆會執行處理規則：
   1. 防火牆規則 1 (FW Mgmt) 不適用。 移至下一步 的規則。
   1. 防火牆規則 2-5 （RDP 規則） 不適用。 移至下一步 的規則。
   1. 防火牆規則 6 & 7 （應用程式規則） 不適用。 移至下一步 的規則。
   1. 防火牆規則 8 （到網際網路） 不適用。 移至下一步 的規則。
   1. 適用防火牆規則 9 (DNS)。 允許的流量。 防火牆轉送流量到 10.0.2.4 (DNS01)。
1. 後端子網路會執行處理輸入的規則：
   1. NSG 規則 1 （封鎖網際網路） 不適用。 移至下一步 的規則。
   1. 預設的 NSG 規則允許至子網路的子網路的流量。 允許的流量。 停止處理 NSG 規則。
1. DNS 伺服器收到的要求。
1. DNS 伺服器沒有快取的位址，而且會要求在網際網路上的根 DNS 伺服器。
1. UDR 會做為下一個躍點，將輸出流量傳送至防火牆。
1. 有允許流量的 「 後端 」 子網路上沒有輸出 NSG 規則。
1. 防火牆會執行處理規則：
   1. 防火牆規則 1 (FW Mgmt) 不適用。 移至下一步 的規則。
   1. 防火牆規則 2-5 （RDP 規則） 不適用。 移至下一步 的規則。
   1. 防火牆規則 6 & 7 （應用程式規則） 不適用。 移至下一步 的規則。
   1. 適用防火牆規則 8 （到網際網路）。 允許的流量。 工作階段會透過 SNAT 重新路由至網際網路上的根 DNS 伺服器。
1. 網際網路 DNS 伺服器回應。 此工作階段已從防火牆起始，所以回應會被防火牆接受。
1. 此工作階段已建立，防火牆會轉送回應到起始的伺服器 DNS01。
1. 後端子網路會執行處理輸入的規則：
    1. NSG 規則 1 （封鎖網際網路） 不適用。 移至下一步 的規則。
    1. 預設的 NSG 規則允許允許此流量的子網路的子網路流量。 停止處理 NSG 規則。
1. DNS 伺服器會接收和快取回應，並再回應到 IIS01 將初始要求。
1. 在後端子網路的 UDR 路由讓防火牆成為下一個躍點。
1. 在後端 」 子網路上的沒有輸出 NSG 規則存在，因此允許流量。
1. 此工作階段已經會建立在防火牆上，讓防火牆 device-mapper IIS 伺服器的回應。
1. 前端子網路會執行處理輸入的規則：
    1. 任何適用於從後端子網路與前端子網路的輸入流量的 NSG 規則，因此沒有任何 NSG 規則會套用。
    1. 預設系統規則會允許子網路之間的流量。 允許的流量。
1. IIS01 從 DNS01 接收回應。

### <a name="allowed-back-end-server-to-front-end-server"></a>（允許）前端伺服器的後端伺服器

1. 系統管理員身分登入 AppVM02 透過 RDP 直接從 windows 檔案總管 透過 IIS01 伺服器要求檔案。
1. 在後端子網路的 UDR 路由讓防火牆成為下一個躍點。
1. 所以允許回應，因此後端的子網路上沒有輸出 NSG 規則。
1. 防火牆會執行處理規則：
   1. 防火牆規則 1 (FW Mgmt) 不適用。 移至下一步 的規則。
   1. 防火牆規則 2-5 （RDP 規則） 不適用。 移至下一步 的規則。
   1. 防火牆規則 6 & 7 （應用程式規則） 不適用。 移至下一步 的規則。
   1. 防火牆規則 8 （到網際網路） 不適用。 移至下一步 的規則。
   1. 防火牆規則 9 (DNS) 不適用。 移至下一步 的規則。
   1. 適用防火牆規則 10 （內部子網路）。 允許的流量。 防火牆會將流量傳遞至 10.0.1.4 (IIS01)。
1. 前端子網路開始處理輸入的規則：
   1. NSG 規則 1 （封鎖網際網路） 不適用，移至下一個規則
   1. 預設的 NSG 規則允許至子網路的子網路流量，因此允許流量。 停止處理 NSG 規則。
1. 假設適當的驗證和授權，IIS01 會接受要求和回應。
1. 前端子網路上的 UDR 路由讓防火牆成為下一個躍點。
1. 因此所以允許回應前端子網路上沒有輸出 NSG 規則。
1. 在防火牆上的這個工作階段已經存在，因此允許此回應。 防火牆將回應傳給 AppVM02。
1. 後端子網路會執行處理輸入的規則：
    1. NSG 規則 1 （封鎖網際網路） 不適用。 移至下一步 的規則。
    2. 預設的 NSG 規則允許至子網路的子網路流量，因此允許流量。 停止處理 NSG 規則。
1. AppVM02 接收回應。

### <a name="denied-internet-direct-to-web-server"></a>（拒絕）網際網路直接到 web 伺服器

1. 網際網路使用者嘗試透過 FrontEnd001.CloudApp.Net 服務存取 IIS01 web 伺服器。
1. 沒有任何端點供 HTTP 流量使用開放讓此流量不會通過雲端服務。 流量不會連線到伺服器。
1. 如果端點是開啟的因為某些原因前, 端子網路上的 NSG （封鎖網際網路） 會封鎖此流量。
1. 最後前, 端子網路的 UDR 路由會傳送任何輸出流量從 IIS01 到防火牆做為下一個躍點。 防火牆會將它視為非對稱流量，並且會捨棄輸出回應。

>因此，有至少三個獨立防禦層，網際網路和 IIS01 之間。 雲端服務防止未經授權或不適當的存取。

### <a name="denied-internet-to-back-end-server"></a>（拒絕）網際網路到後端伺服器

1. 網際網路使用者嘗試透過 BackEnd001.CloudApp.Net 服務存取 AppVM01 上的檔案。
2. 沒有開啟的檔案共用以讓此要求會通過雲端服務的端點。 流量不會連線到伺服器。
3. 如果基於某些原因開啟端點，NSG （封鎖網際網路） 會封鎖此流量。
4. 最後，UDR 路由會傳送任何輸出流量來自 AppVM01 到防火牆做為下一個躍點。 防火牆會將它視為非對稱流量，並且會捨棄輸出回應。

> 因此，有至少三個獨立防禦層，網際網路和 AppVM01 之間。 雲端服務防止未經授權或不適當的存取。

### <a name="denied-front-end-server-to-back-end-server"></a>（拒絕）前端到後端伺服器的伺服器

1. IIS01 遭到入侵，且正在執行惡意程式碼嘗試掃描後端子網路的伺服器。
1. 前端子網路的 UDR 路由會將傳送任何輸出流量從 IIS01 到防火牆做為下一個躍點。 遭到入侵的 VM 無法改變此路由。
1. 防火牆會處理流量。 如果要求 appvm01 或 DNS 伺服器 DNS 查閱，防火牆可能會可能會因為 7 和 9 的防火牆規則允許的流量。 防火牆規則 11 （全部拒絕） 封鎖所有其他流量。
1. 如果您啟用防火牆上的進階的威脅偵測，就無法防止包含已知的簽章或模式，加上旗標的進階的威脅規則的流量。 此量值可以運作，即使根據本文所討論的基本轉送規則允許流量。 本文件不涵蓋進階的威脅偵測。 請參閱您的進階威脅功能的特定網路應用裝置的廠商文件。

### <a name="denied-internet-dns-lookup-on-dns-server"></a>(拒絕) DNS 伺服器上的網際網路 DNS 查閱

1. 網際網路使用者嘗試查閱 DNS01 內部 DNS 記錄透過 BackEnd001.CloudApp.Net 服務。
1. 因為沒有開啟以供 DNS 流量的端點，此流量不會通過雲端服務。 它不會連線到伺服器。
1. 如果端點是開啟的因為某些原因前, 端子網路上的 NSG 規則 （封鎖網際網路） 會封鎖此流量。
1. 最後後, 端的子網路 UDR 路由會傳送任何輸出流量從 DNS01 到防火牆做為下一個躍點。 防火牆會將這視為非對稱流量，並且會捨棄輸出回應。

> 因此，有至少三個獨立防禦層，網際網路和 DNS01 之間。 雲端服務防止未經授權或不適當的存取。

#### <a name="denied-internet-to-sql-access-through-firewall"></a>（拒絕）網際網路，以透過防火牆的 SQL 存取

1. 網際網路使用者從 SecSvc001.CloudApp.Net 網際網路面向雲端服務要求 SQL 資料。
1. 沒有任何端點用於 SQL 的開放讓此流量會通過雲端服務。 它不會到達防火牆。
1. 如果基於某些原因開放 SQL 端點，防火牆就會執行處理規則：
   1. 防火牆規則 1 (FW Mgmt) 不適用。 移至下一步 的規則。
   1. 防火牆規則 2-5 （RDP 規則） 不適用。 移至下一步 的規則。
   1. 防火牆規則 6 & 7 （應用程式規則） 不適用。 移至下一步 的規則。
   1. 防火牆規則 8 （到網際網路） 不適用。 移至下一步 的規則。
   1. 防火牆規則 9 (DNS) 不適用。 移至下一步 的規則。
   1. 防火牆規則 10 （內部子網路） 不適用。 移至下一步 的規則。
   1. 防火牆規則 11 （全部拒絕） 會套用。 封鎖流量。 停止處理規則。

## <a name="references"></a>參考

本節包含下列項目：

* 完整的指令碼。 請將它儲存在 PowerShell 指令碼檔案。
* 網路組態。請將它儲存到名為 NetworkConf2.xml 檔案。

修改使用者定義的變數中所需的檔案。 執行指令碼，並依照稍早在本文中所列的防火牆規則設定指示。

### <a name="full-script"></a>完整指令碼

設定後的使用者定義變數，請執行這個指令碼：

1. 連線到 Azure 訂用帳戶
1. 建立新的儲存體帳戶
1. 建立新的虛擬網路和三個子網路的網路組態檔中所定義
1. 建置五部虛擬機器： 防火牆和四個 Windows Server Vm
1. 設定 UDR:
   1. 建立兩個新的路由表
   1. 將路由新增至路由表
   1. 將路由表繫結至適當子網路
1. 啟用 NVA 上的 IP 轉送
1. 設定 NSG:
   1. 建立 NSG
   1. 新增規則
   1. 將 NSG 繫結至適當的子網路

執行這個 PowerShell 指令碼在本機的網際網路連線電腦或伺服器。

> [!IMPORTANT]
> 當您執行此指令碼時，警告或其他參考的訊息可能會出現在 PowerShell 中。 只有紅色的錯誤訊息都必須注意的問題。

```powershell
    <#
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwarding from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Variables or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) {
            Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Associate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the FrontEnd and BackEnd subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install BackEnd resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

### <a name="network-config-file"></a>網路組態檔

儲存此 XML 檔案以更新的位置。 變更`$NetworkConfigFile`完整上述指令碼連結至已儲存的網路組態檔中使用者定義變數。

```xml
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

## <a name="next-steps"></a>後續步驟

您可以安裝的範例應用程式，以協助進行此周邊網路範例。

> [!div class="nextstepaction"]
> [範例應用程式指令碼](./virtual-networks-sample-app.md)

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "具有 NVA、NSG 和 UDR 的雙向 DMZ"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "防火墙规则的逻辑视图"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "建立前端網路物件"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "建立 DNS 伺服器物件"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "預設 RDP 規則的複本"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "AppVM01 規則"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "應用程式重新導向圖示"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "目标 NAT 图标"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "傳遞圖示"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "防火牆管理規則"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "防火牆 RDP 規則"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "防火牆 Web 規則"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "防火牆 AppVM01 規則"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "防火牆輸出規則"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "防火牆 DNS 規則"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "防火牆內部 VNet 規則"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "防火牆拒絕規則"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "防火牆規則啟用"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
