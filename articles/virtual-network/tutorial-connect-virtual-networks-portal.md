---
title: 使用虛擬網路對等互連連線虛擬網路 - Azure 入口網站 | Microsoft Docs
description: 深入了解如何使用虛擬網路對等互連連線虛擬網路。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ac0c1033546758a77b43298a5fa8cba5f5204650
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>使用 Azure 入口網站以虛擬網路對等互連連線虛擬網路

您可以使用虛擬網路對等互連，讓虛擬網路彼此連線。 一旦虛擬網路對等互連，兩個虛擬網路中的資源就可以彼此通訊，且通訊時會有相同的延遲和頻寬，彷彿這些資源是位於相同的虛擬網路中。 本文涵蓋建立和對等互連兩個虛擬網路。 您會了解如何：

> [!div class="checklist"]
> * 建立兩個虛擬網路
> * 建立虛擬網路之間的對等互連
> * 測試對等互連

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure"></a>登入 Azure 

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="create-virtual-networks"></a>建立虛擬網路

1. 選取 Azure 入口網站左上角的 [+ 建立資源]。
2. 選取 [網絡]，然後選取 [虛擬網路]。
3. 如下圖所示，針對 [名稱] 輸入 myVirtualNetwork1、針對 [位址空間] 輸入 10.0.0.0/16、針對 [資源群組] 輸入 **myResourceGroup**、針對子網路 [名稱] 輸入 Subnet1、針對子網路 [位址範圍] 輸入 10.0.0.0/24、選取 [位置] 和您的 [訂用帳戶]、接受其餘的預設值，然後選取 [建立]：

    ![建立虛擬網路](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. 以下列變更再次完成步驟 1-3：
    - **名稱**：myVirtualNetwork2
    - **資源群組**：選取 [使用現有的] 然後選取 [myResourceGroup]。
    - **位址空間**：*10.1.0.0/16*
    - **子網路位址範圍**：10.1.0.0/24

    myVirtualNetwork2 虛擬網路的位址前置詞不會與 myVirtualNetwork1 虛擬網路的位址首碼重疊。 您無法對等互連具有重疊位址空間的虛擬網路。

## <a name="peer-virtual-networks"></a>對等互連虛擬網路

1. 在 Azure 入口網站頂端的 [搜尋] 方塊中，開始輸入 MyVirtualNetwork1。 當搜尋結果中出現 myVirtualNetwork1 時加以選取。
2. 選取 [設定] 底下的 [對等互連]，然後選取 [+新增]，如下圖所示：

    ![建立對等互連](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. 輸入或選取下圖中顯示的資訊，然後選取 [確定]。 若要選取 myVirtualNetwork2 虛擬網路，請選取 [虛擬網路]，然後選取 [myVirtualNetwork2]。

    ![對等互連設定](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    「對等互連狀態」是「已啟動」，如下圖所示：

    ![對等互連狀態](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    如果您沒有看到狀態，請重新整理瀏覽器。

4. 搜尋 myVirtualNetwork2 虛擬網路。 當它在搜尋結果中傳回時，加以選取。
5. 以下列變更再次完成步驟 1-3，然後選取 [確定]：
    - **名稱**：myVirtualNetwork2-myVirtualNetwork1
    - **虛擬網路**：myVirtualNetwork1

    「對等互連狀態」是「已連線」。 Azure 也會將 myVirtualNetwork2-myVirtualNetwork1 對等互連的對等互連狀態從「已啟動」變更為「已連線」。 在兩個虛擬網路的對等互連狀態變為「已連線」之後，您才能成功建立虛擬網路對等互連。 

對等互連介於兩個虛擬網路之間，但不可轉移。 所以舉例來說，如果您也想要將 myVirtualNetwork2 對等互連至 myVirtualNetwork3，您必須在虛擬網路 myVirtualNetwork2 與 myVirtualNetwork3 之間建立額外的對等互連。 雖然 myVirtualNetwork1 已與 myVirtualNetwork2 對等互連，但是如果 myVirtualNetwork1 也與 myVirtualNetwork3 對等互連，則 myVirtualNetwork1 內的資源只能存取 myVirtualNetwork3 中的資源。 

在對等互連生產環境虛擬網路之前，建議您徹底熟悉[對等互連概觀](virtual-network-peering-overview.md)、[管理對等互連](virtual-network-manage-peering.md)以及[虛擬網路限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 雖然本文說明相同訂用帳戶和位置中兩個虛擬網路之間的對等互連，您也可以對等互連[不同區域](#register)和[不同 Azure 訂用帳戶](create-peering-different-subscriptions.md#portal)中的虛擬網路。 您也可以使用對等互連來建立[中樞和輪輻網路設計](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。

## <a name="test-peering"></a>測試對等互連

若要測試不同虛擬網路中虛擬機器之間透過對等互連的網路通訊，請將虛擬機器部署到每個子網路，然後在虛擬機器之間進行通訊。 

### <a name="create-virtual-machines"></a>建立虛擬機器

在每個虛擬網路中建立虛擬機器，以便您可以在稍後步驟中驗證其間的通訊。

### <a name="create-virtual-machines"></a>建立虛擬機器

1. 選取 Azure 入口網站左上角的 [+ 建立資源]。
2. 選取 [計算]，然後選取 [Windows Server 2016 Datacenter]。 您可以選取不同的作業系統，但是其餘步驟假設您選取的是 **Windows Server 2016 Datacenter**。 
3. 針對 [基本] 選取或輸入下列資訊，然後選取 [確定]︰
    - **名稱**：myVm1
    - **資源群組**：選取 [使用現有的] 然後選取 [myResourceGroup]。
    - **位置**：選取 [美國東部]。

    您輸入的 [使用者名稱] 和 [密碼] 會在稍後的步驟中使用到。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。 所選取的 [位置] 和 [訂用帳戶] 必須與虛擬網路所在的位置和訂用帳戶相同。 您不需要選取虛擬網路建立所在的相同資源群組，但是本文會選取相同的資源群組。
4. 在 [選擇大小] 底下選取虛擬機器大小。
5. 針對 [設定] 選取或輸入下列資訊，然後選取 [確定]︰
    - **虛擬網路**：確定已選取 [myVirtualNetwork1]。 若未選取，請選取 [虛擬網路]，然後在 [選擇虛擬網路] 底下選取 [myVirtualNetwork1]。
    - **子網路**：確定已選取 [Subnet1]。 若未選取，請選取 [子網路]，然後在 [選擇子網路] 底下選取 [Subnet1]，如下圖所示：
    
        ![虛擬機器設定](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. 在 [摘要] 中的 [建立] 底下，選取 [建立] 來開始虛擬機器部署。
7. 再次完成步驟 1-6，但是針對虛擬機器的 [名稱] 輸入 myVm2，然後針對 [虛擬網路] 選取 [myVirtualNetwork2]。

Azure 會指派 10.0.0.4 作為 myVm1 虛擬機器的私人 IP 位址，並且將 10.1.0.4 指派給 myVm2 虛擬機器，因為它們分別是 myVirtualNetwork1 和 myVirtualNetwork2 虛擬機器的 Subnet1 中第一個可用的位址。

建立虛擬機器需要幾分鐘的時間。 請等到這兩個虛擬機器都已建立，再繼續進行其餘步驟。

### <a name="test-virtual-machine-communication"></a>測試虛擬機器通訊

1. 在入口網站頂端的 [搜尋] 方塊中，開始輸入 myVm1。 當 **myVm1** 出現在搜尋結果中時，選取它。
2. 藉由選取 [連線] 來建立與 myVm1 虛擬機器的遠端桌面連線，如下圖所示：

    ![連線至虛擬機器](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. 若要連線至虛擬機器，請開啟所下載的 RDP 檔案。 如果出現提示，請選取 [連接]。
4. 輸入您在建立虛擬機器時指定的使用者名稱和密碼 (您可能需要選取 [更多選擇]，然後選取 [使用不同的帳戶] 以指定您在建立虛擬機器時輸入的認證)，然後選取 [確定]。
5. 您可能會在登入過程中收到憑證警告。 選取 [是] 以繼續進行連線。
6. 稍後的步驟會使用 ping 從 myVmWeb 虛擬機器與 myVm2 虛擬機器通訊。 ping 會使用 ICMP，但 ICMP 依預設不得通過 Windows 防火牆。 從命令提示字元中輸入下列命令，讓 ICMP 通過 Windows 防火牆：

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    雖然本文使用 Ping 進行測試，但不建議在生產環境部署中允許 ICMP 通過 Windows 防火牆。

7. 若要連線至 myVm2 虛擬機器，請在 myVm1 虛擬機器上從命令提示字元輸入下列命令：

    ```
    mstsc /v:10.1.0.4
    ```
    
8. 由於您在 myVm1 上啟用 ping，您現在可以依據 IP 位址來 ping 它：

    ```
    ping 10.0.0.4
    ```
    
    您會收到四個回覆。 如果您依據虛擬機器的名稱 (myVm1) 而不是其 IP 位址來進行 ping，則 ping 會失敗，因為 myVm1 是未知的主機名稱。 Azure 的預設名稱解析可以在相同虛擬網路中的虛擬機器之間運作，但是無法在不同虛擬網路中的虛擬機器之間運作。 若要跨虛擬網路間解析名稱，您必須[部署自己的 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md)或使用 [Azure DNS 私人網域](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

9. 同時中斷與 myVm1 和 myVm2 的 RDP 工作階段。

## <a name="clean-up-resources"></a>清除資源

當不再需要資源群組時，請將資源群組及其包含的所有資源刪除： 

1. 在入口網站頂端的 [搜尋] 方塊中，輸入 myResourceGroup。 當您在搜尋結果中看到 myResourceGroup 時，請加以選取。
2. 選取 [刪除資源群組]。
3. 針對 [輸入資源群組名稱:] 輸入 myResourceGroup，然後選取 [刪除]。

**<a name="register"></a>註冊可獲得虛擬網路對等互連全球預覽版**

在一般情況下，可以為相同地區中的虛擬網路建立對等互連。 讓不同區域中的虛擬網路進行對等互連的功能目前為預覽版。 如需了解有哪些可用的區域，請參閱[虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network)。 若要跨區域對等互連虛擬網路，您必須先註冊預覽版。 您無法使用入口網站註冊，但可以使用 [PowerShell](tutorial-connect-virtual-networks-powershell.md#register) 或 [Azure CLI](tutorial-connect-virtual-networks-cli.md#register) 註冊。 如果您在註冊功能之前嘗試對等互連不同區域中的虛擬網路，則對等互連會失敗。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用虛擬網路對等互連來連線兩個網路。

繼續透過 VPN 將自己的電腦連線到虛擬網路，並且與虛擬網路中或已對等互連虛擬網路中的資源進行互動。

> [!div class="nextstepaction"]
> [將您的電腦連線到虛擬網路](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
