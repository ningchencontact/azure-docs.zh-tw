---
title: Azure NetApp Files 的資源限制 | Microsoft Docs
description: 說明 Azure NetApp Files 資源的限制，包括 NetApp 帳戶、容量集區、磁碟區、快照集和委派子網路的限制。
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
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: d55e11ace4ca306c3d3ec8c0094a751966289db6
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523056"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files 的資源限制

了解 Azure NetApp Files 的資源限制有助於管理磁碟區。

## <a name="resource-limits"></a>資源限制

下表描述 Azure NetApp 檔案資源限制：

|  資源  |  預設限制  |  透過支援要求的可調整  |
|----------------|---------------------|--------------------------------------|
|  每個 Azure 訂用帳戶的 NetApp 帳戶數目   |  10    |  有   |
|  每個 NetApp 帳戶的容量集區數目   |    25     |   有   |
|  每個容量集區的磁碟區數目     |    500   |    有     |
|  每個磁碟區的快照集數目       |    255     |    無        |
|  委派給 Azure NetApp 檔案 (Microsoft.NetApp/volumes) 每個 Azure 虛擬網路的子網路數目    |   1   |    無    |
|  單一的容量集區大小下限   |  4 TiB     |    無  |
|  單一的容量集區大小上限    |  500 TiB   |   無   |
|  單一磁碟區的大小下限    |    100 GiB    |    無    |
|  最大值，指派配額的單一磁碟區 *   |   92 TiB   |    無   |
|  最大值的單一磁碟區 *     |    100 TiB    |    無       |

* 磁碟區可以是手動建立或調整大小，以最大幅度 92 TiB。 不過，磁碟區可以成長的超額部分的案例中最多 100 個 TiB。 請參閱[適用於 Azure NetApp 檔案的成本模型](azure-netapp-files-cost-model.md)如需有關容量超額部分。 

## <a name="request-limit-increase"></a>要求增加限制 

您可以建立 Azure 支援要求，以增加從上述資料表可調整的限制。 

從 Azure 入口網站的瀏覽平面： 

1. 按一下 **說明 + 支援**。
2. 按一下  **+ 新增支援要求**。
3. 在 [基本] 索引標籤中，提供下列資訊： 
    1. 問題類型：選取 **服務和訂用帳戶的限制 （配額）**。
    2. 訂用帳戶:選取的資源，您需要增加配額的訂用帳戶。
    3. 配額類型：選取**儲存體：Azure 的 NetApp 檔案限制**。
    4. 按一下 **下一步解决方法**。
4. 在詳細資料 索引標籤：
    1. 在 [描述] 方塊中，提供對應的資源類型的下列資訊：

        |  資源  |    父代資源      |    要求新的限制     |    增加配額的原因       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  帳戶 |  *訂用帳戶識別碼*   |  *要求新的最大**帳戶**數目*    |  *哪些案例或使用案例會提示要求？*  |
        |  集區    |  *訂用帳戶識別碼、 帳戶 URI*  |  *要求新的最大**集區**數目*   |  *哪些案例或使用案例會提示要求？*  |
        |  磁碟區  |  *訂用帳戶 ID，帳戶的 URI，集區的 URI*   |  *要求新的最大**磁碟區**數目*     |  *哪些案例或使用案例會提示要求？*  |

    2. 指定適當的支援方法，並提供您的合約資訊。

    3. 按一下 **下一步檢閱 + 建立**建立要求。 


## <a name="next-steps"></a>後續步驟  

- [了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
- [適用於 Azure NetApp 檔案的成本的模型](azure-netapp-files-cost-model.md)
