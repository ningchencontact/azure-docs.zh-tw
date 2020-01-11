---
title: 建立安全連線的私用端點
titleSuffix: Azure Cognitive Search
description: 目前為預覽狀態，您可以使用私人端點和安全 VNet 連線來限制對搜尋服務端點的存取。
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 0ed6319a33fc3db0b3144d91f4be10b7ab6121d7
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2020
ms.locfileid: "75865008"
---
# <a name="restrict-access-to-azure-cognitive-search-using-private-endpoint-and-a-virtual-network-connection"></a>使用私人端點和虛擬網路連線來限制對 Azure 認知搜尋的存取。

> [!IMPORTANT] 
> 私人端點的支援目前以有限存取權預覽的形式提供。 此預覽僅適用于**基本層**的搜尋服務。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-10-01-Preview](search-api-preview.md)提供這項功能。 目前沒有入口網站或 .NET SDK 支援。

在本文中，您將瞭解如何建立可透過安全連線存取的新搜尋服務，而不需要公用 IP 位址的存取權。 從部署在與服務相同的虛擬網路中的 Azure 虛擬機器，允許用戶端連線。

## <a name="about-private-endpoint-support"></a>關於私用端點支援

Azure 認知搜尋的[私人端點](../private-link/private-endpoint-overview.md)可讓虛擬網路上的用戶端透過[私人連結](../private-link/private-link-overview.md)安全地存取資料。 私人端點會使用您搜尋服務的[虛擬網路位址空間](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)中的 IP 位址。 用戶端與搜尋服務之間的網路流量會透過虛擬網路和 Microsoft 骨幹網路上的私人連結來進行，以避免公開網際網路的暴露。 如需支援私人連結的 PaaS 服務清單，請參閱產品檔中的[可用性一節](../private-link/private-link-overview.md#availability)。

您的搜尋服務的私用端點可讓您：

+ 封鎖搜尋服務的公用端點上的所有連線。
+ 藉由讓您封鎖從虛擬網路外泄的資料，來提高虛擬網路的安全性。
+ 從使用[VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[ExpressRoutes](../expressroute/expressroute-locations.md)與私用對等互連連線到虛擬網路的內部部署網路，安全地連接到您的搜尋服務。

> [!NOTE]
> 當服務端點為私用時，部分入口網站功能會停用。 您將能夠查看和管理服務層級資訊，但基於安全性考慮，會限制入口網站存取索引資料和服務中的各種元件（例如索引、索引子和技能集定義）。

## <a name="request-access"></a>要求存取 

按一下 [[要求存取](https://aka.ms/SearchPrivateLinkRequestAccess)權] 以註冊此預覽功能。 表單會要求您、公司和一般網路拓撲的相關資訊。 在我們審查您的要求之後，您會收到確認電子郵件，其中包含其他指示。

## <a name="create-a-vm"></a>建立 VM
在本節中，您將建立虛擬網路和子網，以裝載將用來存取搜尋服務私人端點的 VM。

### <a name="set-up-the-virtual-network"></a>設定虛擬網路
1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左上方，選取 [**建立資源**] > [**網路**] [ > ] [**虛擬網路**]。
1. 在 [建立虛擬網路] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入*MyVirtualNetwork* |
    | 位址空間 | 輸入*10.1.0.0/16* |
    | 訂閱 | 選取您的訂用帳戶|
    | 資源群組 | 選取 [**新建**]，輸入*myResourceGroup*，然後選取 **[確定]** 。 |
    | 位置 | 選取 [**美國西部**] 或您所使用的任何區域|
    | 子網路 - 名稱 | 輸入*mySubnet* |
    | 子網路 - 位址範圍 | 輸入*10.1.0.0/24* |
    |||

1. 將其他項目保留為預設值，然後選取 [建立]。


### <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 在 [主要入口網站] 頁面的左上方，選取 [**建立資源**] > [**計算**] [ > **虛擬機器**]。

1. 在 [建立虛擬機器 - 基本] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂閱 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。  |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 myVm。 |
    | 地區 | 選取 [**美國西部**] 或您所使用的任何區域。 |
    | 可用性選項 | 保留預設值 [不需要基礎結構備援]。 |
    | 影像 | 選取 [Windows Server 2019 Datacenter]。 |
    | 大小 | 保留預設值 [標準 DS1 v2]。 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 再次輸入密碼。 |
    | **輸入連接埠規則** |  |
    | 公用輸入連接埠 | 保留預設值 [無]。 |
    | **節省費用** |  |
    | 已經有 Windows 授權？ | 保留預設值 [否]。 |
    |||

1. 選取 **[下一步：磁片]** 。

1. 在 [**建立虛擬機器-磁片**] 中保留預設值，然後選取 **[下一步：網路]** 。

1. 在 [建立虛擬機器 - 網路] 中，選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 虛擬網路 | 保留預設值 [MyVirtualNetwork]。  |
    | 位址空間 | 保留預設值 [10.1.0.0/24]。|
    | 子網路 | 保留預設值 [mySubnet (10.1.0.0/24)]。|
    | 公用 IP | 保留預設值 [(new) myVm-ip]。 |
    | 公用輸入連接埠 | 選取 [允許選取的連接埠]。 |
    | 選取輸入連接埠 | 選取 [HTTP] 和 [RDP]。|
    ||

1. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。

1. 當您看到 [驗證成功] 訊息時，請選取 [建立]。


## <a name="create-your-search-service-with-a-private-endpoint"></a>使用私人端點建立您的搜尋服務

在本節中，您會建立具有私人端點的新 Azure 認知搜尋服務。 

1. 在 [主要入口網站] 頁面的左上方，選取 [**建立資源**] > **Web** > **Azure 認知搜尋**。

1. 在 [**新搜尋服務-基本**] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂閱 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    | **執行個體詳細資料** |  |
    | URL | 輸入唯一名稱。 |
    | 位置 | 選取您在要求存取此預覽功能時所指定的區域。 |
    | 價格層 | 選取 [**變更定價層**]，然後選擇 [**基本**]。 這是預覽所需的層級。 |
    |||
  
1. 選取 **[下一步：調整]** 。

1. 保留 [預設值]，然後選取 **[下一步：網路]** 。

1. 在 [**新搜尋服務-網路**] 中，針對 **[端點連線能力（資料）** ] 選取 [**私人**]。

1. 在 [**新增搜尋服務-網路**] 中，選取 [**私人端點**] 底下的 [ **+ 新增**]。 

1. 在 [**建立私人端點**] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 訂閱 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    | 位置 | 選取 [**美國西部**]。|
    | 名稱 | 輸入 *myPrivateEndpoint*。  |
    | 目標子資源 | 保留預設值**searchService**。 |
    | **網路** |  |
    | 虛擬網路  | 從 [資源群組] *myResourceGroup*中選取 [ *MyVirtualNetwork* ]。 |
    | 子網路 | 選取 [mySubnet] ** 。 |
    | **私人 DNS 整合** |  |
    | 與私人 DNS 區域整合  | 保留預設值 [**是]** 。 |
    | 私人 DNS 區域  | 保留預設的 * * （New） privatelink.search.windows.net * *。 |
    |||

1. 選取 [確定]。 

1. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。 

1. 當您看到 [驗證成功] 訊息時，請選取 [建立]。 
1. 一旦建立服務，請流覽至您剛才建立的資源。
1. 從左側內容功能表中選取 [**金鑰**]。
1. 複製**主要系統管理金鑰**，以供下一個步驟使用。

 
## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

從網際網路連線至 VM：myVm，如下所示：

1. 在入口網站的搜尋列中，輸入 myVm。

1. 選取 [連線] 按鈕。 選取 [連線] 按鈕之後，隨即會開啟 [連線至虛擬機器]。

1. 選取 [下載 RDP 檔案]。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟下載的 .rdp* 檔案。

    1. 如果出現提示，請選取 [連接]。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。

1. 選取 [確定]。

1. 當您登入時，可能會收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  


## <a name="access-the-search-service-privately-from-the-vm"></a>從 VM 私下存取搜尋服務

在本節中，您將驗證對搜尋服務的私人網路存取，並使用私用端點私下連接到儲存體帳戶。

1. 在 myVM ** 的遠端桌面中，開啟 PowerShell。

1. 輸入 ' nslookup [搜尋服務名稱]. search. windows. net '

    您將收到如下訊息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```
1. 請遵循此[快速入門](search-get-started-postman.md)中的 VM，使用 REST API 在 Postman 的服務中建立新的搜尋索引。

1. 在 Postman 中，于您的本機工作站上嘗試幾個相同的要求。

1. 如果您可以從 VM 完成快速入門，但收到遠端伺服器不存在於您的本機工作站上的錯誤，表示您已成功設定搜尋服務的私人端點。

1. 關閉對 *myVM*的遠端桌面連線。 

## <a name="clean-up-resources"></a>清除資源 

當您使用私人端點、搜尋服務帳戶和 VM 完成時，請刪除資源群組及其包含的所有資源： 

1. 在入口網站頂端的 [搜尋] 方塊中輸入  *myResourceGroup* ，然後從搜尋結果中選取 [myResourceGroup] **  。 
1. 選取 [刪除資源群組]。 
1. **在 [輸入資源組名**] 中輸入 *myResourceGroup* ，然後選取 [**刪除**]。

## <a name="next-steps"></a>後續步驟

在本文中，您已在虛擬網路上建立 VM，並在具有私人端點的搜尋服務上建立。 您已從網際網路連線至 VM，並使用私人連結安全地向搜尋服務通訊。 

> [!div class="nextstepaction"]
> [什麼是 Azure 私人端點？](../private-link/private-endpoint-overview.md)。
