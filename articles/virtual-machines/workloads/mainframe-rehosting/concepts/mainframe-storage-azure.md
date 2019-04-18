---
title: 將大型主機的存放裝置移至 Azure 儲存體
description: 可大幅擴充的 Azure 儲存體資源可協助大型主機型組織移轉和現代化 IBM z14 應用程式。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 4fea787e7fe20d60de91761811e1b69bebf3a010
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896249"
---
# <a name="move-mainframe-storage-to-azure"></a>將大型主機儲存體移至 Azure

若要在 Microsoft Azure 上執行大型主機工作負載，您需要知道大型主機的功能相較於 Azure 的如何。 可大幅擴充的儲存體資源可協助組織開始而不需要放棄它們依賴的應用程式現代化。

Azure 會提供類似大型主機的功能相當於 IBM z14 型系統 （撰寫本文時最新的模型） 的儲存體容量。 這篇文章會告訴您如何在 Azure 上取得可比較的結果。

## <a name="mainframe-storage-at-a-glance"></a>大型主機儲存體，且一目瞭然

IBM 大型主機具有兩種方式的儲存體的特性。 第一個是直接存取存放裝置 (DASD)。 第二個是循序的儲存體。 如需管理儲存體，大型主機會提供資料設備儲存體管理子系統 (DFSMS)。 它會管理各種不同的存放裝置的資料存取。

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device)指的是個別裝置的次要 （不在記憶體內部） 儲存體可用於直接存取資料的唯一位址。 一開始，詞彙 DASD 套用至轉動式磁碟、 磁性桶型代表或資料格。 不過，現在一詞也可以套用至固態儲存體裝置 (Ssd)，存放區域網路 (San)、 網路連接存放 (NAS) 和光碟機。 基於本文的目的，DASD 是指轉動式磁碟、 San、 和 Ssd。

相較於 DASD 儲存體，循序大型主機上的儲存體是指其中是起始點，從存取則讀取或寫入資料行中的磁帶機等裝置。

存放裝置通常會連接使用光纖連線 (FICON)，或使用的大型主機的 IO 匯流排上直接存取[HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm)，高速通訊的伺服器上的分割區之間的 IBM 技術hypervisor。

大部分的大型主機系統會將儲存體分成兩種類型：

- *線上儲存體*（也稱為經常性存取儲存體） 所需的日常作業。 DASD 儲存體通常用於此目的。 不過，循序的儲存體，例如每日磁帶備份 （邏輯或實體），也可以使用針對此目的。

- *封存儲存體*（也稱為冷儲存體） 不保證會掛接在指定的時間。 相反地，它是掛接，然後視需要存取。 封存儲存體通常是使用儲存體的循序的磁帶備份 （邏輯或實體） 來實作。

## <a name="mainframe-versus-io-latency-and-iops"></a>與 IO 延遲以及 IOPS 的大型主機

大型主機通常用於需要高效能 IO 和低的 IO 延遲應用程式。 他們可以執行這項操作使用 IO 裝置和 HiperSockets FICON 連線。 HiperSockets 使用時，應用程式和裝置直接連接到大型主機的 IO 通道，就可以達成延遲 （毫秒）。

## <a name="azure-storage-at-a-glance"></a>Azure 儲存體，且一目瞭然

Azure 基礎結構做為-即服務 ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) 儲存體選項提供可比較的大型主機的容量。

Microsoft 提供數 pb 的儲存體，在 Azure 中裝載的應用程式的價值，您有數個儲存體選項。 這些範圍從高效能的 SSD 儲存體到大量的儲存體和封存的低成本的 blob 儲存體。 此外，Azure 提供儲存體的資料備援選項 — 會努力在大型主機環境中設定的項目。

Azure 儲存體是當作[Azure 磁碟](/azure/virtual-machines/windows/managed-disks-overview)， [Azure 檔案](/azure/storage/files/storage-files-introduction)，並[Azure Blob](/azure/storage/blobs/storage-blobs-overview)為下表摘要說明。 深入了解[何時使用每個](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>類型</th><th>描述</th><th>當您想要實現下列目標時，即可使用：</th></tr>
</thead>
<tbody>
<tr><td>Azure 檔案
</td>
<td>
提供 SMB 介面、 用戶端程式庫，以及<a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a>介面，允許從任何位置存取儲存的檔案。
</td>
<td><ul>
<li>當應用程式使用它，並在 Azure 中執行其他應用程式之間共用資料的原生檔案系統 Api 原形雲端應用程式。</li>
<li>儲存開發和偵錯工具需要存取從許多 Vm。</li>
</ul>
</td>
</tr>
<tr><td>Azure Blob
</td>
<td>提供用戶端程式庫和<a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a>介面，允許儲存和存取區塊 blob 中大規模的非結構化的資料。 也支援將 <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> 用於企業巨量資料分析解決方案。
</td>
<td><ul>
<li>在應用程式支援串流及隨機存取案例。</li>
<li>可以從任何地方存取應用程式資料。</li>
<li>在 Azure 上建置的企業資料湖，並執行巨量資料分析。</li>
</ul></td>
</tr>
<tr><td>Azure 磁碟
</td>
<td>提供用戶端程式庫和<a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a>介面，允許持續儲存和存取從連接的虛擬硬碟的資料。
</td>
<td><ul>
<li>隨即轉移使用原生檔案系統 Api 來讀取和寫入資料至永續性磁碟的應用程式。</li>
<li>儲存並不需要從外部磁碟所連接的 VM 存取的資料。</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure 經常性存取 （線上） 」 和 「 冷 （封存） 儲存體

指定系統的儲存體類型取決於系統，包括儲存體大小、 輸送量和 IOPS 的需求。 對於大型主機上的 DASD 型儲存體，在 Azure 上的應用程式通常改用 Azure 磁碟機儲存體。 對於大型主機封存儲存體，Azure 會使用 blob 儲存體。

Ssd 提供在 Azure 上的最高的儲存體效能。 （在撰寫本文件） 有下列選項：

| 類型         | 大小           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB 到 64 TB  | 以 160,000 1,200 個 IOPS |
| 進階 SSD  | 32 GB 到 32 TB | 12 到 15,000 位 IOPS     |
| 標準 SSD | 32 GB 到 32 TB | 12 至 2,000 部 IOPS      |

Blob 儲存體提供 Azure 上的最大的磁碟區的儲存體。 除了儲存體大小，Azure 會提供 managed 和 unmanaged 兩種儲存體。 使用受控儲存體，Azure 會負責管理基礎儲存體帳戶。 使用非受控儲存體，使用者會負責設定適當大小的 Azure 儲存體帳戶，以符合儲存體需求。

## <a name="next-steps"></a>後續步驟

- [大型主機移轉](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [大型主機重新裝載的 Azure 虛擬機器上](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [將大型主機計算移至 Azure](mainframe-compute-Azure.md)
- [決定何時使用 Azure Blob、 Azure 檔案服務或 Azure 磁碟](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [標準 SSD 受控磁碟的 Azure VM 工作負載](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>IBM 資源

- [平行 Sysplex 上 IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 和結合性功能：進階功能](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [針對 Db2 pureScale 功能安裝建立必要使用者](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - 建立執行個體命令](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale 叢集資料庫解決方案](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [大型電腦應用程式的 Microsoft Azure Government 雲端](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 和 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>其他移轉資源

- [Platform Modernization Alliance：在 Azure 上的 IBM Db2](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Azure 虛擬資料中心：原形移轉指南](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
