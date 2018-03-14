---
title: "針對 Azure Linux 虛擬機器上的連結 VHD 進行疑難排解 | Microsoft Docs"
description: "如何針對 Linux VM 發生未預期的重新開機等問題，或針對刪除內含連結 VHD 的儲存體帳戶時所發生的問題進行疑難排解。"
keywords: "ssh 連線被拒, ssh 錯誤, azure ssh, ssh 連線失敗"
services: virtual-machines-linux
author: iainfoulds
manager: jeconnoc
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/28/2018
ms.author: iainfou
ms.openlocfilehash: 10f5e2f4a48d335c6b7d05b5eb5b577765675c0e
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2018
---
# <a name="troubleshoot-attached-vhds-on-azure-linux-virtual-machines"></a>針對 Azure Linux 虛擬機器上的連結 VHD 進行疑難排解

對於作業系統磁碟和任何連結的資料磁碟，Azure 虛擬機器需仰賴虛擬硬碟 (VHD)。 VHD 會以分頁 Blob 的形式儲存在一或多個 Azure 儲存體帳戶中。 本文說明如何針對 VHD 可能發生的常見問題進行疑難排解。 

  * [包含連結 VHD 的 VM 非預期重新開機]
  * [Resource Manager 部署中的儲存體刪除錯誤]

## <a name="you-are-experiencing-unexpected-reboots">包含連結 VHD 的 VM 非預期重新開機</a>

當 Azure 虛擬機器 (VM) 有大量的連結 VHD 位於同一個儲存體帳戶中時，您可能會超出個別儲存體帳戶的延展性目標，導致 VM 作業失敗。 檢查儲存體帳戶的每分鐘計量 (**TotalRequests**/**TotalIngress**/**TotalEgress**)，查看是否有超出儲存體帳戶延展性目標的流量暴增情況。 請參閱「計量顯示 PercentThrottlingError 增加」一節，取得如何判斷儲存體帳戶是否發生節流作業的協助。

一般來說，VHD 每次來自虛擬機器的個別流量輸入或輸出，都會在底層的分頁 Blob 上轉換為 **Get Page** 或 **Put Page** 作業。 因此，您可以在環境中使用預估的 IOPS，依據應用程式的特定行為調節單一儲存體帳戶中應該具備的 VHD 數量。 Microsoft 建議單一儲存體帳戶中的磁碟少於 40 個。 如需關於儲存體帳戶延展性目標的詳細資訊，尤其是您所使用之儲存體帳戶類型的要求率總計和頻寬總計，請參閱 [Azure 儲存體延展性和效能目標](../../storage/common/storage-scalability-targets.md)。

如果您超過儲存體帳戶的延展性目標，請將 VHD 放在多個的儲存體帳戶中，以減少每個個別帳戶中的活動。

## <a name="storage-delete-errors-in-rm">Resource Manager 部署中的儲存體刪除錯誤</a>

本節提供疑難排解指引，協助您解決在 Azure Resource Manager 部署中嘗試刪除 Azure 儲存體帳戶、容器或 Blob 時發生的以下任一錯誤。

>**無法刪除儲存體帳戶 'StorageAccountName'。錯誤︰無法刪除儲存體帳戶，因為正在使用其構件。**

>**無法刪除 # 個容器 (共 # 個)：<br>vhds：目前容器上有租用，但要求中沒有指定任何租用識別碼。**

>**無法刪除 # 個 Blob (共 # 個)：<br>：目前 Blob 上有租用，但要求中沒有指定任何租用識別碼。**

Azure VM 中使用的 VHD 是以分頁 Blob 形式儲存在 Azure 標準或進階儲存體帳戶中的 .vhd 檔案。  如需 Azure 磁碟的詳細資訊，[請參閱](../../virtual-machines/windows/about-disks-and-vhds.md)。 Azure 能預防刪除已連接 VM 的磁碟，以避免損毀。 它也能預防刪除分頁 Blob 已連結 VM 的容器和儲存體帳戶。 

收到以上任一錯誤時，將儲存體帳戶、容器或 Blob 刪除的程序如下： 
1. [識別連接 VM 的 Blob](#step-1-identify-blobs-attached-to-a-vm)
2. [刪除已連接**作業系統磁碟**的 VM](#step-2-delete-vm-to-detach-os-disk)
3. [中斷連結其餘 VM 的所有**資料磁碟**](#step-3-detach-data-disk-from-the-vm)

完成上述步驟之後，請再嘗試將儲存體帳戶、容器或 Blob 刪除。

### <a name="step-1-identify-blob-attached-to-a-vm"></a>步驟 1：識別連接 VM 的 Blob

#### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>案例 1：刪除 Blob - 識別連接的 VM
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表中，選取 [所有資源]。 移至儲存體帳戶，在 [Blob 服務] 下方選取 [容器]，然後瀏覽到要刪除的 Blob。
3. 如果 Blob 的 [租用狀態] 為 [已租用]，請以滑鼠右鍵按一下並選取 [編輯中繼資料]，以開啟 Blob 中繼資料窗格。 

    ![入口網站的螢幕擷取畫面，其中醒目提示儲存體帳戶 Blob 及按一下滑鼠右鍵並選取 [編輯中繼資料]](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. 在 Blob 中繼資料窗格中，檢查和記下 **MicrosoftAzureCompute_VMName** 的值。 這個值是連接 VHD 的目標 VM 名稱。 (如果這個欄位不存在，請參閱**重要事項**)
5. 在 Blob 中繼資料窗格中，檢查和記下 **MicrosoftAzureCompute_DiskType** 的值。 這個值能識別連結的磁碟是作業系統磁碟還是資料磁碟 (如果這個欄位不存在，請參閱**重要事項**)。 

     ![開啟儲存體「Blob 中繼資料」窗格的入口網站螢幕擷取畫面](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. 如果 Blob 磁碟類型是 **OSDisk**，請遵循[步驟 2：刪除 VM 以中斷作業系統磁碟的連線](#step-2-delete-vm-to-detach-os-disk)。 否則，如果 Blob 磁碟類型是 **DataDisk**，請遵循[步驟 3：中斷資料磁碟與 VM 的連線](#step-3-detach-data-disk-from-the-vm)。 

> [!IMPORTANT]
> 如果 **MicrosoftAzureCompute_VMName** 和 **MicrosoftAzureCompute_DiskType** 未出現在 Blob 中繼資料內，代表 Blob 已明確租用，而且未連接 VM。 已租用的 Blob 必須先中斷租用後才能予以刪除。 若要中斷租用，請在 Blob 上按一下滑鼠右鍵，然後選取 [中斷租用]。 即使未連結 VM 的已租用 Blob 無法刪除，不過它無法預防容器或儲存體帳戶遭到刪除。

#### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>案例 2：刪除容器 - 識別連接 VM 之容器內的所有 Blob
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表中，選取 [所有資源]。 移至儲存體帳戶，在 [Blob 服務] 下方選取 [容器]，然後找出要刪除的容器。
3. 按一下開啟容器，容器內的 Blob 清單將會出現。 在清單中識別 Blob 類型 = **分頁 Blob** 且租用狀態 = **已租用**的所有 Blob。 遵循[案例 1](#step-1-identify-blobs-attached-to-a-vm) 以辨識分別與這些 Blob 相關聯的 VM。

    ![入口網站的螢幕擷取畫面，反白顯示儲存體帳戶 Blob、[租用狀態] 和 [已租用]](./media/troubleshoot-vhds/utd-disks-sm.png)

4. 遵循[步驟 2](#step-2-delete-vm-to-detach-os-disk) 和[步驟 3](#step-3-detach-data-disk-from-the-vm) 來刪除連接 **OSDisk** 的 VM 及中斷連結 **DataDisk**。 

#### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>案例 3：刪除儲存體帳戶 - 識別儲存體帳戶內已連接 VM 的所有 Blob
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表中，選取 [所有資源]。 移至儲存體帳戶，在 [Blob 服務] 下方選取 [容器]。

    ![入口網站的螢幕擷取畫面，反白顯示儲存體帳戶容器、[租用狀態] 和 [已租用]](./media/troubleshoot-vhds/utd-containers-sm.png)

3. 在 [容器] 窗格中，識別所有 [租用狀態] 為 [已租用] 的容器，然後遵循[案例 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) 來處理每個**已租用**的容器。
4. 遵循[步驟 2](#step-2-delete-vm-to-detach-os-disk) 和[步驟 3](#step-3-detach-data-disk-from-the-vm) 來刪除連接 **OSDisk** 的 VM 及中斷連結 **DataDisk**。 

### <a name="step-2-delete-vm-to-detach-os-disk"></a>步驟 2：刪除 VM 以中斷作業系統磁碟的連線
如果 VHD 是作業系統磁碟，您必須先刪除 VM 才能刪除連接的 VHD。 完成下列步驟後，您就不需要針對連接相同 VM 的資料磁碟執行額外動作：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表上，選取 [虛擬機器]。
3. 選取連接 VHD 的 VM。
4. 確定沒有正在使用虛擬機器的項目，而且您不再需要虛擬機器。
5. 在 [虛擬機器詳細資料] 窗格頂端，選取 [刪除]，然後按一下 [是] 進行確認。
6. VM 應該會遭到刪除，但 VHD 應該會保留下來。 不過，VHD 應該不會再連接 VM 或再有租用。 釋放租用可能需要幾分鐘的時間。 若要確認租用已釋放，請瀏覽至 Blob 位置；在 [Blob 屬性] 窗格中，[租用狀態] 應該會是 [可用]。

### <a name="step-3-detach-data-disk-from-the-vm"></a>步驟 3：中斷資料磁碟與 VM 的連線
如果 VHD 是資料磁碟，請從 VM 中斷連結 VHD，以便移除租用：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表上，選取 [虛擬機器]。
3. 選取連接 VHD 的 VM。
4. 在 [虛擬機器詳細資料] 窗格中，選取 [磁碟]。
5. 選取連接 VHD 且要刪除的資料磁碟。 您可以藉由檢查 VHD 的 URL 來判斷磁碟中連接的 Blob。
6. 若要確認 Blob 位置，您可以按一下磁碟以查看 [VHD URI] 欄位中的路徑。
7. 在 [磁碟] 窗格頂端，選取 [編輯]。
8. 針對要刪除的資料磁碟按一下**中斷連結圖示**。

     ![開啟儲存體「Blob 中繼資料」窗格的入口網站螢幕擷取畫面](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. 選取 [ **儲存**]。 磁碟現在會與 VM 中斷連結，不再租用 VHD。 釋放租用可能需要幾分鐘的時間。 若要確認租用已釋放，請瀏覽至 Blob 位置；在 [Blob 屬性] 窗格中，[租用狀態] 值應該會是 [已解除鎖定] 或 [可用]。



[包含連結 VHD 的 VM 非預期重新開機]: #you-are-experiencing-unexpected-reboots
[Resource Manager 部署中的儲存體刪除錯誤]: #storage-delete-errors-in-rm