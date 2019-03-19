---
title: 周邊網路範例 – 建置周邊網路來保護應用程式具有防火牆和 Nsg |Microsoft Docs
description: 建置具有防火牆的周邊網路和網路安全性群組 (Nsg)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: e0271c9212b093bd803518ebeaa4b7d9682cc773
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997459"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>範例 2：： 建置周邊網路，透過防火牆和 Nsg 保護應用程式
[返回 Microsoft 雲端服務和網路安全性 頁面][HOME]

此範例示範如何建立周邊網路 (也稱為*DMZ*，*非軍事區域*，並*遮蔽式子網路*) 防火牆時，四個 Windows Server 電腦，與網路安全性群組 (Nsg)。 它包含相關命令，以提供更深入的了解每個步驟的詳細資訊。 < 流量案例 > 一節提供如何流量流經周邊網路中的防禦層的逐步說明。 最後，[參考] 區段提供的完整程式碼和指示如何建置此環境來測試和試驗不同的案例。

![具有 NVA 和 Nsg 的輸入的周邊網路][1]

## <a name="environment"></a>環境 
這個範例根據包含下列項目的 Azure 訂用帳戶的案例：

* 兩個雲端服務：FrontEnd001 和 BackEnd001。
* 名為 CorpNetwork 具有兩個子網路的虛擬網路：前端和後端。
* 單一 NSG 套用至這兩個子網路。
* 網路虛擬設備：Barracuda NextGen 防火牆連接到 FrontEnd 子網路。
* 代表應用程式 web 伺服器在 Windows Server 電腦：IIS01.
* 代表應用程式後端伺服器的兩個 Windows Server 電腦：AppVM01 並 AppVM02。
* 代表 DNS 伺服器在 Windows Server 電腦：DNS01.

> [!NOTE]
> 雖然此範例使用 Barracuda NextGen 防火牆，就可以使用許多網路虛擬設備。
> 
> 

這篇文章的 < 參考資料 > 一節中的 PowerShell 指令碼建立的大部分此處所述的環境。 Vm 和虛擬網路所建立的指令碼，但這些處理序不在這份文件中詳細描述。

若要建置環境：

1. 儲存 < 參考資料 > 一節中的網路組態 XML 檔 （更新名稱、 位置、 與 IP 位址，以符合您的案例）。
2. 更新的使用者定義的變數，在 PowerShell 指令碼，以符合指令碼會執行對 （訂用帳戶、 服務名稱等等） 的環境。
3. 在 PowerShell 中執行指令碼。

> [!NOTE]
> PowerShell 指令碼中指定的區域必須符合網路組態 XML 檔中指定的區域。
>
>

指令碼順利執行之後，您可以完成下列步驟：

1. 設定防火牆規則。 請參閱這篇文章的 「 防火牆規則 」 一節。
2. 如果您想，您可以設定 web 伺服器和具有簡單 web 應用程式，以允許周邊網路設定與測試應用程式伺服器。 您可以使用 [參考] 區段中提供的兩個應用程式指令碼。

下節將說明大部分的 Nsg 與相關的指令碼的陳述式。

## <a name="nsgs"></a>NSG
此範例會組建 NSG 群組，然後載入六個規則。

> [!TIP]
> 一般情況下，您應該先建立特定的 「 允許 」 規則，和較一般的 「 拒絕 」 規則的最後一個。 先評估規則指派的優先順序控制項。 流量會找到可套用至特定的規則之後，會不評估任何其他規則。 NSG 規則可以套用在輸入或輸出方向 （從子網路的觀點來看）。
> 
> 

以宣告方式，這些規則內建的輸入流量：

1. 允許內部 DNS 流量 (連接埠 53)。
2. 允許 RDP 流量 （連接埠 3389） 從網際網路到任何 VM。
3. 允許 HTTP 流量 （連接埠 80） 從網際網路到 NVA （防火牆）。
4. 允許所有流量 （所有連接埠） 從 IIS01 到 AppVM01。
5. 拒絕所有流量 （所有連接埠） 從網際網路到整個虛擬網路 （兩個子網路）。
6. 拒絕所有流量 （所有連接埠） 從 FrontEnd 子網路到 BackEnd 子網路。

這些規則繫結至每個子網路，如果 HTTP 要求是從網際網路到 web 伺服器中，輸入兩者規則 3 （允許） 和規則 5 （拒絕） 似乎適用，但由於規則 3 具有較高的優先順序，只有它會套用。 規則 5 不會派上用場。 因此防火牆會允許 HTTP 要求。

如果相同的流量嘗試抵達 DNS01 伺服器，規則 5 （拒絕） 會先適用，，因此不會允許流量傳遞至伺服器。 規則 6 （拒絕） 封鎖 FrontEnd 子網路與 BackEnd 子網路 （除了在規則 1 和 4 允許的流量） 的通訊。 萬一攻擊者危害前端的 web 應用程式，這可以保護後端網路。 在此情況下，攻擊者會有有限存取權後端 「 受保護 」 網路。 （攻擊者能夠存取 AppVM01 伺服器上公開的資源。）

沒有預設的輸出規則可允許流量外流到網際網路。 在此範例中，我們允許輸出流量，不會修改任何輸出規則。 若要鎖定兩個方向的流量，您需要使用者定義的路由。 您可以了解在不同的範例，在這項技術[主要安全性界限文件][HOME]。

此處所述的 NSG 規則中的 NSG 規則如下[範例 1-建置具有 Nsg 的簡單 DMZ][Example1]。 檢閱中，每個 NSG 規則和其屬性以詳細查看該文章的 NSG 說明。

## <a name="firewall-rules"></a>防火牆規則
您要管理防火牆，並建立所需的組態的電腦上安裝管理用戶端。 請參閱文件從您的防火牆 （或其他 NVA） 廠商有關如何管理裝置。 本節的其餘部分會說明設定防火牆本身，透過廠商的管理用戶端 （不 Azure 入口網站或 PowerShell）。

請參閱[Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)的用戶端下載及連接到此範例中使用的 Barracuda 防火牆的指示。

您需要在防火牆上建立轉送規則。 因為案例中的，在此範例中只會路由傳送的網際網路流量傳入到防火牆，並再到 web 伺服器，您只需要一個轉送 NAT 規則。 在此範例中使用的 Barracuda 防火牆，此規則會是目的地 NAT 規則 (Dst NAT) 傳送此流量。

若要建立下列規則 （或驗證現有的預設規則），完成下列步驟：
1. Barracuda NG Admin 用戶端儀表板中，[設定] 索引標籤中，在**作業組態**區段中，選取**Ruleset**。 

   呼叫方格**主要規則**顯示現有的使用中狀態，並且停用在防火牆上的規則。

2. 若要建立新的規則，請選取 小型的綠色**+** 此方格右上角的按鈕。 （您的防火牆可能會遭到鎖定。 如果您看到標示的按鈕**鎖定**而無法建立或編輯規則，請選取 解除鎖定該 ruleset，並允許 編輯 按鈕。)
  
3. 若要編輯現有的規則，選取規則、 按一下滑鼠右鍵，然後選取**編輯規則**。

建立新的規則，名為類似**WebTraffic。** 

目的地 NAT 規則圖示看起來像這樣：![目的地 NAT 圖示][2]

規則本身會看起來像這樣：

![防火牆規則][3]

將傳出防火牆的 DHCP1 本機 IP 介面叫用防火牆嘗試連線到 HTTP （連接埠 80 或 443 為 HTTPS） 的任何輸入的位址，並將其重新導向至 IP 位址 10.0.1.5 的 web 伺服器。 因為傳入的連接埠 80 和連接埠 80 上前往 web 伺服器的流量，將不需要任何連接埠變更。 但是，目標清單可能已經被 10.0.1.5:8080 如果會將轉譯的輸入連接埠 80 在防火牆上的輸入連接埠 8080 上的 web 伺服器的連接埠 8080 上接聽的 web 伺服器。

您也應該指定的連接方法。 從網際網路目的地規則，動態 SNAT 會是最適當的方法。

即使您只建立一個規則，務必正確設定其優先順序。 在防火牆上的所有規則的方格中，如果這個新的規則位於底部 （在 BLOCKALL 規則），它將永遠不會派上用場。 請確定新的 web 流量的規則在 BLOCKALL 規則之上。

建立規則之後，您需要將它推送至防火牆並將然後將它啟用。 如果您未採取這些步驟，在規則變更才會生效。 下一節會說明推送和啟用程序。

## <a name="rule-activation"></a>規則啟用
現在，將規則新增至規則集，您要上傳至防火牆的規則集並將它啟用。

![防火牆規則啟用][4]

在右上方的 管理用戶端，您會看到一組按鈕。 選取 **傳送變更**以將修改過的規則集傳送到防火牆，然後選取**Activate**。

既然您已啟用防火牆規則集，在環境已完成。 如果您想，您可以在 [參考] 區段，將應用程式新增至環境中執行範例應用程式指令碼。 如果您新增應用程式時，您可以測試流量案例下一節中所述。

> [!IMPORTANT]
> 您應該了解您不會直接到達 web 伺服器。 在瀏覽器從 FrontEnd001.CloudApp.Net 要求 HTTP 頁面，HTTP 端點 （連接埠 80） 會傳遞到防火牆，而非 web 伺服器流量。 防火牆接著，您稍早建立的規則，因為使用 NAT 來將要求對應至 web 伺服器。
> 
> 

## <a name="traffic-scenarios"></a>流量案例
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>（允許）Web 到 web 伺服器通過防火牆
1. 網際網路使用者從 FrontEnd001.CloudApp.Net （網際網路面向雲端服務） 要求 HTTP 頁面。
2. 雲端服務會將流量透過開放的端點連接埠 80 上傳遞至 10.0.1.4:80 上的防火牆本機介面。
3. FrontEnd 子網路開始處理輸入的規則：
   1. NSG 規則 1 (DNS) 不適用。 移至下一個規則。
   2. NSG 規則 2 (RDP) 不適用。 移至下一個規則。
   3. 適用 NSG 規則 3 （網際網路到防火牆）。 允許的流量。 停止處理規則。
4. 流量會叫用防火牆 (10.0.1.4) 的內部 IP 的位址。
5. 防火牆轉送規則會判斷這是連接埠 80 的流量，並將它重新導向到 web 伺服器 IIS01。
6. IIS01 正在接聽 web 流量，接收要求時，並開始處理要求。
7. IIS01 要求 AppVM01 上的 SQL Server 執行個體資訊。
8. 因此允許流量 FrontEnd 子網路上沒有輸出規則。
9. BackEnd 子網路開始處理輸入的規則：
   1. NSG 規則 1 (DNS) 不適用。 移至下一個規則。
   2. NSG 規則 2 (RDP) 不適用。 移至下一個規則。
   3. NSG 規則 3 （網際網路到防火牆） 不適用。 移至下一個規則。
   4. NSG 規則 4 (IIS01 到 AppVM01) 適用。 允許的流量。 停止處理規則。
10. AppVM01 上的 SQL Server 執行個體接收要求，並回應。
11. 因為 BackEnd 子網路上沒有輸出規則，所以允許回應。
12. FrontEnd 子網路開始處理輸入的規則：
    1. 沒有 NSG 規則套用至輸入 BackEnd 子網路到 FrontEnd 子網路的流量，因此沒有任何 NSG 規則套用。
    2. 允許子網路之間流量的預設系統規則會允許此流量，因此允許流量。
13. IIS01 會接收來自 AppVM01 的回應、 完成 HTTP 回應，並將它傳送給要求者。
14. 因為這是從防火牆的 NAT 工作階段，所以回應目的地一開始是防火牆。
15. 防火牆接收來自 web 伺服器的回應，並將它轉送至網際網路的使用者。
16. 因為 FrontEnd 子網路上沒有輸出規則，所以允許回應，以及網際網路使用者會收到的網頁。

#### <a name="allowed-rdp-to-backend"></a>（允許）RDP 到 BackEnd
1. 在網際網路上的伺服器系統管理員要求在 BackEnd001.CloudApp.Net AppVM01 的 RDP 工作階段：*xxxxx*，其中*xxxxx*是 rdp 到 AppVM01 的隨機指派連接埠。 （您可以找到指派的連接埠，在 Azure 入口網站或 PowerShell）。
2. 防火牆只在 FrontEnd001.CloudApp.Net 位址上接聽，因為它並未包含與此流量。
3. BackEnd 子網路開始處理輸入的規則：
   1. NSG 規則 1 (DNS) 不適用。 移至下一個規則。
   2. 適用 NSG 規則 2 (RDP)。 允許的流量。 停止處理規則。
4. 因為沒有輸出規則，所以預設規則會套用，並傳回允許的流量。
5. 已啟用 RDP 工作階段。
6. AppVM01 會提示輸入使用者名稱和密碼。

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>（允許）DNS 伺服器上的 web 伺服器 DNS 查閱
1. Web 伺服器 IIS01 需要 www.data.gov 於摘要資料，但需要解析位址。
2. 網路組態的虛擬網路顯示 DNS01 (BackEnd 子網路上的 10.0.2.4) 做為主要的 DNS 伺服器。 IIS01 將 DNS 要求傳送至 DNS01 中。
3. 因為 FrontEnd 子網路上沒有輸出規則，允許流量。
4. BackEnd 子網路開始處理輸入的規則：
   1. 已套用的 NSG 規則 1 (DNS)。 允許的流量。 停止處理規則。
5. DNS 伺服器收到的要求。
6. DNS 伺服器沒有快取的位址，而且查詢在網際網路上的根 DNS 伺服器。
7. 因為 BackEnd 子網路上沒有輸出規則，允許流量。
8. 網際網路 DNS 伺服器回應。 因為從內部起始工作階段，所以允許回應。
9. DNS 伺服器快取回應，並會回應來自 IIS01 的要求。
10. 因為 BackEnd 子網路上沒有輸出規則，允許流量。
11. FrontEnd 子網路開始處理輸入的規則：
    1. 沒有 NSG 規則套用至輸入 BackEnd 子網路到 FrontEnd 子網路的流量，因此沒有任何 NSG 規則套用。
    2. 允許子網路之間流量的預設系統規則會允許此流量，因此允許流量。
12. IIS01 從 DNS01 接收回應。

#### <a name="allowed-web-server-file-access-on-appvm01"></a>（允許）AppVM01 上的網頁伺服器檔案存取
1. IIS01 要求 AppVM01 上的檔案。
2. 因為 FrontEnd 子網路上沒有輸出規則，允許流量。
3. BackEnd 子網路開始處理輸入的規則：
   1. NSG 規則 1 (DNS) 不適用。 移至下一個規則。
   2. NSG 規則 2 (RDP) 不適用。 移至下一個規則。
   3. NSG 規則 3 （網際網路到防火牆） 不適用。 移至下一個規則。
   4. NSG 規則 4 (IIS01 到 AppVM01) 適用。 允許的流量。 停止處理規則。
4. AppVM01 接收要求，並回應 （假設已獲授權存取） 的檔案。
5. 因為 BackEnd 子網路上沒有輸出規則，所以允許回應。
6. FrontEnd 子網路開始處理輸入的規則：
   1. 沒有 NSG 規則套用至輸入 BackEnd 子網路到 FrontEnd 子網路的流量，因此沒有任何 NSG 規則套用。
   2. 允許子網路之間流量的預設系統規則會允許此流量，因此允許流量。
7. IIS01 會接收檔案。

#### <a name="denied-web-direct-to-web-server"></a>（拒絕）Web 直接到 web 伺服器
Web 伺服器 IIS01 和防火牆都在相同的雲端服務，因為它們會共用相同的公開 IP 位址。 因此任何 HTTP 流量會導向至防火牆。 雖然會成功處理要求，它不能直接前往 web 伺服器。 它會傳遞，依照設計，透過防火牆第一次。 在本節中的流量流程的第一個案例，請參閱。

#### <a name="denied-web-to-backend-server"></a>（拒絕）Web 到 BackEnd 伺服器
1. 網際網路使用者嘗試透過 BackEnd001.CloudApp.Net 服務存取 AppVM01 上的檔案。
2. 因為沒有開啟的檔案共用的端點，這不會通過雲端服務，並不會連線到伺服器。
3. 如果基於某些原因開啟端點，NSG 規則 5 （網際網路到虛擬網路） 會封鎖流量。

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>（拒絕）DNS 伺服器上的 web DNS 查閱
1. 網際網路使用者嘗試查閱 DNS01 內部 DNS 記錄透過 BackEnd001.CloudApp.Net 服務。
2. 因為沒有用於 DNS 的開放端點，這不會通過雲端服務，並不會連線到伺服器。
3. 如果基於某些原因開啟端點，NSG 規則 5 （網際網路到虛擬網路） 會封鎖流量。 （規則 1 [DNS] 不適用的原因有二。 首先，來源位址是網際網路，並套用此規則只有本機虛擬網路時，來源。 第二，此規則是允許規則，讓它永遠不會拒絕流量）。

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>（拒絕）若要透過防火牆的 SQL 存取 web
1. 網際網路使用者從 FrontEnd001.CloudApp.Net （網際網路面向雲端服務） 要求 SQL 資料。
2. 因為沒有用於 SQL 的開放端點，這不會通過雲端服務，並不會到達防火牆。
3. 如果基於某些原因開啟端點，FrontEnd 子網路開始處理輸入的規則：
   1. NSG 規則 1 (DNS) 不適用。 移至下一個規則。
   2. NSG 規則 2 (RDP) 不適用。 移至下一個規則。
   3. 適用 NSG 規則 3 （網際網路到防火牆）。 允許的流量。 停止處理規則。
4. 流量會叫用防火牆 (10.0.1.4) 的內部 IP 的位址。
5. 防火牆轉送規則的 SQL，因此捨棄流量。

## <a name="conclusion"></a>結論
這個範例示範以保護您的應用程式防火牆，並隔離後的端子網路與輸入流量的方式相當直接簡單。

您可以找到更多範例和網路的概觀安全性界線[此處][HOME]。

## <a name="references"></a>參考
### <a name="full-script-and-network-config"></a>完整的指令碼和網路設定
將完整的指令碼儲存在 PowerShell 指令碼檔案。 將網路組態指令碼儲存在名為 NetworkConf2.xml。
視需要變更使用者定義變數。 執行指令碼，並依照這篇文章的 「 防火牆規則 」 一節中的指示。

#### <a name="full-script"></a>完整指令碼
此指令碼，根據使用者定義的變數，將完成下列步驟：

1. 連接到 Azure 訂用帳戶。
2. 建立儲存體帳戶。
3. 建立虛擬網路和兩個子網路，網路組態檔中所定義。
4. 建立四個 Windows Server Vm。
5. 設定 NSG。 設定完成下列步驟：
   * 建立 NSG。
   * 填入的 NSG 規則。
   * 將 NSG 繫結至適當的子網路。

您應該在連接網際網路的電腦或伺服器上的本機執行此 PowerShell 指令碼。

> [!IMPORTANT]
> 當您執行此指令碼時，警告和其他資訊的訊息可能會出現在 PowerShell 中。 您只需要關心會以紅色顯示的錯誤訊息。
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5

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

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

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
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

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

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
        Write-Host "Creating Services" -ForegroundColor Cyan
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
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>網路組態檔
使用更新的位置，儲存此 XML 檔案，然後將連結新增至此檔案中前面的指令碼的 $NetworkConfigFile 變數。

```xml
    <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

#### <a name="sample-application-scripts"></a>範例應用程式指令碼
如果您想要安裝範例應用程式，此對話方塊和其他周邊網路範例，請參閱[範例應用程式指令碼][SampleApp]。

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "具有 NSG 的輸入 DMZ"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "目的地 NAT 圖示"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "防火牆規則"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "防火墙规则激活"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
