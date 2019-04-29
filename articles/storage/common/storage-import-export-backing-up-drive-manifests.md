---
title: 備份 Azure 匯入/匯出磁碟機資訊清單 | Microsoft Docs
description: 了解如何自動備份 Microsoft Azure 匯入/匯出服務的磁碟機資訊清單。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ea223ea3ccd113014ceabff34cc4d0174abb1ddf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483123"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>備份 Azure 匯入/匯出作業的磁碟機資訊清單

可以藉由在 [Put Job](/rest/api/storageimportexport/jobs) 或 [Update Job Properties](/rest/api/storageimportexport/jobs) REST API 作業中將 `BackupDriveManifest` 屬性設定為 `true`，將磁碟機資訊清單自動備份到 blob。 根據預設，不會備份磁碟機資訊清單。 磁碟機資訊清單備份會在與工作相關聯的儲存體帳戶內，在容器中儲存為區塊 Blob。 根據預設，容器名稱是 `waimportexport`，但是您可以在呼叫 `Put Job` 或 `Update Job Properties` 作業時，於 `DiagnosticsPath` 屬性中指定不同的名稱。 備份資訊清單 blob 會以下列格式命名︰`waies/jobname_driveid_timestamp_manifest.xml`。

 您可以藉由呼叫 [Get Job](/rest/api/storageimportexport/jobs) 作業來擷取工作的備份磁碟機資訊清單 URI。 會在每個磁碟機的 `ManifestUri` 屬性中傳回 Blob URI。

## <a name="next-steps"></a>後續步驟

* [使用匯入/匯出服務 REST API](storage-import-export-using-the-rest-api.md)
