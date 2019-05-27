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
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115568"
---
Azure 提供下列內建的 RBAC 角色來授權存取 blob 和佇列資料使用 Azure AD 和 OAuth:

- [儲存體 Blob 資料擁有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner):用來設定擁有權和管理 Azure Data Lake Storage Gen2 的 POSIX 存取控制 (預覽)。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [儲存體 Blob 資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor):用來授與 Blob 儲存體資源的讀取/寫入/刪除權限。
- [儲存體 Blob 資料讀者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader):用來授與 Blob 儲存體資源的唯讀權限。
- [儲存體佇列資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor):用來授與 Azure 佇列的讀取/寫入/刪除權限。
- [儲存體佇列資料讀者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader):用來授與 Azure 佇列的唯讀權限。
- [儲存體佇列資料訊息處理器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor):用來授與查看、 擷取、 及 Azure 儲存體佇列中訊息的刪除權限。
- [儲存體佇列資料訊息寄件者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender):用來授與權限加入 Azure 儲存體佇列中的訊息。

> [!NOTE]
> 請記住，RBAC 角色指派可能需要五分鐘的時間傳播。
