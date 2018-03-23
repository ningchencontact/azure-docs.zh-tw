---
title: 路由傳送網路流量 - Azure 入口網站 | Microsoft Docs
description: 了解如何使用 Azure 入口網站以路由表路由傳送網路流量。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 4cc814e1fd3ee8979662695f9dec3dcce335dc2a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>使用 Azure 入口網站以路由表路由傳送網路流量

根據預設，Azure 會自動路由傳送虛擬網路內所有子網路之間的流量。 您可以建立您自己的路由，以覆寫 Azure 的預設路由。 舉例來說，如果您想要通過防火牆路由傳送子網路之間的流量，則建立自訂路由的能力很有幫助。 在本文中，您將了解如何：

> [!div class="checklist"]
> * 建立路由表
> * 建立路由
> * 讓路由表與虛擬網路子網路產生關聯
> * 測試路由
> * 針對路由進行疑難排解

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure"></a>登入 Azure 

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="create-a-route-table"></a>建立路由表

根據預設，Azure 會路由傳送虛擬網路內所有子網路之間的流量。 若要深入了解 Azure 的預設路由，請參閱[系統路由](virtual-networks-udr-overview.md)。 若要覆寫 Azure 的預設路由，您可建立內含路由的路由表，並且將路由表與虛擬網路子網路產生關聯。

1. 選取 Azure 入口網站左上角的 [+ 建立資源]。
2. 選取 [網路]，然後選取 [路由表]。
3. 選取您的 [訂用帳戶]，選取或輸入下列資訊，然後選取 [建立]：
 
    ![建立路由表](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>建立路由

路由表包含零個或多個路由。 

1. 在入口網站頂端的 [搜尋資源、服務和文件] 方塊中，開始輸入 myRouteTablePublic。 當 **myRouteTablePublic** 出現在搜尋結果時，選取它。
2. 選取 [設定] 底下的 [路由]，然後選取 [+新增]，如下圖所示：

    ![新增路由](./media/tutorial-create-route-table-portal/add-route.png) 
 
4. 在 [新增路由] 底下選取或輸入下列資訊，然後選取 [確定]︰
    - **路由名稱**：ToPrivateSubnet
    - **位址首碼**：10.0.1.0/24
    - **下一個躍點類型**：選取 [虛擬設備]。
    - **下一個躍點位址**：10.0.2.4

    路由會透過具有 IP 位址 10.0.2.4 的網路虛擬設備將所有流量導向至 10.0.1.0/24 位址首碼。 在稍後步驟中會建立具有指定位址首碼的網路虛擬設備和子網路。 路由會覆寫 Azure 的預設路由，該預設路由會直接路由傳送子網路之間的流量。 每個路由都會指定下一個躍點類型。 下一個躍點類型會指示 Azure 如何路由傳送流量。 在此範例中，下一個躍點類型是 VirtualAppliance。 若要深入了解 Azure 中所有可用的下一個躍點類型，以及使用它們的時機，請參閱[下一個躍點類型](virtual-networks-udr-overview.md#custom-routes)。

## <a name="associate-a-route-table-to-a-subnet"></a>建立路由表與子網路的關聯

您必須先建立虛擬網路和子網路，才能讓路由表與子網路產生關聯。

1. 選取 Azure 入口網站左上角的 [+ 建立資源]。
2. 選取 [網絡]，然後選取 [虛擬網路]。
3. 在 [建立虛擬網路] 底下選取或輸入下列資訊，然後選取 [建立]：
    
    - **名稱**：myVirtualNetwork
    - **位址空間**：*10.0.0.0/16*
    - **訂用帳戶**︰選取您的訂用帳戶。
    - **資源群組**：選取 [使用現有] 並選取 **myResourceGroup**。
    - **位置**：選取 [美國東部]
    - **子網路名稱**：公用
    - **位址範圍**：10.0.0.0/24

4. 在入口網站頂端的 [搜尋資源、服務和文件] 方塊中，開始輸入 myVirtualNetwork。 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。
5. 將額外兩個子網路新增至虛擬網路。 在 [設定] 底下選取 [子網路]，然後選取 [+ 子網路]，如下圖所示：

    ![新增子網路](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. 選取或輸入下列資訊，然後選取 [確定]︰
    - **名稱**：私用
    - **位址空間**：10.0.1.0/24

7. 再次完成步驟 5 和 6，以提供下列資訊：
    - **名稱**：DMZ
    - **位址空間**：10.0.2.0/24

您可以將路由表與零個或多個子網路建立關聯。 子網路可以有零個或一個與其相關聯的路由表。 來自子網路的輸出流量會根據 Azure 的預設路由，以及您新增至與子網路相關聯之路由表的自訂路由來進行路由傳送。 讓 myRouteTablePublic 路由表與「公用」子網路產生關聯：

1. [myVirtualNetwork - 子網路] 方塊會在完成上一個步驟之後顯示。 在 [設定] 底下選取 [子網路]，然後選取 [公用]。
2. 如下圖所示，選取 [路由表]，然後選取 [MyRouteTablePublic]。

    ![關聯路由表](./media/tutorial-create-route-table-portal/associate-route-table.png) 

3. 選取 [ **儲存**]。

## <a name="test-routing"></a>測試路由

若要測試路由，您要建立一個作為網路虛擬設備的虛擬機器，讓您在上一個步驟中建立的路由通過。 建立網路虛擬設備之後，您要將虛擬機器部署到「公用」和「私人」子網路。 然後透過網路虛擬設備將「公用」子網路的流量路由傳送至「私人」子網路。

### <a name="create-a-network-virtual-appliance"></a>建立網路虛擬設備

在上一個步驟中，您建立的路由將網路虛擬設備指定為下一個躍點類型。 執行網路應用程式的虛擬機器通常稱為網路虛擬設備。 在生產環境中，您部署的網路虛擬設備通常是預先設定的虛擬機器。 從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) 可取得數個網路虛擬設備。 在本文中會建立基本虛擬機器。

1. 選取 Azure 入口網站左上角的 [+ 建立資源]。
2. 選取 [計算]，然後選取 [Windows Server 2016 Datacenter]。 您可以選取不同的作業系統，但是其餘步驟假設您選取的是 **Windows Server 2016 Datacenter**。 
3. 針對 [基本] 選取或輸入下列資訊，然後選取 [確定]︰
    - **名稱**：myVmNva
    - **資源群組**：選取 [使用現有的] 然後選取 [myResourceGroup]。
    - **位置**：選取 [美國東部]。

    您輸入的 [使用者名稱] 和 [密碼] 會在稍後的步驟中使用到。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。 所選取的 [位置] 和 [訂用帳戶] 必須與虛擬網路所在的位置和訂用帳戶相同。 您不需要選取虛擬網路建立所在的相同資源群組，但是本教學課程會選取相同的資源群組。
4. 在 [選擇大小] 底下選取虛擬機器大小。
5. 針對 [設定] 選取或輸入下列資訊，然後選取 [確定]︰
    - **虛擬網路**：確定有選取 [myVirtualNetwork]。 若未選取，請選取 [虛擬網路]，然後在 [選擇虛擬網路] 底下選取 [myVirtualNetwork]。
    - **子網路**：選取 [子網路]，然後在 [選擇子網路] 底下選取 [DMZ]。
    - **公用 IP 位址**：選取 [公用 IP 位址]，然後在 [選擇公用 IP 位址] 底下選取 [無]。 因為不會從網際網路連線到此虛擬機器，所以不會將公用 IP 位址指派給它。
6. 在 [摘要] 中的 [建立] 底下，選取 [建立] 來開始虛擬機器部署。

建立虛擬機器需要幾分鐘的時間。 在 Azure 完成虛擬機器建立並開啟具有虛擬機器相關資訊的方塊之前，請勿繼續進行下一個步驟。

當 Azure 建立虛擬機器時，它也會在 DMZ 子網路中建立[網路介面](virtual-network-network-interface.md)，並且將網路介面連結至虛擬機器。 必須為每個 Azure 網路介面啟用 IP 轉送，以轉送未指派給網路介面之任何 IP 位址的預定流量。

1. 在建立虛擬機器之後開啟的方塊中，選取 [設定] 底下的 [網路]，然後選取 [myvmnva158] (Azure 為您的虛擬機器建立的網路介面在 **myvmnva** 後面有不同的數字)，如下圖所示：

    ![虛擬機器網路](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

    當您在 DMZ 子網路中建立網路虛擬設備時，Azure 會自動建立網路介面、將網路介面連結至虛擬機器，以及將私人 IP 位址 10.0.2.4 指派給網路介面。 

2. 在 [設定] 底下選取 [IP 組態]，針對 [IP 轉送] 選取 [已啟用]，然後選取 [儲存]，如下圖所示：

    ![啟用 IP 轉送](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

### <a name="create-virtual-machines"></a>建立虛擬機器

在虛擬網路中建立兩部虛擬機器，您就可以在後續步驟中驗證來自「公用」子網路的流量是否透過網路虛擬設備路由傳送至「私人」子網路。

完成[建立網路虛擬設備](#create-a-network-virtual-appliance)的步驟 1-6。 在步驟 3 和 5 中使用相同的設定，下列變更除外：

|虛擬機器   |Name      |子網路      | 公用 IP 位址     |
|---------         |--------- | -----------|---------              |
|虛擬機器 1 | myVmWeb  | 公開     | 接受入口網站預設值 |
|虛擬機器 2 | myVmMgmt | 私人    | 接受入口網站預設值 |

您可以在 Azure 建立 myVmWeb 虛擬機器時建立 myVmMgmt 虛擬機器。 在 Azure 完成兩部虛擬機器建立之前，請勿繼續進行後續步驟。

### <a name="route-traffic-through-a-network-virtual-appliance"></a>透過網路虛擬設備來路由傳送流量

1. 在入口網站頂端的 [搜尋] 方塊中，開始輸入 myVmMgmt。 當 **myVmMgmt** 出現在搜尋結果中時，請加以選取。
2. 藉由選取 [連線] 來建立與 myVmMgmt 虛擬機器的遠端桌面連線，如下圖所示：

    ![連線至虛擬機器](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. 若要連線至虛擬機器，請開啟所下載的 RDP 檔案。 如果出現提示，請選取 [連接]。
4. 輸入您在建立虛擬機器時指定的使用者名稱和密碼 (您可能需要選取 [更多選擇]，然後選取 [使用不同的帳戶] 以指定您在建立虛擬機器時輸入的認證)，然後選取 [確定]。
5. 您可能會在登入過程中收到憑證警告。 選取 [是] 以繼續進行連線。
6. 在稍後步驟中，tracert.exe 命令用於測試路由。 Tracert 會使用 ICMP，它不得通過 Windows 防火牆。 從命令提示字元中輸入下列命令，讓 ICMP 通過 Windows 防火牆：

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    雖然本文使用 tracert 來測試路由，但不建議在生產環境部署中允許 ICMP 通過 Windows 防火牆。
7. 您已在[啟用 IP 轉送](#enable-ip-forwarding)中針對虛擬機器的網路介面啟用在 Azure 內 IP 轉送。 在虛擬機器內，作業系統或在虛擬機器內執行的應用程式也必須能夠轉送網路流量。 當您在生產環境中部署網路虛擬設備時，設備通常會在轉送流量之前篩選、記錄或執行一些其他函式。 但在本文中，作業系統只會轉送它接收的所有流量。 從 myVmMgmt 虛擬機器完成下列步驟，以在 myVmNva 的作業系統內啟用 IP 轉送：

    從命令提示字元使用下列命令，透過遠端桌面連線到 myVmNva 虛擬機器：

    ``` 
    mstsc /v:myvmnva
    ```
    
    若要在作業系統內啟用 IP 轉送，請在 PowerShell 中輸入下列命令：

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    重新啟動虛擬機器，這也會中斷與遠端桌面工作階段的連線。
8. 保持連線到 myVmMgmt 虛擬機器，在 myVmNva 虛擬機器重新啟動之後，使用下列命令建立與 myVmWeb 虛擬機器的遠端桌面工作階段：

    ``` 
    mstsc /v:myVmWeb
    ```
    
    從命令提示字元中輸入下列命令，讓 ICMP 通過 Windows 防火牆：

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

9. 若要測試從 myVmWeb 虛擬機器到 myVmMgmt 虛擬機器的網路流量路由，請從命令提示字元輸入下列命令：

    ```
    tracert myvmmgmt
    ```

    回應如下列範例所示：
    
    ```
    Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    您可以看到第一個躍點是 10.0.2.4，也就是網路虛擬設備的私人 IP 位址。 第二躍點是 10.0.1.4，也就是 myVmMgmt 虛擬機器的私人 IP 位址。 新增至 myRouteTablePublic 路由表且與「公用」子網路產生關聯的路由，會導致 Azure 透過 NVA 路由傳送流量，而不是直接路由傳送到「私人」子網路。
10.  關閉 myVmWeb 虛擬機器的遠端桌面工作階段，但您仍然與 myVmMgmt 虛擬機器連線。
11. 若要測試從 myVmMgmt 虛擬機器到 myVmWeb 虛擬機器的網路流量路由，請從命令提示字元輸入下列命令：

    ```
    tracert myvmweb
    ```

    回應如下列範例所示：

    ```
    Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    您可以看到流量從 myVmMgmt 虛擬機器直接路由傳送至 myVmWeb 虛擬機器。 根據預設，Azure 會直接路由傳送子網路之間的流量。
12. 關閉 myVmMgmt 虛擬機器的遠端桌面工作階段。

## <a name="troubleshoot-routing"></a>針對路由進行疑難排解

如同您在先前步驟中所學，Azure 會套用預設路由，您可以選擇性地使用自己的路由覆寫。 有時候，流量可能無法如您所預期進行路由。 您可以使用 Azure 網路監看員的[下一個躍點](../network-watcher/network-watcher-check-next-hop-portal.md)功能，判斷 Azure 如何路由傳送兩部虛擬機器之間的流量。 

1. 在入口網站頂端的 [搜尋] 方塊中，開始輸入「Network Watcher」。 當搜尋結果中出現**網路監看員**時，請加以選取。
2. 在 [網路診斷工具] 底下，選取 [下一個躍點]。
3. 若要測試從 myVmWeb (10.0.0.4) 虛擬機器到 myVmMgmt (10.0.1.4) 虛擬機器的流量路由，請選取您的訂用帳戶，輸入如下圖所示的資訊 (您的**網路介面**名稱可能稍微不同)，然後選取 [下一個躍點]：

    ![下一個躍點](./media/tutorial-create-route-table-portal/next-hop.png)  

    [結果] 輸出會通知您從 myVmWeb 到 myVmMgmt 之流量的下一個躍點 IP 位址是 10.0.2.4 (myVmNva 虛擬機器)，下一個躍點類型是 VirtualAppliance，而造成路由的路由表是 myRouteTablePublic。

每個網路介面的有效路由是由 Azure 的預設路由和您定義的路由所組成。 若要查看虛擬機器中網路介面的所有有效路由，請完成下列步驟：

1. 在入口網站頂端的 [搜尋] 方塊中，開始輸入 myVmWeb。 當 **myVmWeb** 出現在搜尋結果中時，選取它。
2. 在 [設定] 底下選取 [網路]，然後選取 [myvmweb369] (Azure 為您的虛擬機器建立的網路介面在 **myvmweb** 後面有不同的數字)。
3. 在 [支援與疑難排解] 底下選取 [有效路由]，如下圖所示︰

    ![有效路由](./media/tutorial-create-route-table-portal/effective-routes.png) 

    您會看到 Azure 的預設路由和您在 myRouteTablePublic 路由表中新增的路由。 若要深入了解 Azure 如何從路由清單中選取路由，請參閱 [Azure 如何選取路由](virtual-networks-udr-overview.md#how-azure-selects-a-route)。

## <a name="clean-up-resources"></a>清除資源

當不再需要資源群組時，請將資源群組及其包含的所有資源刪除： 

1. 在入口網站頂端的 [搜尋] 方塊中，輸入 myResourceGroup。 當您在搜尋結果中看到 myResourceGroup 時，請加以選取。
2. 選取 [刪除資源群組]。
3. 針對 [輸入資源群組名稱:] 輸入 myResourceGroup，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本文中，您已建立路由表並將其與子網路產生關聯。 您已建立網路虛擬設備，它會將來自公用子網路的流量路由傳送至私人子網路。 雖然您可以在虛擬網路內部署許多 Azure 資源，但是某些 Azure PaaS 服務的資源無法部署到虛擬網路。 您仍可將某些 Azure PaaS 服務的資源存取，限制為僅來自虛擬網路子網路的流量。 請繼續閱讀下一篇文章，以了解如何限制對 Azure PaaS 資源的網路存取。

> [!div class="nextstepaction"]
> [限制對 PaaS 資源的網路存取](virtual-network-service-endpoints-configure.md#azure-portal)
