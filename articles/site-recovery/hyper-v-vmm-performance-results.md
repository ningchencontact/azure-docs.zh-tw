---
title: 使用 Azure Site Recovery 將 VMM 雲端中的 Hyper-V VM 複寫至次要網站的測試結果 | Microsoft Docs
description: 本文提供使用 Azure Site Recovery 將 VMM 雲端中的 Hyper-V VM 複寫至次要網站之效能測試的相關資訊。
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: a7413b2dcb24a42092eb2af9816b1d29a8306e19
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377221"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>將 Hyper-V 複寫至次要網站的測試結果


本文提供將 System Center Virtual Machine Manager (VMM) 雲端中的 Hyper-V VM 複寫至次要資料中心時的效能測試結果。

## <a name="test-goals"></a>測試目標

測試目標是檢查 Site Recovery 在穩定狀態複寫期間的執行情況。

- 當 VM 已完成初始複寫，且正在同步處理差異變更時，就會發生穩定狀態複寫。
- 請務必使用穩定狀態測量效能，因為除非發生非預期的中斷，否則它是大多數 VM 維持的狀態。
- 測試部署是由兩個內部部署網站所組成，且每個網站都有一個 VMM 伺服器。 此測試部署通常屬於總公司/分公司部署，以總公司作為主要網站，而分公司作為次要或復原網站。

## <a name="what-we-did"></a>我們執行的動作

以下是我們在測試階段中所執行的動作：

1. 使用 VMM 範本建立 VM。
2. 啟動 VM，並擷取 12 小時內的基準效能計量。
3. 在主要和復原 VMM 伺服器上建立雲端。
4. 在 Site Recovery 中設定複寫，包括來源和復原雲端之間的對應。
5. 啟用 VM 的保護，並允許它們完成初始複寫。
6. 等待幾個小時讓系統趨於穩定。
7. 擷取 12 小時內的效能計量，且所有 VM 在這 12 小時內都維持於預期的複寫狀態。
8. 測量基準效能計量和複寫效能計量之間的差異。


## <a name="primary-server-performance"></a>主要伺服器效能

* Hyper-V 複本 (由 Site Recovery 使用) 會使用主要伺服器上最小的儲存負荷，以非同步方式追蹤記錄檔的變更。
* Hyper-V 複本會利用自我維護的記憶體快取，以減少 IOPS 的追蹤負荷。 它會在記憶體中儲存對 VHDX 的寫入，並在記錄傳送至復原網站之前，將這些寫入排清到記錄檔中。 如果寫入達到預先決定的限制，也會發生磁碟排清。
* 下圖顯示複寫的穩定狀態 IOPS 負荷。 我們可以看到複寫的 IOPS 負荷大約是 5%，這相當低。

  ![主要結果](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V 複本會利用主要伺服器上的記憶體來最佳化磁碟效能。 如下圖所示，主要叢集中所有伺服器上的記憶體負荷都很低。 所顯示的記憶體負荷是複寫所使用的記憶體相較於 Hyper-V 伺服器上已安裝的記憶體總數的百分比。

![主要結果](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V 複本的 CPU 負荷最低。 如圖所示，複寫負荷位於 2-3% 的範圍內。

![主要結果](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>次要伺服器效能

Hyper-V 複本會使用復原伺服器上的少量記憶體來最佳化儲存作業數目。 此圖形會摘要說明復原伺服器上的記憶體使用量。 所顯示的記憶體負荷是複寫所使用的記憶體相較於 Hyper-V 伺服器上已安裝的記憶體總數的百分比。

![次要結果](./media/hyper-v-vmm-performance-results/IC744916.png)

復原站台上的 I/O 作業量為主要站台上寫入作業數目的函數。 讓我們看看復原站台上的 I/O 作業總數與主要站台上的 I/O 作業和與寫入作業的總數的比較。 此圖顯示復原站台上的 IOPS 總數

* 大約是主要站台上寫入 IOPS 的 1.5 倍。
* 大約是主要站台上 IOPS 總數的 37%。

![次要結果](./media/hyper-v-vmm-performance-results/IC744917.png)

![次要結果](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>對網路使用率的影響

針對每秒 5 Gb 的現有頻寬，會在主要和復原節點 (已啟用壓縮) 之間使用平均每秒 275 Mb 的網路頻寬。

![結果網路使用率](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>對 VM 效能的影響

複寫對虛擬機器上執行的生產環境工作負載的影響是其中一個重要的考量。 如果主要站台已針對複寫適當地佈建，對工作負載應該不會有任何影響。 Hyper-V 複本的輕量型追蹤機制可確保虛擬機器中執行的工作負載在穩定狀態複寫期間不會受到影響。 下圖將就此說明。

此圖顯示啟用複寫之前和之後，執行不同工作負載的虛擬機器執行的 IOPS。 您可以觀察到兩者之間沒有差異。

![複本效果結果](./media/hyper-v-vmm-performance-results/IC744920.png)

下圖顯示啟用複寫之前和之後，執行不同工作負載的虛擬機器的輸送量。 您可以觀察到複寫沒有顯著的影響。

![結果複本效果](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>結論

結果清楚地顯示，搭配 Hyper-V 複本的 Site Recovery 透過最小的負荷，就可以為大型叢集妥善調整。 Site Recovery 提供簡單的部署、複寫、管理和監視功能。 Hyper-V 複本提供成功調整複寫所需的基礎結構。 

## <a name="test-environment-details"></a>測試環境詳細資料

### <a name="primary-site"></a>主要站台

* 主要網站所具備的叢集內含五部執行 470 個虛擬機器的 Hyper-V 伺服器。
* VM 執行不同的工作負載，而且全都有啟用 Site Recovery 保護。
* 叢集節點的儲存體是由 iSCSI SAN 提供。 機型 – Hitachi HUS130。
* 每部叢集伺服器都有四張網路卡 (NIC)，每張網路卡各為 1 Gbps。
* 其中兩張網路卡會連線到 iSCSI 私人網路，另外兩張會連線到外部企業網路。 其中一個外部網路會保留為供叢集通訊專用。

![主要硬體需求](./media/hyper-v-vmm-performance-results/IC744922.png)

| 伺服器 | RAM | 模型 | 處理器 | 處理器數目 | NIC | 軟體 |
| --- | --- | --- | --- | --- | --- | --- |
| 叢集中的 Hyper-V 伺服器： <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 具有256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V 角色 |
| VMM 伺服器 |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>次要站台

* 次要站台有六個節點的容錯移轉叢集。
* 叢集節點的儲存體是由 iSCSI SAN 提供。 機型 – Hitachi HUS130。

![主要硬體規格](./media/hyper-v-vmm-performance-results/IC744923.png)

| 伺服器 | RAM | 模型 | 處理器 | 處理器數目 | NIC | 軟體 |
| --- | --- | --- | --- | --- | --- | --- |
| 叢集中的 Hyper-V 伺服器： <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V 角色 |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V 角色 |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V 角色 |
| VMM 伺服器 |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>伺服器工作負載

* 基於測試目的，我們挑選了常用於企業客戶案例中的工作負載。
* 我們使用 [IOMeter](http://www.iometer.org) 搭配資料表中摘要說明的工作負載特性進行模擬。
* 所有 IOMeter 設定檔都設為寫入隨機位元組，以模擬最壞情況的工作負載寫入模式。

| 工作負載 | I/O 大小 (KB) | 存取百分比 | 讀取百分比 | 未完成的 I/O | I/O 模式 |
| --- | --- | --- | --- | --- | --- |
| 檔案伺服器 |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |全部 100% 隨機 |
| SQL Server (磁片區 1)<br />SQL Server (第2卷) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% 隨機<br />100% 的順序 |
| Exchange |32 |100% |67% |8 |100% 隨機 |
| 工作站/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |兩者都 100% 隨機 |
| Web 檔案伺服器 |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |全部 75% 隨機 |

### <a name="vm-configuration"></a>VM 設定

* 主要叢集上 470 個 VM。
* 所有 VM 都有 VHDX 磁碟。
* VM 執行資料表中摘要說明的工作負載。 全部都使用 VMM 範本建立。

| 工作負載 | VM 數 | RAM 下限 (GB) | RAM 上限 (GB) | 每個 VM 的邏輯磁碟大小 (GB) | IOPS 上限 |
| --- | --- | --- | --- | --- | --- |
| [SQL Server] |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| 檔案伺服器 |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| 網頁伺服器 |149 |.5 |1 |80 |6 |
| 總計 |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Site Recovery 設定

* Site Recovery 設有內部部署至內部部署的保護
* VMM 伺服器已設定四個雲端，其中包含 Hyper-V 叢集伺服器及其 VM。

| 主要 VMM 雲端 | 受保護的 VM | 複寫頻率 | 其他復原點 |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 分鐘 |None |
| PrimaryCloudRpo30s |47 |30 秒 |None |
| PrimaryCloudRpo30sArp1 |47 |30 秒 |1 |
| PrimaryCloudRpo5m |235 |5 分鐘 |None |

### <a name="performance-metrics"></a>效能度量

資料表摘要說明在部署中測量的效能度量和計數器。

| 度量 | 計數器 |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Available memory |\Memory\Available MBytes |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM read (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM write (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Write Operations/S |
| VM read throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM write throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## Next steps

[Set up replication](hyper-v-vmm-disaster-recovery.md)\Processor(_Total)\` Processor Timeation of Hyper-V VMs in VMM clouds to a secondary site with Azure Site Recovery | Microsoft Docs
description: This article provides information about performance testing for replication of Hyper-V VMs in VMM clouds to a secondary site using Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi

---
# Test results for Hyper-V replication to a secondary site


This article provides the results of performance testing when replicating Hyper-V VMs in System Center Virtual Machine Manager (VMM) clouds, to a secondary datacenter.

## Test goals

The goal of testing was to examine how Site Recovery performs during steady state replication.

- Steady state replication occurs when VMs have completed initial replication, and are synchronizing delta changes.
- It’s important to measure performance using steady state, because it’s the state in which most VMs remain, unless unexpected outages occur.
- The test deployment consisted of two on-premises sites, with a VMM server in each site. This test deployment is typical of a head office/branch office deployment, with head office acting as the primary site, and the branch office as the secondary or recovery site.

## What we did

Here's what we did in the test pass:

1. Created VMs using VMM templates.
2. Started VMs, and captured baseline performance metrics over 12 hours.
3. Created clouds on the primary and recovery VMM servers.
4. Configured replication in Site Recovery, including mapping between source and recovery clouds.
5. Enabled protection for VMs, and allowed them to complete initial replication.
6. Waited a couple of hours for system stabilization.
7. Captured performance metrics over 12 hours, where all VMs remained in an expected replication state for those 12 hours.
8. Measured the delta between the baseline performance metrics, and the replication performance metrics.


## Primary server performance

* Hyper-V Replica (used by Site Recovery) asynchronously tracks changes to a log file, with minimum storage overhead on the primary server.
* Hyper-V Replica utilizes self-maintained memory cache to minimize IOPS overhead for tracking. It stores writes to the VHDX in memory, and flushes them into the log file before the time that the log is sent to the recovery site. A disk flush also happens if the writes hit a predetermined limit.
* The graph below shows the steady state IOPS overhead for replication. We can see that the IOPS overhead due to replication is around 5%, which is quite low.

  ![Primary results](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V Replica uses memory on the primary server, to optimize disk performance. As shown in the following graph, memory overhead on all servers in the primary cluster is marginal. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Primary results](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V Replica has minimum CPU overhead. As shown in the graph, replication overhead is in the range of 2-3%.

![Primary results](./media/hyper-v-vmm-performance-results/IC744915.png)

## Secondary server performance

Hyper-V Replica uses a small amount of memory on the recovery server, to optimize the number of storage operations. The graph summarizes the memory usage on the recovery server. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744916.png)

The amount of I/O operations on the recovery site is a function of the number of write operations on the primary site. Let’s look at the total I/O operations on the recovery site in comparison with the total I/O operations and write operations on the primary site. The graphs show that the total IOPS on the recovery site is

* Around 1.5 times the write IOPS on the primary.
* Around 37% of the total IOPS on the primary site.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744917.png)

![Secondary results](./media/hyper-v-vmm-performance-results/IC744918.png)

## Effect on network utilization

An average of 275 Mb per second of network bandwidth was used between the primary and recovery nodes (with compression enabled), against an existing bandwidth of 5 Gb per second.

![Results network utilization](./media/hyper-v-vmm-performance-results/IC744919.png)

## Effect on VM performance

An important consideration is the impact of replication on production workloads running on the virtual machines. If the primary site is adequately provisioned for replication, there shouldn’t be any impact on the workloads. Hyper-V Replica’s lightweight tracking mechanism ensures that workloads running in the virtual machines are not impacted during steady-state replication. This is illustrated in the following graphs.

This graph shows IOPS performed by virtual machines running different workloads, before and after replication was enabled. You can observe that there is no difference between the two.

![Replica effect results](./media/hyper-v-vmm-performance-results/IC744920.png)

The following graph shows the throughput of virtual machines running different workloads, before and after replication was enabled. You can observe that replication has no significant impact.

![Results replica effects](./media/hyper-v-vmm-performance-results/IC744921.png)

## Conclusion

The results clearly show that Site Recovery, coupled with Hyper-V Replica, scales well with minimum overhead for a large cluster. Site Recovery provides simple deployment, replication, management and monitoring. Hyper-V Replica provides the necessary infrastructure for successful replication scaling. 

## Test environment details

### Primary site

* The primary site has a cluster containing five Hyper-V servers, running 470 virtual machines.
* The VMs run different workloads, and all have Site Recovery protection enabled.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.
* Each cluster server has four network cards (NICs) of one Gbps each.
* Two of the network cards are connected to an iSCSI private network, and two are connected to an external enterprise network. One of the external networks is reserved for cluster communications only.

![Primary hardware requirements](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 has 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Secondary site

* The secondary site has a six-node failover cluster.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.

![Primary hardware specification](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Server workloads

* For test purposes we picked workloads commonly used in enterprise customer scenarios.
* We use [IOMeter](http://www.iometer.org) with the workload characteristic summarized in the table for simulation.
* All IOMeter profiles are set to write random bytes to simulate worst-case write patterns for workloads.

| Workload | I/O size (KB) | % Access | %Read | Outstanding I/Os | I/O pattern |
| --- | --- | --- | --- | --- | --- |
| File Server |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |All 100% random |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% random<br />100% sequential |
| Exchange |32 |100% |67% |8 |100% random |
| Workstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Both 100% random |
| Web File Server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |All 75% random |

### VM configuration

* 470 VMs on the primary cluster.
* All VMs with VHDX disk.
* VMs running workloads summarized in the table. All were created with VMM templates.

| Workload | # VMs | Minimum RAM (GB) | Maximum RAM (GB) | Logical disk size (GB) per VM | Maximum IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| File Server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Web server |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96.83 TB |4108 |

### Site Recovery settings

* Site Recovery was configured for on-premises to on-premises protection
* The VMM server has four clouds configured, containing the Hyper-V cluster servers and their VMs.

| Primary VMM cloud | Protected VMs | Replication frequency | Additional recovery points |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 mins |None |
| PrimaryCloudRpo30s |47 |30 secs |None |
| PrimaryCloudRpo30sArp1 |47 |30 secs |1 |
| PrimaryCloudRpo5m |235 |5 mins |None |

### Performance metrics

The table summarizes the performance metrics and counters that were measured in the deployment.

| Metric | Counter |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| 可用記憶體 |\記憶體\可用的 MB |
| IOPS |\PhysicalDisk(_Total)\每秒的磁碟傳輸數 |
| 每秒的 VM 讀取 (IOPS) 作業數 |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| 每秒的 VM 寫入 (IOPS) 作業數 |\Hyper-V 虛擬存放裝置 (\<VHD >) \Write 作業/秒 |
| VM 讀取輸送量 |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM 寫入輸送量 |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## <a name="next-steps"></a>後續步驟

[設定複寫](hyper-v-vmm-disaster-recovery.md)
