---
title: 管理 Azure 中的私人端點
description: 了解如何使用 Azure 入口網站建立私人端點
services: private-link
author: KumudD
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 2b7ad95dc652f2099af75308f05ddd0684e088aa
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104739"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>使用 Azure 入口網站建立私人端點

私人端點是 Azure 中私人連結的基本要素。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。 在本快速入門中，您將了解如何使用 Azure PowerShell 在 Azure 虛擬網路上建立 VM，以及建立具有 Azure 私人端點的 SQL Database 伺服器。 然後，您就可以從 VM 安全地存取 SQL Database 伺服器。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-vm"></a>建立 VM
在本節中，您將建立虛擬網路和子網路，以裝載用來存取 Private Link 資源 (在此範例中為 Azure 中的 SQL 伺服器) 的 VM。

### <a name="create-the-virtual-network"></a>建立虛擬網路


在本節中，您將建立虛擬網路和子網路，以裝載用來存取 Private Link 資源的 VM。

1. 在畫面的左上方，選取 [建立資源]   > [網路]   > [虛擬網路]  。
1. 在 [建立虛擬網路]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 MyVirtualNetwork  。 |
    | 位址空間 | 輸入 *10.1.0.0/16*。 |
    | 訂用帳戶 | 選取您的訂用帳戶。|
    | 資源群組 | 選取 [新建]  ，輸入 *myResourceGroup*，然後選取 [確定]  。 |
    | 位置 | 選取 [WestCentralUS]  。|
    | 子網路 - 名稱 | 輸入 mySubnet  。 |
    | 子網路 - 位址範圍 | 輸入 *10.1.0.0/24*。 |
    |||
1. 將其他項目保留為預設值，然後選取 [建立]  。


### <a name="create-virtual-machine"></a>建立虛擬機器

1. 在 Azure 入口網站的畫面左上角，選取 [建立資源]   > [計算]   > [虛擬機器]  。

1. 在 [建立虛擬機器 - 基本]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。  |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 myVm  。 |
    | 區域 | 選取 [WestCentralUS]  。 |
    | 可用性選項 | 保留預設值 [不需要基礎結構備援]  。 |
    | 映像 | 選取 [Windows Server 2019 Datacenter]  。 |
    | 大小 | 保留預設值 [標準 DS1 v2]  。 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 再次輸入密碼。 |
    | **輸入連接埠規則** |  |
    | 公用輸入連接埠 | 保留預設值 [無]  。 |
    | **節省費用** |  |
    | 已經有 Windows 授權？ | 保留預設值 [否]  。 |
    |||

1. 完成時，選取 [下一步:  磁碟]。

1. 在 [建立虛擬機器 - 磁碟]  ，保留預設值並選取 [下一步：  網路功能]。

1. 在 [建立虛擬機器 - 網路功能]  中，選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 虛擬網路 | 保留預設值 [MyVirtualNetwork]  。  |
    | 位址空間 | 保留預設值 [10.1.0.0/24]  。|
    | 子網路 | 保留預設值 [mySubnet (10.1.0.0/24)]  。|
    | 公用 IP | 保留預設值 [(new) myVm-ip]  。 |
    | 公用輸入連接埠 | 選取 [允許選取的連接埠]  。 |
    | 選取輸入連接埠 | 選取 [HTTP]  和 [RDP]  。|
    |||


1. 選取 [檢閱 + 建立]  。 您會移至 [檢閱 + 建立]  頁面，其中 Azure 會驗證您的設定。

1. 當您看到 [驗證成功]  訊息時，請選取 [建立]  。

## <a name="create-a-sql-database-server"></a>建立 SQL 資料庫伺服器
在本節中，您將在 Azure 中建立 SQL 資料庫伺服器。 

1. 在 Azure 入口網站的畫面左上角，選取 [建立資源]   > [資料庫]   > [SQL 資料庫]  。

1. 在 [建立SQL 資料庫 - 基本]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **資料庫詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    | **執行個體詳細資料** |  |
    | 資料庫名稱  | 輸入 mydatabase  。 如果此名稱已被使用，請建立唯一名稱。 |
    |||
5. 在 [伺服器] **** 中選取 [建立新的]  。 
6. 在 [新增伺服器] **** 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |伺服器名稱  | 輸入 myserver  。 如果此名稱已被使用，請建立唯一名稱。|
    | 伺服器管理員登入| 輸入您所選擇的系統管理員名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 8 個字元，而且符合定義的需求。 |
    | 位置 | 選取您想要放置 SQL Server 的 Azure 區域。 |
    
7. 選取 [確定] **** 。 
8. 選取 [檢閱 + 建立] **** 。 您會移至 [檢閱 + 建立] ****   頁面，其中 Azure 會驗證您的設定。 
9. 當您看到 [驗證成功] 訊息時，請選取 [建立] **** 。 
10. 當您看到 [驗證成功] 訊息時，請選取 [建立]。 

## <a name="create-a-private-endpoint"></a>建立私人端點

在本節中，您將建立 SQL 伺服器，並在其中新增私人端點。 

1. 在 Azure 入口網站的畫面左上角，選取 [建立資源]   > [網路]   > [Private Link 中心 (預覽)]  。
2. 在 [Private Link 中心 - 概觀] **** 的 [與服務建立私人連線] **** 選項中，選取 [啟動] **** 。
1. 在 [建立私人端點 (預覽) - 基本]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    | **執行個體詳細資料** |  |
    | 名稱 | 輸入  myPrivateEndpoint  。 如果此名稱已被使用，請建立唯一名稱。 |
    |區域|選取 [WestCentralUS]  。|
    |||
5. 選取 [下一步： **** 資源]。
6. 在 [建立私人端點 - 資源] **** 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |連線方式  | 選取 [連線到我目錄中的 Azure 資源]。|
    | 訂用帳戶| 選取您的訂用帳戶。 |
    | 資源類型 | 選取 [Microsoft .sql/伺服器] **** 。 |
    | 資源 |選取 [myServer] |
    |目標子資源 |選取 [sqlServer] |
    |||
7. 選取 [下一步： **** 組態]。
8. 在 [建立私人端點 (預覽) - 組態]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |**網路**| |
    | 虛擬網路| 選取 [MyVirtualNetwork]  。 |
    | 子網路 | 選取 [mySubnet] ** 。 |
    |**私人 DNS 整合**||
    |與私人 DNS 區域整合 |選取 [是] **** 。 |
    |私人 DNS 區域 |選取 [(New)privatelink.database.windows.net]  |
    |||

1. 選取 [檢閱 + 建立] **** 。 您會移至 [檢閱 + 建立] ****   頁面，其中 Azure 會驗證您的設定。 
2. 當您看到 [驗證成功] ****   訊息時，請選取 [建立] **** 。 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>使用遠端桌面 (RDP) 連線到 VM


在您建立 *myVm  之後，從網際網路與其連線，如下所示： 

1. 在入口網站的搜尋列中，輸入 myVm  。

1. 選取 [連線]  按鈕。 選取 [連線]  按鈕之後，隨即會開啟 [連線至虛擬機器]  。

1. 選取 [下載 RDP 檔案]  。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟下載的 .rdp* 檔案。

    1. 如果出現提示，請選取 [連接]  。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選取 [其他選擇]   > [使用不同的帳戶]  ，以指定您在建立 VM 時輸入的認證。

1. 選取 [確定]  。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是]  或 [繼續]  。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>從 VM 私下存取 SQL 資料庫伺服器

1. 在 myVM ** 的遠端桌面中，開啟 PowerShell。
2. 輸入  `nslookup myserver.database.windows.net`。 

    您將收到如下訊息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
3. Install [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. In **Connect to server**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **Database Engine**.|
    | Server name| Select *myserver.database.windows.net* |
    | User name | Enter a password provided during the SQL server creation. |
    |Password |Enter a password provided during the SQL server creation. |
    |Remember password|Select **Yes**.|
    |||
1. Select **Connect**.
2. Browse databases from left menu.
3. (Optionally) Create or query information from mydatabase.
4. Close the remote desktop connection to *myVm*. 

## Clean up resources 
When you're done using the private endpoint, SQL server, and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this quickstart, you created a VM on a virtual network, a SQL database server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the SQL database server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint?](private-endpoint-overview.md).

