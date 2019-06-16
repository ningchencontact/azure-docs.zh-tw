---
title: 包含檔案
description: 包含檔案
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080909"
---
| **Resource** | **預設限制** | **上限** |
| --- | --- | --- |
| 每個訂用帳戶區域的 azure Batch 帳戶 | 1-3 |50 |
| 每一 Batch 帳戶的專用核心 | 90-900 | 請連絡支援人員 |
| 每一 Batch 帳戶的低優先順序核心 | 10-100 | 請連絡支援人員 |
| **[Active](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 作業和作業排程，每個 Batch 帳戶 (**完成**作業具有無限制) | 100-300 | 1,000<sup>1</sup> |
| 每一 Batch 帳戶的集區 | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> 預設限制會根據您用來建立 Batch 帳戶的訂用帳戶類型而有所不同。 所顯示的核心配額是針對 Batch 服務模式中的 Batch 帳戶。 [檢視您 Batch 帳戶中的配額](../articles/batch/batch-quota-limit.md#view-batch-quotas)。

<sup>1</sup>若要要求增加到超過此限制，請連絡 Azure 支援服務。
