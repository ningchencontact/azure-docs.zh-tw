---
title: Azure NetApp Files 的資源限制 | Microsoft Docs
description: 說明 Azure NetApp Files 資源的限制，包括容量集區、磁碟區和委派子網路的限制。
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
ms.topic: reference
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: f34afb1df2ae38353f29a80bfb6798c16856dbeb
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54056354"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files 的資源限制
了解 Azure NetApp Files 的資源限制有助於管理磁碟區。

## <a name="capacity_pools"></a>容量集區

- 單一容量集區的大小下限為 4 TiB，大小上限為 500 TiB。 
- 每個容量集區都只能屬於一個 NetApp 帳戶。 不過，您的 NetApp 帳戶內可以有多個容量集區。  

## <a name="volumes"></a>磁碟區

- 單一磁碟區的大小下限為 100 GiB，大小上限為 92 TiB。
- 您在每個區域的每個 Azure 訂用帳戶中最多可以有 100 個磁碟區。  

## <a name="delegated_subnet"></a>委派的子網路 

在每個 Azure 虛擬網路 (Vnet) 中，都只有一個子網路可委派給 Azure NetApp Files。

## <a name="next-steps"></a>後續步驟

[了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
