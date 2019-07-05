---
title: 基準測試磁碟區的效能和使用 Azure NetApp 檔案的度量 |Microsoft Docs
description: 提供效能評定測試的建議磁碟區的效能和使用 Azure NetApp 檔案的度量。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: b-juche
ms.openlocfilehash: 12ae9e313655924f11799152b5e58b77776c135c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478806"
---
# <a name="benchmark-testing-for-volume-performance-and-metrics-using-azure-netapp-files"></a>適用於使用 Azure NetApp Files 的磁碟區效能和計量基準測試

本文章提供基準測試磁碟區的效能和使用 Azure NetApp 檔案的度量的建議。

## <a name="overview"></a>概觀

若要了解 Azure NetApp 檔案磁碟區的效能特性，您可以使用開放原始碼工具[FIO](https://github.com/axboe/fio)執行一系列基準測試來模擬各種不同的工作負載。 FIO 可以安裝在這兩個 Linux 和 Windows 架構的作業系統。  它是絕佳的工具，以取得快速的快照集的 IOPS 和輸送量的磁碟區。

### <a name="vm-instance-sizing"></a>VM 執行個體大小

為了獲得最佳結果，請確定您使用的會適當地調整大小以執行測試的虛擬機器 (VM) 執行個體。 下列範例會使用 Standard_D32s_v3 執行個體。 如需有關 VM 執行個體大小的詳細資訊，請參閱[大小的 Windows Azure 中的虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)適用於以 Windows 為基礎的 Vm，並[在 Azure 中 Linux 虛擬機器的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)適用於以 Linux 為基礎的 Vm。

### <a name="azure-netapp-files-volume-sizing"></a>Azure 的 NetApp 檔案磁碟區大小

請確定您選擇的預期的效能層級的正確服務層級和磁碟區的配額大小。 請參閱[服務層級 Azure NetApp 檔案](azure-netapp-files-service-levels.md)如需詳細資訊。

### <a name="virtual-network-vnet-recommendations"></a>虛擬網路 (VNet) 的建議

您應該執行基準測試與 Azure NetApp 檔案相同的 VNet 中的測試。 下列範例會示範建議：

![VNet 的建議](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>FIO 的安裝

FIO 是 Linux 和 Windows 的二進位格式提供。 請依照下列中的二進位套件一節[FIO](https://github.com/axboe/fio)安裝適用於您選擇的平台。

## <a name="fio-examples-for-iops"></a>IOPS 的 FIO 範例 

在本節中的 FIO 範例會使用下列設定：
* VM 執行個體大小：D32s_v3
* 容量集區服務層級和大小：進階 / 50 TiB
* 磁碟區配額大小：48 TiB

下列範例顯示 FIO 隨機讀取和寫入。

### <a name="fio-8k-block-size-100-random-reads"></a>FIO:8 k 的區塊大小 100%的隨機讀取

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>輸出：68 k 閱讀顯示的 IOPS

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO:8 k 的區塊大小 100%隨機寫入

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>輸出：73 k 寫入顯示的 IOPS

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>頻寬 FIO 範例

此區段顯示循序 FIO 中的範例會讀取和寫入。

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO:64 k 的區塊大小 100%循序讀取

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>輸出：11.8 顯示的 Gbit/s 輸送量

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO:64 k 的區塊大小 100%循序寫入

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>輸出：12.2 顯示的 Gbit/s 輸送量

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>磁碟區計量

Azure 的 NetApp 檔案效能資料會透過 Azure 監視器計數器。 計數器是可透過 Azure 入口網站和 REST API GET 要求。 

您可以檢視歷程記錄資料的下列資訊：
* 讀取延遲的平均值 
* 寫入延遲的平均值 
* （平均） 的讀取 IOPS
* 寫入 IOPS （平均）
* 磁碟區的邏輯大小 （平均）
* 磁碟區快照集大小 （平均）

### <a name="using-azure-monitor"></a>使用 Azure 監視器 

如下所示，您可以從 [計量] 頁面中，存取 Azure NetApp 檔案以每個磁碟區上的計數器：

![Azure 監視器計量](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

您也可以建立儀表板在 Azure 監視器中 Azure NetApp 檔案移至 [計量] 頁面、 NetApp、 篩選，並指定感興趣的磁碟區的計數器： 

![Azure 監視器儀表板](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure 監視器 API 存取

您可以使用 REST API 呼叫，以存取 Azure NetApp 檔案計數器。 請參閱[支援 Azure 監視器的度量：Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes)計數器的容量集區和磁碟區。

下列範例顯示取得的 URL 來檢視邏輯磁碟區大小：

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>後續步驟

- [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
- [適用於 Azure NetApp 檔案的效能基準測試](azure-netapp-files-performance-benchmarks.md)