---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9402c4b24c9d64b4b69d750fbd19de40cda396f3
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2019
ms.locfileid: "57218085"
---
Azure 會提供下列內建的 RBAC 角色的儲存體資料的存取權：

- [儲存體 Blob 資料擁有者 (預覽)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview)：用來設定擁有權和管理 Azure Data Lake Storage Gen2 的 POSIX 存取控制 (預覽)。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [儲存體 Blob 資料參與者 (預覽)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview)：用來授與 Blob 儲存體資源的讀取/寫入/刪除權限。
- [儲存體 Blob 資料讀者 (預覽)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview)：用來授與 Blob 儲存體資源的唯讀權限。
- [儲存體佇列資料參與者 (預覽)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview)：用來授與 Azure 佇列的讀取/寫入/刪除權限。
- [儲存體佇列資料讀者 (預覽)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview)：用來授與 Azure 佇列的唯讀權限。

如需有關如何為 Azure 儲存體定義內建角色的詳細資訊，請參閱[了解角色定義](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview)。 建立自訂的 RBAC 角色的詳細資訊，請參閱[建立 azure 角色型存取控制的自訂角色](../articles/role-based-access-control/custom-roles.md)。 
