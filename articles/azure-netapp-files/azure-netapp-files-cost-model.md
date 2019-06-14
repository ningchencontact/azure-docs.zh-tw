---
title: 適用於 Azure NetApp 檔案的成本的模型 |Microsoft Docs
description: 描述 Azure NetApp 檔案，從服務管理支出的成本模型。
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
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: b06e3366224b90899dd3f9f9439edf897de82794
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65524216"
---
# <a name="cost-model-for-azure-netapp-files"></a>適用於 Azure NetApp Files 的成本模型 

了解 Azure NetApp 檔案的成本模型，可協助您從服務管理您的費用。

## <a name="calculation-of-capacity-consumption"></a>容量耗用量的計算

Azure 的 NetApp 檔案是在佈建的儲存體容量計費。  佈建的容量會藉由建立容量集區配置。  計費依據為 $; 佈建-GiB/每月的每小時增加容量的集區。 單一的容量集區的最小大小是 4 TiB，和 1 TiB 增加可以接著擴充容量集區。 建立磁碟區容量集區中。  每個磁碟區指派配額之遞減從集區佈建的容量。 可指派給磁碟區的範圍從最小值為 92 TiB 的最大值 100 GiB 的配額。  

對於作用中的磁碟區，配額容量耗用量取決於邏輯 （有效） 的容量。

如果磁碟區的實際容量耗用量超過其儲存配額，磁碟區可以繼續成長。 只要在實際的磁碟區大小未超過系統限制 (100 TiB)，仍會允許寫入。  

使用針對其佈建的數量的容量集區容量總計為更高的其中一個指派的配額或實際的總和的集區中的所有磁碟區的耗用量： 

   ![使用總空間計算](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

下圖說明這些概念。  
* 我們有 4 TiB 的佈建的容量的容量集區。  集區包含三個磁碟區。  
    * 磁碟區 1 會指派 2 TiB 的配額，並具有 800 GiB 的耗用量。  
    * 磁碟區 2 被指派 1 TiB 的配額，而且有 100 GiB 的耗用量。  
    * 磁碟區 3 已指派的配額為 500 GiB，但具有 800 GiB 的耗用量 （超額部分）。  
* 4 TiB 的容量 （佈建的金額） 的計量容量集區。  
    3.8 TiB 的容量會取用 （2 TiB 和 1 TiB 的配額從磁碟區 1 和 2，而 800 GiB 的磁碟區 3 的實際耗用量）。 和剩餘的容量的 200 GiB。

   ![具有三個磁碟區的容量集區](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>超額部分，容量耗用量  

當使用集區容量總計超過其佈建的容量時，仍允許寫入的資料。  寬限期之後 （1 小時），如果的集區的已使用的容量仍然會超過其佈建的容量，然後將集區大小就會自動增加遞增量為 1 TiB 之前佈建的容量大於的總計已使用容量。  比方說，在上圖中，如果磁碟區 3 持續成長，且實際的耗用量到達 1.2 TiB，然後在寬限期之後, 在集區會自動重新調整 5 TiB。  結果是已佈建的集區容量 (也就是 5 TiB) 超過使用的容量 (4.2 TiB)。  

## <a name="manual-changes-of-the-pool-size"></a>手動變更集區大小  

您可以手動增加或減少集區大小。 不過，適用下列限制：
* 服務的最小和最大限制  
    請參閱的文章[資源限制](azure-netapp-files-resource-limits.md)。
* 在初始 4 TiB 最低預付金額後增量為 1 TiB
* 一小時的最小計費增量
* 佈建的集區大小不可能會減少到小於集區中使用的容量總計。

## <a name="behavior-of-maximum-size-pool-overage"></a>最大大小的集區的超額部分的行為   

您可以建立或調整大小的容量集區的大小上限為 500 TiB。  當容量集區中使用的容量總計超過 500 TiB，會發生下列情況：
* （如果磁碟區低於 100 TiB 的系統最大值），仍會允許寫入的資料。
* 之後一小時的寬限期內，集區會自動調整大小 1 TiB 的增加，直到集區佈建容量超過使用的容量總計。
* 額外佈建，而計費超過 500 TiB 不能用來指派大量配額的集區容量。 也不能用來展開 效能 QoS 限制。  
    請參閱[服務層級](azure-netapp-files-service-levels.md)相關效能限制與 QoS 調整大小。

下圖說明這些概念：
* 我們必須使用進階儲存層和 500 TiB 容量的容量集區。 集區包含九個磁碟區。
    * 磁碟區 1 繨 8 指派 60 TiB 的配額。  使用的容量總計會是 480 TiB。  
        每個磁碟區的 QoS 上限為 3.75 GiB/秒的輸送量 (60 TiB * 64 MiB/秒)。  
    * 第 9 卷，就會被指派的配額為 20 的 TiB。  
        第 9 卷的 QoS 上限為 1.25 GiB/秒的輸送量 (60 TiB * 64 MiB/秒)。
* 第 9 卷是超額的案例。 它有 25 的 TiB 的實際用電量。  
    * 之後一小時的寬限期，容量集區將會重新調整 「 505 TiB。  
        也就是說，總計已使用容量 = 8 * 60-8，透過磁碟區 1 和 25 的 TiB 的磁碟區 9 的實際用電量的 TiB 配額。
    * 計費的容量會是 「 505 TiB。
    * （因為集區的總指派的配額可能不會超過 500 TiB），則無法增加磁碟區 9 的磁碟區配額。
    * （因為集區總數的 QoS 仍根據 500 TiB），可能不會指派額外的 QoS 輸送量。

   ![具有九個磁碟區的容量集區](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>快照集容量耗用量 

Azure NetApp 檔案中的快照集容量耗用量計費對父磁碟區的配額。  如此一來，它會共用相同的計費費率為磁碟區所屬的容量集區。  不過，不同於使用中的磁碟區，快照集耗用的測量依據增量耗用的容量。  Azure 的 NetApp 檔案快照集是在本質上的差異。 根據資料變更率，快照集通常會耗用較少比邏輯的使用中的磁碟區容量的容量。 例如，假設您有 500 GiB 的磁碟區只能包含 10 GiB 差異資料的快照集。 收取該快照的磁碟區配額的容量會是 10 GiB，而不是 500 GiB。 

## <a name="next-steps"></a>後續步驟

* [Azure 定價頁面的 NetApp 檔案](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
