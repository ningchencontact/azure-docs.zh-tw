---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4f9a828e5233c88db2106bc648c07578927e0d29
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68514875"
---
Azure 提供下列內建 RBAC 角色, 以使用 Azure AD 和 OAuth 來授權 blob 和佇列資料的存取:

- [儲存體 Blob 資料擁有](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)者:用於設定 Azure Data Lake Storage Gen2 的擁有權和管理 POSIX 存取控制。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [儲存體 Blob 資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor):用來授與 Blob 儲存體資源的讀取/寫入/刪除權限。
- [儲存體 Blob 資料讀取器](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader):用來授與 Blob 儲存體資源的唯讀權限。
- [儲存體佇列資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor):用來授與 Azure 佇列的讀取/寫入/刪除權限。
- [儲存體佇列資料讀取器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader):用來授與 Azure 佇列的唯讀權限。
- [儲存體佇列資料訊息處理器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor):用來授與 Azure 儲存體佇列中訊息的查看、抓取和刪除許可權。
- [儲存體佇列資料訊息寄件者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender):用來將新增許可權授與 Azure 儲存體佇列中的訊息。

> [!NOTE]
> 請記住, RBAC 角色指派最多可能需要五分鐘的時間來傳播。
