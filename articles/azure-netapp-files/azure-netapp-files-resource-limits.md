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
ms.topic: concepts
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 196d85917e0a9900e141d58bff171beeb8540409
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430008"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files 的資源限制

了解 Azure NetApp Files 的資源限制有助於管理磁碟區。

- 每個 Azure 訂用帳戶可以擁有最多 10 個 NetApp 帳戶。
- 每個 NetApp 帳戶最多可以有 25 個容量集區。
- 每個容量集區都只能屬於一個 NetApp 帳戶。  
- 單一容量集區的大小下限為 4 TiB，大小上限為 500 TiB。 
- 每個容量集區最多可以有 500 個磁碟區。
- 單一磁碟區的大小下限為 100 GiB，大小上限為 92 TiB。
- 每個磁碟區最多可以有 255 個快照集。
- 每個 Azure 虛擬網路 (Vnet) 只有一個子網路可委派給 Azure NetApp Files。

**後續步驟**

[了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
