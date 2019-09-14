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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 15d0a584d88045f6020162a88124cd9d6a4735bf
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984013"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files 的資源限制

了解 Azure NetApp Files 的資源限制有助於管理磁碟區。

## <a name="resource-limits"></a>資源限制

下表說明 Azure NetApp Files 的資源限制：

|  Resource  |  預設限制  |  可透過支援要求調整  |
|----------------|---------------------|--------------------------------------|
|  每個 Azure 訂用帳戶的 NetApp 帳戶數目   |  10    |  是   |
|  每個 NetApp 帳戶的容量集區數目   |    25     |   是   |
|  每個容量集區的磁片區數目     |    500   |    是     |
|  每個磁片區的快照集數目       |    255     |    否        |
|  每個 Azure 虛擬網路委派給 Azure NetApp Files （Microsoft NetApp/磁片區）的子網數目    |   1   |    否    |
|  可存取 Azure NetApp Files 的 VNet 中的 Ip 數目（包括對等互連 Vnet）   |    1000   |    是   |
|  單一容量集區的大小下限   |  4 TiB     |    否  |
|  單一容量集區的大小上限    |  500 TiB   |   否   |
|  單一磁片區的大小下限    |    100 GiB    |    否    |
|  單一磁片區的大小上限     |    100 TiB    |    否       |
|  每個磁片區的檔案數目上限（inode）     |    50000000    |    否    |    

## <a name="request-limit-increase"></a>增加要求限制 

您可以建立 Azure 支援要求，以增加上表中的可調整限制。 

從 Azure 入口網站導覽平面： 

1. 按一下 [說明 **+ 支援**]。
2. 按一下 [ **+ 新增支援要求**]。
3. 在 [基本] 索引標籤上，提供下列資訊： 
    1. 問題類型：選取 **[服務與訂用帳戶限制（配額）** ]。
    2. 訂戶選取您需要增加配額的資源訂用帳戶。
    3. 配額類型：選取**儲存體：Azure NetApp Files 限制**。
    4. 按一下 **下一步解決方案**。
4. 在 [詳細資料] 索引標籤上：
    1. 在 [描述] 方塊中，提供對應資源類型的下列資訊：

        |  Resource  |    父資源      |    要求的新限制     |    增加配額的原因       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  帳戶 |  *訂用帳戶識別碼*   |  *已要求新的**帳戶**號碼上限*    |  *哪種案例或使用案例會提示要求？*  |
        |  集區    |  *訂用帳戶識別碼、帳戶 URI*  |  *已要求新的**集**區數目上限*   |  *哪種案例或使用案例會提示要求？*  |
        |  磁碟區  |  *訂用帳戶識別碼、帳戶 URI、集區 URI*   |  *已要求新的**磁片**區數目上限*     |  *哪種案例或使用案例會提示要求？*  |

    2. 指定適當的支援方法，並提供您的合約資訊。

    3. 按一下 **下一步請參閱**  + 建立 來建立要求。 


## <a name="next-steps"></a>後續步驟  

- [了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files 的成本模型](azure-netapp-files-cost-model.md)
