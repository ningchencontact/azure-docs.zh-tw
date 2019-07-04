---
title: 效能基準測試，適用於 Azure NetApp 檔案 |Microsoft Docs
description: 描述 Azure NetApp 檔案在磁碟區層級的效能基準測試結果。
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 14081daf1f45a84bc8ad19bf0239db1281d9e624
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449511"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>適用於 Azure NetApp Files 的效能基準測試

本文說明 Azure NetApp 檔案在磁碟區層級的效能基準測試結果。 

## <a name="sample-application-used-for-the-tests"></a>用於測試的範例應用程式

使用 Azure NetApp 檔案的範例應用程式執行效能測試。 應用程式具有下列特性： 

* 以 Linux 為基礎的應用程式專為雲端建置
* 可以以線性方式調整已新增的虛擬機器 (Vm) 來視需要增加計算能力
* 需要快速的 data lake 的協助工具
* 有時候是隨機和循序有時的 I/O 模式 
    * 隨機模式需要大量 I/O 的低延遲。 
    * 循序模式需要大量的頻寬。 

## <a name="about-the-workload-generator"></a>關於工作負載產生器

結果是來自於 Vdbench 摘要檔中。 [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html)是命令列公用程式會產生驗證儲存體效能的磁碟 I/O 工作負載。 使用的用戶端-伺服器組態是可擴充的。  它包含單一混合主版/用戶端和 14 的專用用戶端 Vm。

## <a name="about-the-tests"></a>了解測試

這些測試被設計來識別可能的範例應用程式的限制和回應時間的曲線數目達到的限制。  

執行下列測試： 

* 100 %8 KiB 隨機讀取
* 100 %8 KiB 隨機寫入
* 100 %64 KiB 循序讀取
* 100 %64 KiB 循序寫入
* 50 %64 KiB 循序讀取，50 %64 KiB 循序寫入
* 50 %8 KiB 隨機讀取，50 %8 KiB 隨機寫入

## <a name="bandwidth"></a>頻寬

Azure 的 NetApp 檔案提供多個[服務層級](azure-netapp-files-service-levels.md)。 每個服務層級提供不同的每個 TiB 的佈建的容量 （磁碟區配額） 的頻寬量。 磁碟區的頻寬限制會根據服務層級，以及磁碟區的配額組合來佈建。 頻寬限制是輸送量的只有一個因素決定將可實現的實際數量。  

4,500 MiB 目前已完成的工作負載測試中的單一磁碟區的最高輸送量。  使用 Premium 服務層級，70.31 TiB 的磁碟區配額會佈建足夠的頻寬可以實現下列計算此輸送量： 

![頻寬公式](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![配額和服務層級](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>輸送量密集的工作負載

輸送量測試使用 Vdbench 和 12xD32s V3 儲存體 Vm 的組合。 在測試中的範例磁碟區來達成下列的輸送量數字：

![輸送量測試](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>我 i/o 密集型工作負載

I/O 測試使用 Vdbench 和 12xD32s V3 儲存體 Vm 的組合。 範例中的磁碟區測試達到以下的 I/O 數字：

![I/O 測試](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latency

測試 Vm 和 Azure NetApp 檔案磁碟區之間的距離會對 I/O 效能的影響。  下表比較與兩組不同的 Vm 的延遲回應曲線的 IOPS。  一組 Vm 即將 Azure NetApp 檔案和其他集已進一步離開。  增加的延遲時間，進一步組 Vm 所需的 IOPS 達到平行處理原則的指定層級會影響。  不論如何，讀取磁碟區可以超過 300,000 IOPS，如下所示： 

![延遲研究](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>總結

延遲的工作負載 （資料庫） 可以有一位數毫秒回應時間。 交易效能可能會超過 300 個 IOPS 的單一磁碟區。

（適用於串流處理和映像） 的輸送量敏感應用程式可擁有 4.5GiB / 秒的輸送量。

## <a name="example-scripts"></a>範例指令碼

下列指令碼範例是僅用於示範目的。  也就是不是要用於生產環境中使用。  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
