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
ms.topic: concepts
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: a10ac319fe362011531e00f832bb8e471fd56fdf
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56431072"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files 的服務層級
Azure NetApp Files 支援兩個服務層級：「進階」和「標準」。 

## <a name="Premium"></a>進階儲存體

「進階」儲存體能提供最多每 TiB 64 MiB/秒的輸送量。 輸送量效能是針對磁碟區配額編製索引。 例如，來自進階儲存體並具有 2 TiB 已佈建配額 (無論實際耗用量為何) 的磁碟區，將具有 128 MiB/秒的輸送量。

## <a name="Standard"></a>標準儲存體

「標準」儲存體能提供最多每 TiB 16 MiB/秒的輸送量。 輸送量效能是針對磁碟區配額編製索引。 例如，來自標準儲存體並具有 2 TiB 已佈建配額 (無論實際耗用量為何) 的磁碟區，將具有 32 MiB/秒的輸送量。

## <a name="next-steps"></a>後續步驟

- 如需不同服務層級的價格，請參閱 [Azure NetApp Files 定價頁面](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
