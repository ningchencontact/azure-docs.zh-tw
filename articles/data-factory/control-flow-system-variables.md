---
title: Azure Data Factory 中的系統變數 | Microsoft Docs
description: 本文描述 Azure Data Factory 支援的系統變數。 定義 Data Factory 實體時，可以在運算式中使用這些變數。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 77285692846d2a301e091426801e20203816181a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure Data Factory 支援的系統變數
本文描述 Azure Data Factory 支援的系統變數。 定義 Data Factory 實體時，可以在運算式中使用這些變數。 

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [Data Factory 第 1 版中的函式與變數](v1/data-factory-functions-variables.md)。


## <a name="pipeline-scope"></a>管線範圍：

| 變數名稱 | 說明 |
| --- | --- |
| @pipeline().DataFactory |管線執行在其中執行的資料處理站名稱 | 
| @pipeline().Pipeline |管線的名稱 |
| @pipeline().RunId | 特定管線執行的識別碼 | 
| @pipeline().TriggerType | 叫用管線的觸發程序類型 (手動、排程器) | 
| @pipeline().TriggerId| 叫用管線之觸發程序的識別碼 |
| @pipeline().TriggerName| 叫用管線之觸發程序的名稱 |
| @pipeline().TriggerTime| 叫用管線之觸發程序的時間。 觸發程序時間是實際的觸發時間，而非排定的時間。 例如，會傳回 `13:20:08.0149599Z` 而非 `13:20:00.00Z` |

## <a name="trigger-scope"></a>觸發程序範圍：

| 變數名稱 | 說明 |
| --- | --- |
| trigger().scheduledTime |排定觸發程序叫用管線執行的時間。 例如，對於每隔 5 分鐘就觸發的觸發程序，此變數分別會傳回 `2017-06-01T22:20:00Z`、`2017-06-01T22:25:00Z` 和 `2017-06-01T22:29:00Z`。|
| trigger().startTime |觸發程序「實際」觸發以叫用管線執行的時間。 例如，對於每隔 5 分鐘就觸發的觸發程序，此變數可能會傳回分別像是 `2017-06-01T22:20:00.4061448Z`、`2017-06-01T22:25:00.7958577Z` 和 `2017-06-01T22:29:00.9935483Z` 的項目。|

## <a name="next-steps"></a>後續步驟
如需如何在運算式中使用這些變數的詳細資訊，請參閱[運算式語言和函數](control-flow-expression-language-functions.md)。 
