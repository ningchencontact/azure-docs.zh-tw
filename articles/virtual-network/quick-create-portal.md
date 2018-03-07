---
title: "在 Azure 中建立虛擬網路 - 入口網站 | Microsoft Docs"
description: "快速了解如何使用 Azure 入口網站來建立虛擬網路。 虛擬網路可讓許多類型的 Azure 資源互相進行私密通訊。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: b1dbe96b9f522474cd2eeb2b63f3429f9ea4d8ed
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>使用 Azure 入口網站建立虛擬網路

在本文中，您將了解如何建立虛擬網路。 建立虛擬網路之後，您需將兩部虛擬機器部署到虛擬網路中，以在兩者間測試私人網路通訊。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure"></a>登入 Azure 

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="create-a-virtual-network"></a>建立虛擬網路

1. 選取 Azure 入口網站左上角的 [+ 新增]。

2. 選取 [網絡]，然後選取 [虛擬網路]。

3. 如下圖所示，針對 [名稱] 輸入 *myVirtualNetwork*、針對 [資源群組] 輸入 *myResourceGroup*、選取 [位置] 和您的 [訂用帳戶]、接受其餘的預設值，然後選取 [建立]。 

    ![輸入虛擬網路的相關基本資訊](./media/quick-create-portal/virtual-network.png)

    [位址空間] 需以 CIDR 標記法指定。 虛擬網路會包含零個或多個子網路。 預設子網路**位址範圍** 10.0.0.0/24 會使用虛擬網路的整個位址範圍，因此，使用預設的位址空間和範圍時，無法在虛擬網路中建立另一個子網路。 指定的位址範圍包括 IP 位址 10.0.0.0-10.0.0.254。 不過，只有 10.0.0.4-10.0.0.254 可供使用，因為 Azure 會保留每個子網路中的前四個位址 (0-3) 和最後一個位址。 可用的 IP 位址會指派給部署在虛擬網路內的資源。

## <a name="test-network-communication"></a>測試網路通訊

虛擬網路可讓數種類型的 Azure 資源互相進行私密通訊。 其中一種您可以部署到虛擬網路中的資源類型是虛擬機器。 在虛擬網路中建立兩部虛擬機器，以便您可以在稍後步驟中驗證它們之間的私密通訊。

### <a name="create-virtual-machines"></a>建立虛擬機器

1. 選取 Azure 入口網站左上角的 [新增] 按鈕。

2. 選取 [計算]，然後選取 [Windows Server 2016 Datacenter]。

3. 依照下圖所示，輸入虛擬機器資訊。 您輸入的 [使用者名稱] 和 [密碼] 會在稍後的步驟中用來登入虛擬機器。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。 選取你的 [訂用帳戶]，選擇使用現有的 [myResourceGroup] 資源群組，並確定所選 [位置] 與您建立虛擬機器的位置相同。 完成時選取 [確定]。

    ![輸入虛擬機器的相關基本資訊](./media/quick-create-portal/virtual-machine-basics.png)

4. 選取虛擬機器的大小，然後選取 [選取]。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 為您顯示的大小可能與以下範例不同： 

    ![選取虛擬機器的大小](./media/quick-create-portal/virtual-machine-size.png)

5. 在 [設定] 底下，應該已經針對 [虛擬網路] 選取 [myVirtualNetwork]，如果沒有，請選取 [虛擬網路]，然後選取 [myVirtualNetwork]。 針對 [子網路]保留選取 [default]，然後選取 [確定]。

    ![選取虛擬網路](./media/quick-create-portal/virtual-machine-network-settings.png)

6. 在 [摘要] 頁面上，選取 [建立] 來開始進行虛擬機器部署。 

7. 建立虛擬機器需要幾分鐘的時間。 建立之後，虛擬機器會釘選到 Azure 入口網站儀表板上，並且會自動開啟虛擬機器摘要。 選取 [網路功能]。

    ![虛擬機器網路資訊](./media/quick-create-portal/virtual-machine-networking.png)

    您會看到 [私人 IP] 位址為 *10.0.0.4*。 在步驟 5 中，您已在 [設定] 底下選取 [myVirtualNetwork] 虛擬網路，並針對 [子網路] 接受名為 *default* 的子網路。 當您[建立虛擬網路](#create-a-virtual-network)時，針對子網路 [位址範圍] 接受了預設值 10.0.0.0/24。 Azure 的 DHCP 伺服器會將您所選子網路的第一個可用位址指派給虛擬機器。 由於 Azure 會保留每個子網路的前四個位址 (0-3)，因此子網路的第一個可用 IP 位址是 10.0.0.4。

    指派的**公用 IP** 位址與指派給虛擬機器的位址不同。 Azure 預設會為每部虛擬機器指派一個公用、可透過網際網路路由傳送的 IP 位址。 將會從[指派給每個 Azure 區域的位址集區](https://www.microsoft.com/download/details.aspx?id=41653) \(英文\) 指派公用 IP 位址給虛擬機器。 雖然 Azure 知道哪個公用 IP 位址已指派給虛擬機器，但在虛擬機器中執行的作業系統並不會察覺指派給它的任何公用 IP 位址。

8. 再次完成步驟 1-7，但在步驟 3 中，將虛擬機器命名為 *myVm2*。 

9. 建立虛擬機器之後，如您在步驟 7 中所做的一樣，選取 [網路功能]。 您會看到 [私人 IP] 位址為 *10.0.0.5*。 由於 Azure 先前已將子網路中第一個可用的位址 *10.0.0.4* 指派給 *myVm1* 虛擬機器，因此它將 *10.0.0.5* 指派給 *myVm2* 虛擬機器，因為這是子網路中下一個可用的位址。

### <a name="connect-to-a-virtual-machine"></a>連接到虛擬機器

1. 從遠端連線至 *myVm1*虛擬機器。 在 Azure 入口網站頂端，輸入 *myVm1*。 當 **myVm1** 出現在搜尋結果中時，選取它。 選取 [連線]  按鈕。

    ![虛擬機器概觀](./media/quick-create-portal/virtual-machine-overview.png)

2. 在選取 [連線] 按鈕之後，將會建立一個「遠端桌面通訊協定」(.rdp) 檔案並下載至您的電腦。  

3. 開啟所下載的 rdp 檔案。 如果出現提示，請選取 [連接]。 輸入您在建立虛擬機器時指定的使用者名稱和密碼，然後選取 [確定]。 您可能會在登入過程中收到憑證警告。 選取 [是] 或 [繼續] 以繼續進行連線。

### <a name="validate-communication"></a>驗證通訊

嘗試針對 Windows 虛擬機器執行 Ping 會失敗，因為預設不允許 Ping 穿過 Windows 防火牆。 若要允許針對 *myVm1* 執行 Ping，請從命令提示字元中輸入下列命令：

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

若要驗證與 *myVm2* 的通訊，請從 *myVm1* 虛擬機器上的命令提示字元中輸入下列命令。 請提供您建立虛擬機器時所使用的認證，然後完成連線：

```
mstsc /v:myVm2
```

遠端桌面連線成功，因為兩部虛擬機器都已從 *default* 子網路獲指派私人 IP 位址，並且預設穿過 Windows 防火牆開啟了遠端桌面。 您能夠依據主機名稱連線至 *myVm2*，因為 Azure 會自動為虛擬網路內的所有主機提供 DNS 名稱解析。 在命令提示字元中，從 myVm2 針對 myVm1 執行 Ping。

```
ping myvm1
```

Ping 執行成功，因為您在上一個步驟中允許它穿過 *myVm1* 虛擬機器上的 Windows 防火牆。 若要確認對網際網路的輸出通訊，請輸入下列命令：

```
ping bing.com
```

您會從 bing.com 收到四個回覆。根據預設，虛擬網路中的所有虛擬機器都能對網際網路進行輸出通訊。 

結束遠端桌面工作階段。

## <a name="clean-up-resources"></a>清除資源

當不再需要資源群組時，請將資源群組及其所有內容刪除。 在 Azure 入口網站頂端，輸入 *myResourceGroup*。 當 **myResourceGroup** 出現在搜尋結果中時，選取它。 選取 [刪除] 。

## <a name="next-steps"></a>後續步驟

在本文中，您已部署含有一個子網路的預設虛擬網路。 若要了解如何建立含有多個子網路的自訂虛擬網路，請繼續進行建立自訂虛擬網路的教學課程。

> [!div class="nextstepaction"]
> [建立自訂虛擬網路](virtual-networks-create-vnet-arm-pportal.md#portal)
