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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "67080909"
---
| **Resource** | **預設限制** | **上限** |
| --- | --- | --- |
| 每個訂用帳戶每個區域 Azure Batch 帳戶 | 1-3 |50 |
| 每一 Batch 帳戶的專用核心 | 90-900 | 連絡支援人員 |
| 每一 Batch 帳戶的低優先順序核心 | 10-100 | 連絡支援人員 |
| 每一 Batch 帳戶的作用中作業和作業排程（**已完成** **[的工作沒有](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 限制） | 100-300 | 1,000<sup>1</sup> |
| 每個 Batch 帳戶集區數 | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> 預設限制會根據您用來建立 Batch 帳戶的訂用帳戶類型而有所不同。 所顯示的核心配額是針對 Batch 服務模式中的 Batch 帳戶。 [檢視您 Batch 帳戶中的配額](../articles/batch/batch-quota-limit.md#view-batch-quotas)。

<sup>1</sup>若要要求增加超過此限制，請聯絡 Azure 支援。
