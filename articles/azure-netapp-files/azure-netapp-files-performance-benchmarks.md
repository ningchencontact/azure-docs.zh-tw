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
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870878"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>適用於 Azure NetApp 檔案的效能基準測試

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
* 100 %64 kib 且循序讀取
* 100 %64 KiB 循序寫入
* 50 %64 KiB 循序讀取]、 [50 %64 KiB 循序寫入
* 8 KiB 隨機讀取的 50%、 50 %8 KiB 隨機寫入

## <a name="bandwidth"></a>頻寬

Azure 的 NetApp 檔案提供多個[服務層級](azure-netapp-files-service-levels.md)。 每個服務層級提供不同的每個 TiB 的佈建的容量 （磁碟區配額） 的頻寬量。 磁碟區的頻寬限制會根據服務層級，以及磁碟區的配額組合來佈建。 請注意，頻寬限制只能有一個因素決定將可實現的輸送量的實際數量。  

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

測試 Vm 和 Azure NetApp 檔案磁碟區之間的距離會對 I/O 效能的影響。  下表比較與兩組不同的 Vm 的延遲回應曲線的 IOPS。  一組 Vm 即將 Azure NetApp 檔案和其他集已進一步離開。  請注意，增加的延遲時間，進一步組 Vm 有達到指定層級的平行處理原則的 IOPS 數量的影響。  不論如何，讀取磁碟區可以超過 300,000 IOPS，如下所示： 

![延遲研究](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>總結

延遲的工作負載 （資料庫） 可以具有一毫秒回應時間。 交易效能可能會超過 300 個 IOPS 的單一磁碟區。

（適用於串流處理和映像） 的輸送量敏感應用程式可擁有 4.5GiB / 秒的輸送量。
