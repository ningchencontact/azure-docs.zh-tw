---
title: Azure PowerShell - 訂閱 Blob 儲存體帳戶
description: Azure 事件方格和 Azure PowerShell 指令碼範例 - 訂閱 Blob 儲存體帳戶
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: 1e394b27818456b96118f80f17cf459ac08f45a9
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790508"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>使用 PowerShell 訂閱 Blob 儲存體帳戶的事件

此指令碼可針對 Blob 儲存體帳戶的事件建立 Event Grid 訂用帳戶。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>範例指令碼 - Stable

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立事件訂用帳戶。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | 建立事件格線訂用帳戶。 |

## <a name="next-steps"></a>後續步驟

* 如需受控應用程式的簡介，請參閱 [Azure 受控應用程式概觀](../overview.md)。
* 如需 PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/azure/get-started-azureps)。
