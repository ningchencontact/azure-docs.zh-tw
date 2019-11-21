---
title: 規劃 Azure 檔案服務部署 | Microsoft Docs
description: 了解規劃 Azure 檔案部署時的考量事項。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7d11dc70a78fcec62032c2a6af168bd306c9d416
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227875"
---
# <a name="planning-for-an-azure-files-deployment"></a>規劃 Azure 檔案服務部署

[Azure 檔案服務](storage-files-introduction.md)可提供在雲端中完全受控的檔案共用，可透過業界標準 SMB 通訊協定加以存取。 因為 Azure 檔案服務受到完整管理，所以部署於生產環境案例遠易於部署及管理檔案伺服器或 NAS 裝置。 針對在組織中部署生產環境使用的 Azure 檔案共用，本文說明應考慮的主題。

## <a name="management-concepts"></a>管理概念

 下圖說明 Azure 檔案服務管理建構：

![檔案結構](./media/storage-files-introduction/files-concepts.png)

* **儲存體帳戶**：一律透過儲存體帳戶來存取 Azure 儲存體。 如需關於儲存體帳戶容量的詳細資訊，請參閱[延展性和效能目標](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* **共用**：檔案儲存體共用是 Azure 中的 SMB 檔案共用。 所有的目錄和檔案必須在上層共用中建立。 An account can contain an unlimited number of shares and a share can store an unlimited number of files, up to the total capacity of the file share. The total capacity for premium and standard file shares is 100 TiB.

* **目錄**：選擇性的目錄階層。

* **檔案**：共用中的檔案。 檔案的大小可高達 1 TiB。

* **URL 格式**：若是透過檔案 REST 通訊協定以進行 Azure 檔案共用的要求，可以使用下列 URL 格式來定址檔案：

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>資料存取方法

Azure 檔案服務提供兩種方便的內建資料存取方法，您可以個別或結合使用來存取資料：

1. **雲端直接存取**：利用業界標準伺服器訊息區 (SMB) 通訊協定或透過檔案 REST API，[Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 及/或 [Linux](storage-how-to-use-files-linux.md) 可掛接任何 Azure 檔案共用。 共用上的檔案藉由 SMB 讀取和寫入時，會直接在 Azure 中的檔案共用上進行。 若要由 Azure 中的 VM 掛接，作業系統中的 SMB 用戶端必須至少支援 SMB 2.1。 若要掛接在內部 (例如使用者的工作站上)，工作站所支援的 SMB 用戶端必須至少支援 SMB 3.0 (含加密)。 除了 SMB 之外，新的應用程式或服務可以透過檔案 REST 直接存取檔案共用，為軟體開發提供了方便且可擴充的應用程式開發介面。
2. **Azure 檔案同步**：藉由 Azure 檔案同步，共用可以複寫到內部部署或 Azure 中的 Windows Server。 您的使用者可透過 Windows Server 存取檔案共用，像是透過 SMB 或 NFS 共用。 這適用於從遠離 Azure 資料中心的位置 (例如分公司) 存取和修改資料的情況。 資料可以在多個 Windows Server 端點之間複寫，例如多個分公司之間。 最後，資料可能會分層至 Azure 檔案服務，因此透過伺服器仍然可以存取所有資料，但伺服器並沒有資料的完整複本。 資料實際上是在使用者開啟時順暢地取回。

下表說明使用者和應用程式如何存取您的 Azure 檔案共用：

| | 雲端直接存取 | Azure 檔案同步 |
|------------------------|------------|-----------------|
| 您需要使用哪些通訊協定？ | Azure 檔案服務支援 SMB 2.1、SMB 3.0 和檔案 REST API。 | 透過 Windows Server 上任何支援的通訊協定 (SMB、NFS、FTPS 等) 存取您的 Azure 檔案共用 |  
| 您會在何處執行工作負載？ | **Azure 中**：Azure 檔案服務提供對資料的直接存取。 | **低速網路的內部部署**：Windows、Linux 及 macOS 用戶端可掛接區域內部部署的 Windows 檔案共用，作為 Azure 檔案共用的快速快取。 |
| 您需要何種 ACL 層級？ | 共用和檔案層級。 | 共用、檔案和使用者層級。 |

## <a name="data-security"></a>資料安全性

Azure 檔案服務具有數個內建的選項，可用於確保資料安全性：

* 支援兩種網路通訊協定的加密：SMB 3.0 加密和經由 HTTPS 的檔案 REST。 依照預設： 
    * Clients that support SMB 3.0 encryption send and receive data over an encrypted channel.
    * Clients that do not support SMB 3.0 with encryption can communicate intra-datacenter over SMB 2.1 or SMB 3.0 without encryption. 不允許 SMB 用戶端透過 SMB 2.1 或 SMB 3.0 進行無加密的資料中心之間通訊。
    * 用戶端可以藉由 HTTP 或 HTTPS 透過檔案 REST 進行通訊。
* 待用加密 ([Azure 儲存體服務加密](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json))：為所有儲存體帳戶啟用「儲存體服務加密」(SSE)。 靜止資料是使用完全受控金鑰加密。 待用加密不會增加儲存成本或降低效能。 
* Optional requirement of encrypted data in-transit: when selected, Azure Files rejects access to the data over unencrypted channels. 具體來說，只允許具有加密連線的 HTTPS 和 SMB 3.0。

    > [!Important]  
    > 要求資料安全傳輸將導致舊版 SMB 用戶端通訊失敗，因為它無法與加密的 SMB 3.0 通訊。 如需詳細資訊，請參閱[掛接在 Windows 上](storage-how-to-use-files-windows.md)、[掛接在 Linux 上](storage-how-to-use-files-linux.md)、[掛接在 macOS 上](storage-how-to-use-files-mac.md)。

為了有最高安全性，當您使用新式用戶端來存取資料時，強烈建議您一律啟用加密待用資料與加密傳輸資料。 例如，如果您需要在僅支援 SMB 2.1 的 Windows Server 2008 R2 VM 上掛接共用，因為 SMB 2.1 不支持加密，因此儲存體帳戶必須允許未加密的流量。

如果您使用 Azure 檔案同步來存取 Azure 檔案共用，則無論是否需要對待用資料進行加密，我們一律會使用加密的 HTTPS 和 SMB 3.0 將資料同步處理至 Windows Server。

## <a name="file-share-performance-tiers"></a>檔案共用效能層級

Azure Files offers two performance tiers: standard and premium.

### <a name="standard-file-shares"></a>標準檔案共用

Standard file shares are backed by hard disk drives (HDDs). Standard file shares provide reliable performance for IO workloads that are less sensitive to performance variability such as general-purpose file shares and dev/test environments. 標準檔案共用僅適用於隨用隨付計費模型。

> [!IMPORTANT]
> If you want to use file shares larger than 5 TiB, see the [Onboard to larger file shares (standard tier)](#onboard-to-larger-file-shares-standard-tier) section for steps to onboard, as well as regional availability and restrictions.

### <a name="premium-file-shares"></a>Premium file shares

Premium file shares are backed by solid-state drives (SSDs). Premium file shares provide consistent high performance and low latency, within single-digit milliseconds for most IO operations, for IO-intensive workloads. This makes them suitable for a wide variety of workloads like databases, web site hosting, and development environments. 進階檔案共用僅適用於佈建計費模型。 Premium file shares use a deployment model separate from standard file shares.

Azure Backup is available for premium file shares and Azure Kubernetes Service supports premium file shares in version 1.13 and above.

If you'd like to learn how to create a premium file share, see our article on the subject: [How to create an Azure premium file storage account](storage-how-to-create-premium-fileshare.md).

Currently, you cannot directly convert between a standard file share and a premium file share. If you would like to switch to either tier, you must create a new file share in that tier and manually copy the data from your original share to the new share you created. You can do this using any of the Azure Files supported copy tools, such as Robocopy or AzCopy.

> [!IMPORTANT]
> Premium file shares are available with LRS in most regions that offer storage accounts and with ZRS in a smaller subset of regions. To find out if premium file shares are currently available in your region, see the [products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) page for Azure. To find out what regions support ZRS, see [Support coverage and regional availability](../common/storage-redundancy-zrs.md#support-coverage-and-regional-availability).
>
> To help us prioritize new regions and premium tier features, please fill out this [survey](https://aka.ms/pfsfeedback).

#### <a name="provisioned-shares"></a>佈建共用

進階檔案共用會以固定的 GiB/IOPS/輸送量比例為基礎佈建。 對於每個佈建的 GiB，共用將會發出一個 IOPS 和 0.1 MiB/秒輸送量，直到每個共用的上限為止。 允許佈建的最小值為 100 GiB 與最小 IOPS/輸送量。

在盡可能達成的基礎上，每個佈建之儲存體 Gib 的所有共用都可高載至最多三個 IOPS，並持續 60 分鐘或更久的時間 (視共用大小而定)。 新的共用一開始有以佈建容量為基礎的完整高載額度。

Shares must be provisioned in 1 GiB increments. Minimum size is 100 GiB, next size is 101 GiB and so on.

> [!TIP]
> Baseline IOPS = 1 * provisioned GiB. (Up to a max of 100,000 IOPS).
>
> Burst Limit = 3 * Baseline IOPS. (Up to a max of 100,000 IOPS).
>
> egress rate = 60 MiB/s + 0.06 * provisioned GiB
>
> ingress rate = 40 MiB/s + 0.04 * provisioned GiB

Provisioned share size is specified by share quota. Share quota can be increased at any time but can be decreased only after 24 hours since the last increase. After waiting for 24 hours without a quota increase, you can decrease the share quota as many times as you like, until you increase it again. IOPS/Throughput scale changes will be effective within a few minutes after the size change.

It is possible to decrease the size of your provisioned share below your used GiB. If you do this, you will not lose data but, you will still be billed for the size used and receive the performance (baseline IOPS, throughput, and burst IOPS) of the provisioned share, not the size used.

The following table illustrates a few examples of these formulae for the provisioned share sizes:

|Capacity (GiB) | 基準 IOPS | Burst IOPS | Egress (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | 最多 300     | 66   | 44   |
|500         | 500     | Up to 1,500   | 90   | 60   |
|1,024       | 1,024   | Up to 3,072   | 122   | 81   |
|5,120       | 5,120   | Up to 15,360  | 368   | 245   |
|10,240      | 10,240  | Up to 30,720  | 675 | 450   |
|33,792      | 33,792  | Up to 100,000 | 2,088 | 1,392   |
|51,200      | 51,200  | Up to 100,000 | 3,132 | 2,088   |
|102,400     | 100,000 家 | Up to 100,000 | 6,204 | 4,136   |

> [!NOTE]
> File shares performance is subject to machine network limits, available network bandwidth, IO sizes, parallelism, among many other factors. For example, based on internal testing with 8 KiB read/write IO sizes, a single Windows virtual machine, *Standard F16s_v2*, connected to premium file share over SMB could achieve 20K read IOPS and 15K write IOPS. With 512 MiB read/write IO sizes, the same VM could achieve 1.1 GiB/s egress and 370 MiB/s ingress throughput. To achieve maximum performance scale, spread the load across multiple VMs. Please refer [troubleshooting guide](storage-troubleshooting-files-performance.md) for some common performance issues and workarounds.

#### <a name="bursting"></a>Bursting

Premium file shares can burst their IOPS up to a factor of three. Bursting is automated and operates based on a credit system. Bursting works on a best effort basis and the burst limit is not a guarantee, file shares can burst *up to* the limit.

Credits accumulate in a burst bucket whenever traffic for your file share is below baseline IOPS. For example, a 100 GiB share has 100 baseline IOPS. If actual traffic on the share was 40 IOPS for a specific 1-second interval, then the 60 unused IOPS are credited to a burst bucket. These credits will then be used later when operations would exceed the baseline IOPs.

> [!TIP]
> Size of the burst bucket = Baseline IOPS * 2 * 3600.

Whenever a share exceeds the baseline IOPS and has credits in a burst bucket, it will burst. Shares can continue to burst as long as credits are remaining, though shares smaller than 50 TiB will only stay at the burst limit for up to an hour. Shares larger than 50 TiB can technically exceed this one hour limit, up to two hours but, this is based on the number of burst credits accrued. Each IO beyond baseline IOPS consumes one credit and once all credits are consumed the share would return to baseline IOPS.

Share credits have three states:

- Accruing, when the file share is using less than the baseline IOPS.
- Declining, when the file share is bursting.
- Remaining constant, when there are either no credits or baseline IOPS are in use.

New file shares start with the full number of credits in its burst bucket. Burst credits will not be accrued if the share IOPS fall below baseline IOPS due to throttling by the server.

## <a name="file-share-redundancy"></a>檔案共用備援

Azure Files standard shares supports four data redundancy options: locally redundant storage (LRS), zone redundant storage (ZRS), geo-redundant storage (GRS), and geo-zone-redundant storage (GZRS) (preview).

Azure Files premium shares support both LRS and ZRS, ZRS is currently available in a smaller subset of regions.

下列各節說明不同備援選項之間的差異：

### <a name="locally-redundant-storage"></a>本地備援儲存體

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>區域備援儲存體

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>異地備援儲存體

> [!Warning]  
> 若您使用 Azure 檔案共用作為 GRS 儲存體帳戶中的雲端端點，您不應該啟動儲存體帳戶的容錯移轉。 這麼做將導致同步停止運作，且可能會在新分層的檔案中產生未預期的資料遺失。 若發生 Azure 區域遺失，Microsoft 將會觸發與 Azure 檔案同步相容的儲存體帳戶容錯移轉。

異地備援儲存體 (GRS) 設計為將您的資料複寫到與主要區域相隔數百英哩遠的次要區域，以在指定年份為物件提供至少 99.99999999999999% (16 9's) 的持久性。 如果您的儲存體帳戶已啟用 GRS，即使主要區域發生全區中斷或災害而無法復原的情況，您的資料仍會是永久性。

If you opt for read-access geo-redundant storage (RA-GRS), you should know that Azure File does not support read-access geo-redundant storage (RA-GRS) in any region at this time. File shares in the RA-GRS storage account work like they would in GRS accounts and are charged GRS prices.

GRS 會將您的資料複寫到次要區域中的另一個資料中心，但如果 Microsoft 起始從主要到次要區域的容錯移轉，該資料則為唯讀。

For a storage account with GRS enabled, all data is first replicated with locally redundant storage (LRS). 更新會先認可到主要位置，並使用 LRS 進行複寫。 接著會使用 GRS，以非同步的方式將更新複寫到次要區域。 當資料寫入次要位置時，也會使用 LRS 在該位置中複寫。

主要和次要區域會管理分散在儲存體縮放單位內不同容錯網域和升級網域之間的複本。 儲存體縮放單位是資料中心內的基本複寫單位。 這個層級的複寫是由 LRS 提供；如需詳細資訊，請參閱[本地備援儲存體 (LRS)：適用於 Azure 儲存體的低成本資料備援](../common/storage-redundancy-lrs.md)。

當您決定要使用的複寫選項時，請記住下列幾點：

* Geo-zone-redundant storage (GZRS) (preview) provides high availability together with maximum durability by replicating data synchronously across three Azure availability zones and then replicating data asynchronously to the secondary region. You can also enable read access to the secondary region. GZRS is designed to provide at least 99.99999999999999% (16 9's) durability of objects over a given year. For more information on GZRS, see [Geo-zone-redundant storage for highly availability and maximum durability (preview)](../common/storage-redundancy-gzrs.md).
* Zone-redundant storage (ZRS) provides highly availability with synchronous replication and may be a better choice for some scenarios than GRS. 如需有關 ZRS 的詳細資訊，請參閱 [ZRS](../common/storage-redundancy-zrs.md)。
* 非同步複寫會涉及從將資料寫入主要區域，到將資料複寫至次要區域這段時間的延遲。 當發生區域性災害時，如果無法從主要區域復原尚未複寫到次要區域的變更，則這些變更可能會遺失。
* 使用 GRS 時，複本不提供讀取或寫入存取，除非 Microsoft 起始對次要區域的容錯移轉。 在容錯移轉的情況下，當容錯移轉完成時，您會有該資料的讀取和寫入存取權。 如需詳細資訊，請參閱[災害復原指導方針](../common/storage-disaster-recovery-guidance.md)。

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Onboard to larger file shares (standard tier)

This section only applies to the standard file shares. All premium file shares are available with 100 TiB capacity.

### <a name="restrictions"></a>限制

- LRS/ZRS to GRS/GZRS account conversion will not be possible for any storage account with large file shares enabled.

### <a name="regional-availability"></a>區域可用性

Standard file shares are available in all regions up to 5 TiB. In certain regions, they are available with a 100 TiB limit, those regions are listed in the following table:

|地區 |Supported redundancy |
|-------|---------|
|澳大利亞東部 |LRS     |
|澳大利亞東南部|LRS |
|加拿大中部  |LRS     |
|加拿大東部     |LRS     |
|印度中部  |LRS     |
|Central US*   |LRS     |
|東亞      |LRS     |
|East US*        |LRS     |
|East US 2*      |LRS     |
|法國中部 |LRS, ZRS|
|法國南部   |LRS     |
|北歐   |LRS     |
|印度南部    |LRS     |
|東南亞 |LRS, ZRS|
|美國中西部|LRS     |
|West Europe*    |LRS, ZRS|
|West US*        |LRS     |
|美國西部 2      |LRS, ZRS|

\* Supported for new accounts, not all existing accounts have completed the upgrade process. You can check if your existing storage accounts have completed the upgrade process by attempting to [Enable large file shares](storage-files-how-to-create-large-file-share.md).

To help us prioritize new regions and features, please fill out this [survey](https://aka.ms/azurefilesatscalesurvey).

### <a name="enable-and-create-larger-file-shares"></a>Enable and create larger file shares

To begin using larger file shares, see our article [How to enable and create large file shares](storage-files-how-to-create-large-file-share.md).

## <a name="data-growth-pattern"></a>資料成長模式

Today, the maximum size for an Azure file share is 100 TiB. 由於目前的此一限制，當您在部署 Azure 檔案共用時，必須考量預期的資料成長。

It is possible to sync multiple Azure file shares to a single Windows File Server with Azure File Sync. This allows you to ensure that older, large file shares that you may have on-premises can be brought into Azure File Sync. For more information, see [Planning for an Azure File Sync Deployment](storage-files-planning.md).

## <a name="data-transfer-method"></a>資料傳輸方法

有許多簡單的選項可從現有檔案共用 (例如內部部署檔案共用) 大量傳輸資料到 Azure 檔案服務。 部分常用方法包括 (非完整清單)：

* **Azure 檔案同步**：在 Azure 檔案共用 (雲端端點) 和 Windows 目錄命名空間 (伺服器端點) 之間的初次同步處理中，Azure 檔案同步會從現有檔案共用將所有資料複寫至 Azure 檔案服務。
* **[Azure 匯入/匯出](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** ：Azure 匯入/匯出服務可讓您將硬碟寄送到 Azure 資料中心，以便將大量資料安全地傳入 Azure 檔案共用。 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** ：Robocopy 是知名的複製工具，隨附於 Windows 和 Windows Server。 Robocopy 可在本機掛接檔案共用，然後將該掛接位置作為 Robocopy 命令中的目的地，以將資料傳輸到 Azure 檔案服務中。
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** ：AzCopy 是命令列公用程式，設計為使用最佳效能的簡單命令，將資料複製到 Azure 檔案服務與 Azure Blob 儲存體，以及從其中複製資料。

## <a name="next-steps"></a>後續步驟
* [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
* [部署 Azure 檔案](storage-files-deployment-guide.md)
* [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
