---
title: Connect privately to a storage account using Azure Private Endpoint
description: Learn how to connect privately to a storage account in Azure using a Private Endpoint.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: cfe0caaf199821358f8a66ac65ae75c38336c725
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228083"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Connect privately to a storage account using Azure Private Endpoint
Azure Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link resources.

In this Quickstart, you will learn how to create a VM on an Azure virtual network, a storage account with a Private Endpoint using the Azure portal. Then, you can securely access the storage account from the VM.


## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-vm"></a>建立 VM
In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link Resource (a storage account in this example).

### <a name="create-the-virtual-network"></a>建立虛擬網路

In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link resource.

1. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]。
1. 在 [建立虛擬網路] 中，輸入或選取這項資訊：

    | 設定 | Value |
    | ------- | ----- |
    | Name | 輸入 MyVirtualNetwork。 |
    | 位址空間 | 輸入 *10.1.0.0/16*。 |
    | Subscription | 選取您的訂用帳戶。|
    | Resource group | 選取 [新建]，輸入 *myResourceGroup*，然後選取 [確定]。 |
    | Location | 選取 [WestCentralUS]。|
    | 子網路 - 名稱 | 輸入 mySubnet。 |
    | 子網路 - 位址範圍 | 輸入 *10.1.0.0/24*。 |
    |||
1. 將其他項目保留為預設值，然後選取 [建立]。


### <a name="create-virtual-machine"></a>建立虛擬機器

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual machine**.

1. 在 [建立虛擬機器 - 基本] 中，輸入或選取這項資訊：

    | 設定 | Value |
    | ------- | ----- |
    | **專案詳細資料** | |
    | Subscription | 選取您的訂用帳戶。 |
    | Resource group | 選取 **myResourceGroup**。 您已在上一節中建立此項目。  |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 myVm。 |
    | 地區 | 選取 [WestCentralUS]。 |
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

1. Select **Next: Disks**.

1. In **Create a virtual machine - Disks**, leave the defaults and select **Next: Networking**.

1. 在 [建立虛擬機器 - 網路功能] 中，選取這項資訊：

    | 設定 | Value |
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

## <a name="create-your-private-endpoint"></a>Create your Private Endpoint
In this section, you will create a private storage account using a Private Endpoint to it. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Storage** > **Storage account**.

1. In **Create storage account - Basics**, enter or select this information:

    | 設定 | Value |
    | ------- | ----- |
    | **專案詳細資料** | |
    | Subscription | 選取您的訂用帳戶。 |
    | Resource group | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    | **執行個體詳細資料** |  |
    | 儲存體帳戶名稱  | Enter *mystorageaccount*. 如果此名稱已被使用，請建立唯一名稱。 |
    | 地區 | 選取 [WestCentralUS]。 |
    | 效能| 保留預設值 [標準]。 |
    | 帳戶類型 | Leave the default **Storage (general purpose v2)** . |
    | 複寫 | Select **Read-access geo-redundant storage (RA-GRS)** . |
    |||
  
3. Select **Next: Networking**.
4. In **Create a storage account - Networking**, connectivity method, select **Private Endpoint**.
5. In **Create a storage account - Networking**, select **Add Private Endpoint**. 
6. In **Create Private Endpoint**, enter or select this information:

    | 設定 | Value |
    | ------- | ----- |
    | **專案詳細資料** | |
    | Subscription | 選取您的訂用帳戶。 |
    | Resource group | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    |Location|選取 [WestCentralUS]。|
    |Name|Enter *myPrivateEndpoint*.  |
    |Storage sub-resource|Leave the default **Blob**. |
    | **網路** |  |
    | 虛擬網路  | Select *MyVirtualNetwork* from resource group *myResourceGroup*. |
    | 子網路 | 選取 [mySubnet] ** 。 |
    | **私人 DNS 整合**|  |
    | 與私人 DNS 區域整合  | Leave the default **Yes**. |
    | 私人 DNS 區域  | Leave the default ** (New) privatelink.blob.core.windows.net**. |
    |||
7. 選取 [確定]。 
8. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。 
9. 當您看到 [驗證成功] 訊息時，請選取 [建立]。 
10. Browse to the storage account resource that you just created.
11. Select **Access Keys** from the left content menu.
12. Select **Copy** on the connection string for key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

Connect to the VM *myVm* from the internet as follows:

1. 在入口網站的搜尋列中，輸入 myVm。

1. 選取 [連線] 按鈕。 選取 [連線] 按鈕之後，隨即會開啟 [連線至虛擬機器]。

1. 選取 [下載 RDP 檔案]。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟下載的 .rdp* 檔案。

    1. 如果出現提示，請選取 [連接]。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。

1. 選取 [確定]。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  

## <a name="access-storage-account-privately-from-the-vm"></a>Access storage account privately from the VM

In this section, you will connect privately to the storage account using the Private Endpoint.

> [!IMPORTANT]
> DNS configuration for storage needs a manual modification on the hosts file to include the FQDN of the specific account Please modify the following file using administrator permissions on Windows: c:\Windows\System32\Drivers\etc\hosts or Linux /etc/hosts Include the DNS information for the account from previous step in the following format [Private IP Address] myaccount.blob.core.windows.net

1. 在 myVM ** 的遠端桌面中，開啟 PowerShell。
2. Enter `nslookup mystorageaccount.blob.core.windows.net` You'll receive a message similar to this:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. 安裝 [Microsoft Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)。
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. 選取 [下一步]。
8. Enter the connection string by pasting the information previously copied.
9. 選取 [下一步]。
10. 選取 [ **連接**]。
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. 請使用 AzCopy 在 Blob、檔案和表格儲存體之間傳輸資料。 


## <a name="clean-up-resources"></a>清除資源 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. 在入口網站頂端的 [搜尋] 方塊中輸入  *myResourceGroup* ，然後從搜尋結果中選取 [myResourceGroup] **  。 
2. 選取 [刪除資源群組]。 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## <a name="next-steps"></a>後續步驟
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
