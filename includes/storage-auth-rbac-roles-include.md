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
ms.openlocfilehash: 9b8418dba12748915666c6a91ee65b37c0f59ace
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807471"
---
Azure 提供以下内置的 RBAC 角色，用于访问存储数据：

- [儲存體 Blob 資料擁有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner):用來設定擁有權和管理 Azure Data Lake Storage Gen2 的 POSIX 存取控制 (預覽)。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [儲存體 Blob 資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor):用來授與 Blob 儲存體資源的讀取/寫入/刪除權限。
- [儲存體 Blob 資料讀者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader):用來授與 Blob 儲存體資源的唯讀權限。
- [儲存體佇列資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor):用來授與 Azure 佇列的讀取/寫入/刪除權限。
- [儲存體佇列資料讀者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader):用來授與 Azure 佇列的唯讀權限。
- [儲存體佇列資料訊息處理器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor):用來授與查看、 擷取、 及 Azure 儲存體佇列中訊息的刪除權限。
- [儲存體佇列資料訊息寄件者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender):用來授與權限加入 Azure 儲存體佇列中的訊息。

> [!IMPORTANT]
> RBAC 角色指派可能需要五分鐘的時間傳播。

如需有關如何為 Azure 儲存體定義內建角色的詳細資訊，請參閱[了解角色定義](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview)。 若要了解如何创建自定义 RBAC 角色，请参阅[针对 Azure 基于角色的访问控制创建自定义角色](../articles/role-based-access-control/custom-roles.md)。 
