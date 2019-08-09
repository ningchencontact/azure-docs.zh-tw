---
title: 適用於 Azure NetApp Files 的計量 | Microsoft Docs
description: 說明 Azure NetApp Files 的計量。
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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 7cf382f511d2ba8452d77bf207f36b749cb31e94
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848796"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files 的計量

Azure NetApp Files 提供有關已配置的儲存體、實際儲存體使用量、磁碟區輸送量、IOPS 及延遲的計量。 藉由分析這些計量，您可以更了解 NetApp 帳戶的使用方式模式與磁碟區效能。  

## <a name="capacity_pools"></a>容量集區的使用計量

<!-- 
- *Volume pool allocated size*  
    The size (GiB) of the provisioned capacity pool  
--> 
- *已使用的磁碟區集區配置*  
    指定容量集區中的磁片區配額 (GiB) 總數 (也就是容量集區中磁片區的布建大小總計)  
    這是您在建立磁碟區時選取的大小。  
- *磁碟區集區邏輯大小總計*  
    在容量集區中的磁片區上使用的邏輯空間 (GiB) 總數  
<!-- 
- *Volume pool total snapshot size*  
    The total of incremental logical space used by the snapshots  
-->

## <a name="volumes"></a>磁碟區的使用計量

<!-- 
- *Volume allocated size*   
    The volume size (quota) provisioned in GiB  
--> 
- *磁碟區邏輯大小*   
    磁片區中使用的總邏輯空間 (GiB)  
    此大小包含作用中的檔案系統和快照集所使用的邏輯空間。  
- *磁碟區快照集大小*   
   磁片區中的快照集所使用的增量邏輯空間  

## <a name="performance-metrics-for-volumes"></a>磁片區的效能計量

- *AverageReadLatency*   
    從磁片區讀取的平均時間 (以毫秒為單位)
- *AverageWriteLatency*   
    從磁片區寫入的平均時間 (以毫秒為單位)
- *ReadIops*   
    每秒磁片區的讀取次數
- *WriteIops*   
    每秒磁片區寫入的次數

## <a name="next-steps"></a>後續步驟

* [了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
* [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
* [建立適用於 Azure NetApp Files 的磁碟區](azure-netapp-files-create-volumes.md)
