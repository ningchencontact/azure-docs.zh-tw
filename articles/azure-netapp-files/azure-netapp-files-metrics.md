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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 1563b60ef26ac5e4d40f45095d0109dd9dd71570
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672571"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files 的計量

Azure NetApp Files 提供有關已配置的儲存體、實際儲存體使用量、磁碟區輸送量、IOPS 及延遲的計量。 藉由分析這些計量，您可以更了解 NetApp 帳戶的使用方式模式與磁碟區效能。  

## <a name="capacity_pools"></a>容量集區的使用計量

- *磁碟區集區配置大小*  
    這是佈建的容量集區的大小 (GiB)。  
- *已使用的磁碟區集區配置*  
    這是指定容量集區中的磁碟區配額總計 (GiB) (即容量集區中磁碟區的的佈建大小總計)。 這是您在建立磁碟區時選取的大小。  
- *磁碟區集區邏輯大小總計*  
    這是容量集區中磁碟區之間所使用的邏輯空間 (GiB) 總計。  
- *磁碟區集區快照集大小總計*  
    這是快照集所使用的累加邏輯空間總計。  

## <a name="volumes"></a>磁碟區的使用計量

- *磁碟區配置大小*   
    這是 GiB 中配置的磁碟區大小 (配額)。  
- *磁碟區邏輯大小*   
    這是磁碟區中使用的邏輯空間 (GiB) 總計。 此大小包含作用中的檔案系統和快照集所使用的邏輯空間。  
- *磁碟區快照集大小*   
    這是磁碟區中快照集所使用的累加邏輯空間。  

## <a name="next-steps"></a>後續步驟

* [了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
* [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
* [建立適用於 Azure NetApp Files 的磁碟區](azure-netapp-files-create-volumes.md)
