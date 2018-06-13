---
title: 建立具有多個子網路的 Azure 虛擬網路 - 入口網站 | Microsoft Docs
description: 了解如何使用 Azure 入口網站建立具有多個子網路的虛擬網路。
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 898bdef779282d7312c76696f744b97ec2dfcded
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880478"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-portal"></a>使用 Azure 入口網站建立具有多個子網路的虛擬網路

虛擬網路可讓數種類型的 Azure 資源與網際網路通訊，以及讓資源彼此私密通訊。 在虛擬網路中建立多個子網路可讓您分割網路，以便能夠篩選或控制子網路之間流量的流動。 在本文中，您將了解如何：

> [!div class="checklist"]
> * 建立虛擬網路
> * 建立子網路
> * 測試虛擬機器之間的網路通訊

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure"></a>登入 Azure 

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="create-a-virtual-network"></a>建立虛擬網路

1. 選取 Azure 入口網站左上角的 [+ 建立資源]。
2. 選取 [網絡]，然後選取 [虛擬網路]。
3. 如下圖所示，針對 [名稱] 輸入「myVirtualNetwork」、針對 [位址空間] 輸入「10.0.0.0/16」、針對 [資源群組] 輸入「myResourceGroup」、針對子網路 [名稱] 輸入「公用」、針對子網路 [位址範圍] 輸入10.0.0.0/24、選取 [位置] 和您的 [訂用帳戶]、接受其餘的預設值，然後選取 [建立]：

    ![建立虛擬網路](./media/virtual-networks-create-vnet-arm-pportal/create-virtual-network.png)

    [位址空間] 和 [位址範圍] 需以 CIDR 標記法指定。 指定的 [位址空間] 包括 IP 位址 10.0.0.0-10.0.255.254。 針對子網路指定的 [位址範圍] 必須位於針對虛擬網路定義的 [位址空間] 內。 Azure DHCP 會將子網路位址範圍中的 IP 位址指派給子網路中所部署的資源。 Azure 只會將位址 10.0.0.4-10.0.0.254 指派給**公用**子網路內所部署的資源，因為 Azure 會保留每個子網路中的前四個位址 (在此範例中為子網路的 10.0.0.0-10.0.0.3) 和最後一個位址 (在此範例中為子網路的 10.0.0.255)。

## <a name="create-a-subnet"></a>建立子網路

1. 在入口網站頂端的 [搜尋資源、服務和文件] 方塊中，開始輸入 myVirtualNetwork。 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。
2. 選取 [子網路]，然後選取 [+ 子網路]，如下圖所示：

     ![新增子網路](./media/virtual-networks-create-vnet-arm-pportal/add-subnet.png)
     
3. 在出現的 [新增子網路] 方塊中，針對 [名稱] 輸入「私密」，針對 [位址範圍] 輸入 10.0.1.0/24，然後選取 [確定]。  子網路位址範圍不能與虛擬網路內的其他子網路位址範圍重疊。 

部署 Azure 虛擬網路和子網路以在生產環境中使用之前，建議您徹底熟悉位址空間[考量](manage-virtual-network.md#create-a-virtual-network)和[虛擬網路限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 一旦資源部署至子網路，某些虛擬網路和子網路變更 (例如變更位址範圍) 可能需要重新部署子網路內已部署的現有 Azure 資源。

## <a name="test-network-communication"></a>測試網路通訊

虛擬網路可讓數種類型的 Azure 資源與網際網路通訊，以及讓資源彼此私密通訊。 其中一種您可以部署到虛擬網路中的資源類型是虛擬機器。 在虛擬網路中建立兩部虛擬機器，以便您可以在稍後步驟中測試它們之間的網路通訊和網際網路。

### <a name="create-virtual-machines"></a>建立虛擬機器

1. 選取 Azure 入口網站左上角的 [+ 建立資源]。
2. 選取 [計算]，然後選取 [Windows Server 2016 Datacenter]。 您可以選取不同的作業系統，但是其餘步驟假設您選取的是 **Windows Server 2016 Datacenter**。 
3. 針對 [基本] 選取或輸入下列資訊，然後選取 [確定]︰
    - **名稱**：myVmWeb
    - **資源群組**：選取 [使用現有的] 然後選取 [myResourceGroup]。
    - **位置**：選取 [美國東部]。

    您輸入的 [使用者名稱] 和 [密碼] 會在稍後的步驟中使用到。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。 所選取的 [位置] 和 [訂用帳戶] 必須與虛擬網路所在的位置和訂用帳戶相同。 您不需要選取虛擬網路建立所在的相同資源群組，但是本教學課程會選取相同的資源群組。
4. 在 [選擇大小] 底下選取虛擬機器大小。
5. 針對 [設定] 選取或輸入下列資訊，然後選取 [確定]︰
    - **虛擬網路**：確定有選取 [myVirtualNetwork]。 若未選取，請選取 [虛擬網路]，然後在 [選擇虛擬網路] 底下選取 [myVirtualNetwork]。
    - **子網路**：確定有選取 [公用]。 若未選取，請選取 [子網路]，然後在 [選擇子網路] 底下選取 [公用]，如下圖所示：
    
        ![虛擬機器設定](./media/virtual-networks-create-vnet-arm-pportal/virtual-machine-settings.png)
 
6. 在 [摘要] 中的 [建立] 底下，選取 [建立] 來開始虛擬機器部署。
7. 再次完成步驟 1-6，但是針對虛擬機器的 [名稱] 輸入 myVmMgmt，然後針對 [子網路] 選取 [私密]。

建立虛擬機器需要幾分鐘的時間。 請等到這兩個虛擬機器都已建立，再繼續進行其餘步驟。

在本文中所建立的虛擬機器有一個[網路介面](virtual-network-network-interface.md)，其具有一個動態指派給網路介面的 IP 位址。 您部署虛擬機器之後，可以[新增多個公用和私人 IP 位址，也可以將 IP 位址指派方法變更為靜態](virtual-network-network-interface-addresses.md#add-ip-addresses)。 您可以[新增網路介面](virtual-network-network-interface-vm.md#vm-add-nic)，最多可達您在建立虛擬機器時選取之[虛擬機器大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)所支援的限制。 您也可以針對虛擬機器[啟用單一根目錄 I/O 虛擬化 (SR-IOV)](create-vm-accelerated-networking-powershell.md)，但前提是您所建立的虛擬機器必須具有支援該功能的虛擬機器大小。

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>在虛擬機器之間通訊以及與網際網路通訊

1. 在入口網站頂端的 [搜尋] 方塊中，開始輸入 myVmMgmt。 當 **myVmMgmt** 出現在搜尋結果中時，請加以選取。
2. 藉由選取 [連線] 來建立與 myVmMgmt 虛擬機器的遠端桌面連線，如下圖所示：

    ![連線至虛擬機器](./media/virtual-networks-create-vnet-arm-pportal/connect-to-virtual-machine.png)  

3. 若要連線至虛擬機器，請開啟所下載的 RDP 檔案。 如果出現提示，請選取 [連接]。
4. 輸入您在建立虛擬機器時指定的使用者名稱和密碼 (您可能需要選取 [更多選擇]，然後選取 [使用不同的帳戶] 以指定您在建立虛擬機器時輸入的認證)，然後選取 [確定]。
5. 您可能會在登入過程中收到憑證警告。 選取 [是] 以繼續進行連線。
6. 稍後的步驟會使用 ping 從 myVmWeb 虛擬機器與 myVmMgmt 虛擬機器通訊。 ping 會使用 ICMP，但 ICMP 依預設不得通過 Windows 防火牆。 從命令提示字元中輸入下列命令，讓 ICMP 通過 Windows 防火牆：

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    雖然本文使用 ping，但不建議在生產環境部署中允許 ICMP 通過 Windows 防火牆。
7. 為確保安全，通常會限制可以在虛擬網路中遠端連線的虛擬機器數目。 在本教學課程中，myVmMgmt 虛擬機器是用來管理虛擬網路中的 myVmWeb 虛擬機器。 若要從 myVmMgmt 虛擬機器將桌面遠端連線至 myVmWeb 虛擬機器，請從命令提示字元輸入下列命令：

    ``` 
    mstsc /v:myVmWeb
    ```
8. 若要從 myVmWeb 虛擬機器對 myVmMgmt 虛擬機器通訊，請從命令提示字元輸入下列命令：

    ```
    ping myvmmgmt
    ```

    您會收到類似下列輸出範例的輸出：
    
    ```
    Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
    Ping statistics for 10.0.1.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
    ```
      
    您可以看到 myVmMgmt 虛擬機器的位址是 10.0.1.4。 在您於上一個步驟中將 myVmMgmt 虛擬機器部署所在的私人子網路內，10.0.1.4 是該子網路位址範圍中可用的第一個 IP 位址。  您會看到虛擬機器的完整網域名稱是 myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net。 雖然在您的虛擬機器中，網域名稱的 dar5p44cif3ulfq00wxznl3i3f 部分並不相同，但是網域名稱的其餘部分都相同。 

    根據預設，Azure 虛擬機器都會使用預設的 Azure DNS 服務。 虛擬網路內的虛擬機器全都可以使用 Azure 的預設 DNS 服務，解析相同虛擬網路中所有其他虛擬機器的名稱。 若不使用 Azure 的預設 DNS 服務，您也可以使用您自己的 DNS 伺服器或 Azure DNS 服務的私人網域功能。 如需詳細資訊，請參閱[使用專屬 DNS 伺服器的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)或[將 Azure DNS 用於私人網域](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

9. 若要在 myVmWeb 虛擬機器上安裝適用於 Windows Server 的 Internet Information Services (IIS)，請從 PowerShell 工作階段輸入下列命令：

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

10. 完成 IIS 安裝之後，將 myVmWeb 遠端桌面工作階段中斷連線，以留在 myVmMgmt 遠端桌面工作階段。 開啟網頁瀏覽器，然後瀏覽至 http://myvmweb。 您會看到 IIS 歡迎頁面。
11. 將 myVmMgmt 遠端桌面工作階段中斷連線。
12. 尋找 myVmWeb 虛擬機器的公用 IP 位址。 當 Azure 建立 myVmWeb 虛擬機器時，名為 myVmWeb 的公用 IP 位址資源也會建立並指派給虛擬機器。 您可以在步驟 2 的圖片中看到，針對**公用 IP 位址**已將 52.170.5.92 指派給 myVmMgmt 虛擬機器。 若要尋找指派給 myVmWeb 虛擬機器的公用 IP 位址，請在入口網站的搜尋方塊中搜尋 myVmWeb，然後在它出現在搜尋結果中時加以選取。

    雖然不需要對虛擬機器指派公用 IP 位址，但 Azure 預設會對您建立的每個虛擬機器指派公用 IP 位址。 若要從網際網路對虛擬機器進行通訊，就必須對虛擬機器指派公用 IP 位址。 無論是否有對虛擬機器指派公用 IP 位址，虛擬機器都可以對外與網際網路通訊。 若要深入了解 Azure 中的輸出網際網路連線，請參閱 [Azure 中的輸出連線](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
13. 在您自己的電腦上，瀏覽至 myVmWeb 虛擬機器的公用 IP 位址。 從您自己的電腦檢視 IIS 歡迎頁面的嘗試會失敗。 嘗試會失敗是因為虛擬機器在部署時，Azure 預設是為每個虛擬機器建立網路安全性群組。 

     網路安全性群組包含安全性規則，會依連接埠和 IP 位址允許或拒絕傳入和傳出網路流量。 Azure 建立的預設網路安全性群組允許相同虛擬網路中的資源透過所有連接埠彼此通訊。 對於 Windows 虛擬機器，預設網路安全性群組會拒絕從網際網路透過所有連接埠傳入的所有流量，但 TCP 通訊埠 3389 (RDP) 除外。 因此根據預設，您也可以從網際網路直接以 RDP 連線至 myVmWeb 虛擬機器，即便您並不想要讓連接埠 3389 對 Web 伺服器開放。 既然網頁瀏覽是透過連接埠 80 來通訊，因為預設網路安全性群組中沒有任何規則允許流量通過通訊埠 80，所以從網際網路進行通訊會失敗。

## <a name="clean-up-resources"></a>清除資源

當不再需要資源群組時，請將資源群組及其包含的所有資源刪除： 

1. 在入口網站頂端的 [搜尋] 方塊中，輸入 myResourceGroup。 當您在搜尋結果中看到 myResourceGroup 時，請加以選取。
2. 選取 [刪除資源群組]。
3. 針對 [輸入資源群組名稱:] 輸入 myResourceGroup，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何部署具有多個子網路的虛擬網路。 您也已了解在建立 Windows 虛擬機器時，Azure 會建立網路介面並連結至虛擬機器，而且會建立網路安全性群組以便只允許從網際網路透過連接埠 3389 傳輸流量。 請前進到下一個教學課程，以了解如何篩選進入子網路的網路流量，而不是進入個別虛擬機器的網路流量。

> [!div class="nextstepaction"]
> [篩選進入子網路的網路流量](./virtual-networks-create-nsg-arm-pportal.md)
