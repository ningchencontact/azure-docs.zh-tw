---
title: 包含檔案
description: 包含檔案
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: fe6c6d461fd6b99ce2ce57ebdd61a3f3e961489c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174142"
---
## <a name="storage"></a>儲存體

|  |  |
|---------|---------|
| [允許的儲存體帳戶和虛擬機器 SKU](../articles/governance/policy/samples/allowed-skus-storage.md) | 要求儲存體帳戶和虛擬機器使用已核准的 SKU。 使用內建的原則來確保使用已核准的 SKU。 您需指定已核准的虛擬機器 SKU 陣列和已核准的儲存體帳戶 SKU 陣列。 |
| [允許的儲存體帳戶 SKU](../articles/governance/policy/samples/allowed-storage-account-skus.md) | 要求儲存體帳戶使用已核准的 SKU。 您需指定已核准的 SKU 陣列。 |
| [針對儲存體帳戶拒絕使用非經常性存取層處理](../articles/governance/policy/samples/deny-cool-access-tiering.md) | 針對 Blob 儲存體帳戶禁止使用非經常性存取層處理。  |
| [針對儲存體帳戶確保只允許 HTTPS 流量](../articles/governance/policy/samples/ensure-https-storage-account.md) | 要求儲存體帳戶使用 HTTPS 流量。  |
| [確保儲存體檔案加密](../articles/governance/policy/samples/ensure-storage-file-encryption.md) | 要求針對儲存體帳戶啟用檔案加密。  |
| [要求儲存體帳戶加密](../articles/governance/policy/samples/require-storage-account-encryption.md) | 要求儲存體帳戶使用 Blob 加密。  |