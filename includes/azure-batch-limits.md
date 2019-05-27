---
title: 包含檔案
description: 包含檔案
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 9ffb02fce41e8805dfccf1dfd6e982cf107039ec
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127553"
---
| **Resource** | **預設限制** | **上限** |
| --- | --- | --- |
| 每個訂用帳戶區域的 azure Batch 帳戶 | 1-3 |50 |
| 每一 Batch 帳戶的專用核心 | 10-100 | N/A<sup>1</sup> |
| 每一 Batch 帳戶的低優先順序核心 | 10-100 | N/A<sup>2</sup> |
| 每一 Batch 帳戶的作用中作業和作業排程<sup>3</sup> | 100-300 | 1,000<sup>4</sup> |
| 每個 Batch 帳戶集區數 | 20-100 | 500<sup>4</sup> |

> [!NOTE]
> 預設限制會根據您用來建立 Batch 帳戶的訂用帳戶類型而有所不同。 所顯示的核心配額是針對 Batch 服務模式中的 Batch 帳戶。 [檢視您 Batch 帳戶中的配額](../articles/batch/batch-quota-limit.md#view-batch-quotas)。 

<sup>1</sup>可以增加每一 Batch 帳戶專用核心數目，但未指定最大數目。 討論增加選項，請連絡 Azure 支援人員。

<sup>2</sup>可以增加每一 Batch 帳戶的低優先順序核心數目，但未指定最大數目。 討論增加選項，請連絡 Azure 支援人員。

<sup>3</sup>完成的作業和作業排程不受限制。

<sup>4</sup>若要要求增加到超過此限制，請連絡 Azure 支援服務。
