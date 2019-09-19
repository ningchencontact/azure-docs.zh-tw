---
title: 使用 Azure 私用端點私下連接至儲存體帳戶
description: 瞭解如何在 Azure 中使用私人端點，私下連線到儲存體帳戶。
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: bb0c6e9d20c12df3532a52df1fe4d9574344d4b3
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104724"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>使用 Azure 私用端點私下連接至儲存體帳戶
Azure 私用端點是 Azure 中私人連結的基本建立區塊。 它可讓 Azure 資源（例如虛擬機器（Vm））私下與私人連結資源進行通訊。

在本快速入門中，您將瞭解如何在 Azure 虛擬網路上建立 VM、使用 Azure 入口網站的儲存體帳戶與私人端點。 然後，您可以從 VM 安全地存取儲存體帳戶。


## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-vm"></a>建立 VM
在本節中，您將建立虛擬網路和子網，以裝載用來存取私人連結資源的 VM （在此範例中為儲存體帳戶）。

### <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您將建立虛擬網路和子網，以裝載用來存取私人連結資源的 VM。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]。
1. 在 [建立虛擬網路] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | Name | 輸入*MyVirtualNetwork*。 |
    | 位址空間 | 輸入 *10.1.0.0/16*。 |
    | 訂閱 | 選取您的訂用帳戶。|
    | 資源群組 | 選取 [新建]，輸入 *myResourceGroup*，然後選取 [確定]。 |
    | Location | 選取 [ **WestCentralUS**]。|
    | 子網路 - 名稱 | 輸入*mySubnet*。 |
    | 子網路 - 位址範圍 | 輸入 *10.1.0.0/24*。 |
    |||
1. 將其他項目保留為預設值，然後選取 [建立]。


### <a name="create-virtual-machine"></a>建立虛擬機器

1. 在 [Azure 入口網站] 畫面的左上方，選取 [**建立資源** > ] [**計算** > ] [**虛擬機器**]。

1. 在 [建立虛擬機器 - 基本] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂閱 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。  |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入*myVm*。 |
    | 區域 | 選取 [ **WestCentralUS**]。 |
    | 可用性選項 | 保留預設值 [不需要基礎結構備援]。 |
    | Image | 選取 [ **Windows Server 2019 Datacenter**]。 |
    | Size | 保留預設值 [標準 DS1 v2]。 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 再次輸入密碼。 |
    | **輸入連接埠規則** |  |
    | 公用輸入連接埠 | 保留預設值 [無]。 |
    | **節省費用** |  |
    | 已經有 Windows 授權？ | 保留預設值 [否]。 |
    |||

1. 完成時，選取 [下一步:磁碟]。

1. 在 **建立虛擬機器-磁片** 中保留預設值， **然後選取 下一步：** 網路功能。

1. 在 [建立虛擬機器 - 網路功能] 中，選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 虛擬網路 | 保留預設值**MyVirtualNetwork**。  |
    | 位址空間 | 保留預設值 [ **10.1.0.0/24**]。|
    | Subnet | 保留預設值 [ **mySubnet （10.1.0.0/24）** ]。|
    | 公用 IP | 保留預設值 [(new) myVm-ip]。 |
    | 公用輸入連接埠 | 選取 [允許選取的連接埠]。 |
    | 選取輸入連接埠 | 選取 [HTTP] 和 [RDP]。|
    ||

1. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。

1. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

## <a name="create-your-private-endpoint"></a>建立您的私用端點
在本節中，您將使用私人端點來建立私人儲存體帳戶。 

1. 在 [Azure 入口網站] 畫面的左上方，選取 [**建立資源** > ] [儲存體] [**儲存體** > **帳戶**]。

1. 在 [**建立儲存體帳戶-基本**] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂閱 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    | **執行個體詳細資料** |  |
    | 儲存體帳戶名稱  | 輸入*mystorageaccount*。 如果此名稱已被使用，請建立唯一名稱。 |
    | 區域 | 選取 [ **WestCentralUS**]。 |
    | 效能| 保留預設值 [標準]。 |
    | 帳戶種類 | 保留預設**儲存體（一般用途 v2）** 。 |
    | 複寫 | 選取 **[讀取權限異地多餘儲存體（RA-GRS）** ]。 |
    |||
  
3. 選取 **下一步：** 網路。
4. 在 [ **建立儲存體帳戶-網路功能**、連線方式] 中，選取 [**私人端點**]。
5. 在 [ **建立儲存體帳戶-網路**] 中，選取 [**新增私人端點**]。 
6. 在 [ **建立私人端點**] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂閱 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    |Location|選取 [ **WestCentralUS**]。|
    |Name|輸入 *myPrivateEndpoint*。  |
    |儲存體子資源|保留預設 **Blob**。 |
    | **連** |  |
    | 虛擬網路  | 從 [資源群組] *myResourceGroup*中選取 [ *MyVirtualNetwork* ]。 |
    | Subnet | 選取 [ *mySubnet*]。 |
    | **私人 DNS 整合**|  |
    | 與私人 DNS 區域整合  | 保留預設值 [**是]** 。 |
    | 私人 DNS 區域  | 保留預設值 ** [（新增）] privatelink.blob.core.windows.net**。 |
    |||
7. 選取 **[確定]** 。 
8. 選取 [ **審查 + 建立**]。 系統會帶您前往 Azure 驗證設定的 [ **審核] + [建立** ] 頁面。 
9. 當您看到 [ **通過** 驗證] 訊息時，請選取 [ **建立**]。 
10. 流覽至您剛才建立的儲存體帳戶資源。
11. 從左側內容功能表中選取 [ **存取金鑰**]。
12. 選取 [key1] 連接字串上的 [**複製**]。
 
## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

從網際網路連線至 VM *myVm* ，如下所示：

1. 在入口網站的搜尋列中，輸入*myVm*。

1. 選取 [連線] 按鈕。 選取 [連線] 按鈕之後，隨即會開啟 [連線至虛擬機器]。

1. 選取 [下載 RDP 檔案]。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟下載的 .rdp * 檔案。

    1. 如果出現提示，請選取 [連接]。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。

1. 選取 [確定]。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  

## <a name="access-storage-account-privately-from-the-vm"></a>從 VM 私下存取儲存體帳戶

在本節中，您會使用私用端點，私下連接到儲存體帳戶。

> [!IMPORTANT]
> 存放裝置的 DNS 設定需要在主機檔案上進行手動修改以包含特定帳戶的 FQDN，請使用 Windows 上的系統管理員許可權修改下列檔案： c:\Windows\System32\Drivers\etc\hosts 或 Linux/etc/hosts依照下列格式，在上一個步驟中包含帳戶的 DNS 資訊： [私人 IP 位址] myaccount.blob.core.windows.net

1. 在 *myVM*的遠端桌面中，開啟 PowerShell。
2. 輸入 `nslookup mystorageaccount.blob.core.windows.net`您將會收到類似下面的訊息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
