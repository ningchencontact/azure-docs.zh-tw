---
title: Azure NetApp Files 的服務層級 | Microsoft Docs
description: 描述 Azure NetApp Files 之服務層級的輸送量效能。
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
ms.openlocfilehash: 1f9c427045c9d42f6a11cc4bcc798cfc47a4428c
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523113"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files 的服務等級
服務層級為容量集區的屬性。 服務層級定義和以磁碟區指派給磁碟區的配額為基礎的容量集區中允許的最大輸送量來區分。

## <a name="supported-service-levels"></a>支援的服務層級

Azure 的 NetApp 檔案服務支援三種服務層級：*強力*， *Premium*，以及*標準*。 

* <a name="Ultra"></a>強力的儲存體

    強力的儲存層提供最多 128 MiB/秒的輸送量每 1 TiB 的磁碟區指派的配額。 

* <a name="Premium"></a>進階儲存體

    進階儲存層提供最多 64 MiB/秒的輸送量每 1 TiB 的磁碟區指派的配額。 

* <a name="Standard"></a>標準儲存體

    標準儲存層提供最多 16 MiB/秒的輸送量每 1 TiB 的磁碟區指派的配額。

## <a name="throughput-limits"></a>輸送量限制

磁碟區的輸送量限制取決於下列因素的組合：
* 磁碟區所屬的容量集區服務層級
* 指派給磁碟區的配額  

下圖說明這個概念：

![服務層級圖](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

在上述範例 1，從已指派的配額 2 TiB 進階儲存層的容量集區的磁碟區會指派的輸送量限制為 128 MiB/秒 (2 TiB * 64 MiB/秒)。 此案例適用於無論容量集區的大小或實際的磁碟區耗用量。

在上面的範例 2，從已指派的配額 100 GiB 的進階儲存層的容量集區的磁碟區會指派 6.25 MiB/秒的輸送量限制 (0.09765625 TiB * 64 MiB/秒)。 此案例適用於無論容量集區的大小或實際的磁碟區耗用量。

## <a name="next-steps"></a>後續步驟

- 如需不同服務層級的價格，請參閱 [Azure NetApp Files 定價頁面](https://azure.microsoft.com/pricing/details/storage/netapp/)
- 請參閱[適用於 Azure NetApp 檔案的成本模型](azure-netapp-files-cost-model.md)容量集區中的容量耗用量的計算 
- [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)