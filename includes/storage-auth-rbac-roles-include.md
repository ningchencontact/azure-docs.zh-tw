---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4c66f35537cff87eb53a6cfe9bc0389f038f1a10
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449764"
---
Azure 會提供下列內建的 RBAC 角色的儲存體資料的存取權：

- [儲存體 Blob 資料擁有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview):用來設定擁有權和管理 Azure Data Lake Storage Gen2 的 POSIX 存取控制 (預覽)。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [儲存體 Blob 資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview):用來授與 Blob 儲存體資源的讀取/寫入/刪除權限。
- [儲存體 Blob 資料讀者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview):用來授與 Blob 儲存體資源的唯讀權限。
- [儲存體佇列資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview):用來授與 Azure 佇列的讀取/寫入/刪除權限。
- [儲存體佇列資料讀者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview):用來授與 Azure 佇列的唯讀權限。
- [儲存體佇列資料訊息處理器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor-preview):用來授與查看、 擷取、 及 Azure 儲存體佇列中訊息的刪除權限。
- [儲存體佇列資料訊息寄件者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender-preview):用來授與權限加入 Azure 儲存體佇列中的訊息。

> [!IMPORTANT]
> RBAC 角色指派可能需要五分鐘的時間傳播。

如需有關如何為 Azure 儲存體定義內建角色的詳細資訊，請參閱[了解角色定義](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview)。 建立自訂的 RBAC 角色的詳細資訊，請參閱[建立 azure 角色型存取控制的自訂角色](../articles/role-based-access-control/custom-roles.md)。 
