---
title: 適用於 Azure NetApp 檔案的效能考量 |Microsoft Docs
description: 描述 Azure NetApp 檔案的效能考量。
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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454135"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>適用於 Azure NetApp Files 的效能考量

[輸送量限制](azure-netapp-files-service-levels.md)的磁碟區取決於指派給磁碟區的配額和層級所選服務的組合。 當您進行 Azure NetApp 檔案相關的效能計劃時，您需要了解幾個考量。 

## <a name="quota-and-throughput"></a>配額和輸送量  

輸送量限制為只有一個可以實現的實際效能的行列式。  

典型的儲存體效能考量，包括讀取和寫入混合時，傳輸大小、 隨機或循序模式，以及許多其他因素會影響傳遞的總效能。  

在測試中觀察到的最大經驗輸送量是 4,500 MiB/秒。  在進階儲存體層，70.31 TiB 的磁碟區配額會佈建的輸送量限制夠高，以達到此層級的效能。  

如果您考慮指派配額數量超過 70.31 TiB 的磁碟區，其他的配額可能會指派到磁碟區來儲存額外的資料。 不過，已新增的配額不會有進一步的增加，實際的輸送量。  

請參閱[效能基準測試 Azure NetApp 檔案](azure-netapp-files-performance-benchmarks.md)如需詳細資訊。

## <a name="overprovisioning-the-volume-quota"></a>過度佈建的磁碟區配額

如果工作負載的效能是繫結的輸送量限制，就可以過度佈建磁碟區配額設定較高的輸送量層級，並達成更高的效能。  

例如，如果進階儲存層中的磁碟區只 500 GiB 資料，但需要 128 MiB/秒的輸送量，您可以設定配額為 2 TiB，因此會據以設定的輸送量層級 (64 MiB/秒每 TB * 2 TiB = 128 MiB/秒)。  

如果您以一致的方式過度的磁碟區可達到更高的輸送量佈建，請考慮改為使用較高的服務層級。  在上述範例中，您可以達到相同的輸送量限制，有一半的磁碟區配額改為使用強力的儲存層 (128 MiB/秒每 TiB * 1 TiB = 128 MiB/秒)。

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>以動態方式增加或減少量配額

如果您的效能需求是暫時性質，或如果您有更固定的一段時間的效能需求，則您可以動態增加或減少磁碟區的配額，以立即調整輸送量限制。  請注意下列考量： 

* 可以增加或減少而暫停 IO，不需要的磁碟區的配額，並存取磁碟區不會中斷，或不是受影響。  

    在作用中的磁碟區的 I/O 交易期間，您可以調整配額。  請注意該磁碟區的配額，您可以永遠不會減少下方的邏輯磁碟區中儲存的資料量。

* 變更磁碟區的配額時，變更對應的輸送量限制是近乎即時的。 

    變更不會中斷或影響的磁碟區的存取或 I/O。  

* 調整磁碟區配額需要容量集區大小的變更。  

    您可以調整容量集區大小，以動態方式並不會影響磁碟區可用性或 I/O。

## <a name="next-steps"></a>後續步驟

- [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
- [適用於 Azure NetApp 檔案的效能基準測試](azure-netapp-files-performance-benchmarks.md)