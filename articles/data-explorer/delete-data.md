---
title: 從 Azure 資料總管刪除資料
description: 此文章說明 Azure 資料總管中的大量刪除案例，包括清除和以保留期為基礎的刪除。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9c1b21e119a38c6d306b9c564ab7958ba21a1c41
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049127"
---
# <a name="delete-data-from-azure-data-explorer"></a>從 Azure 資料總管刪除資料

在此文章中，我們將討論 Azure 資料總管支援的數種大量刪除方法。 它不支援即時刪除每筆記錄，因為它針對快速讀取存取進行了最佳化。

* 如果不再需要一或多個資料表，則可使用 drop table 或 drop tables 命令來刪除它們。

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* 如果不再需要舊資料，可透過變更資料庫或資料表層級的保留期限來刪除它。

    假設有一個保留期設為 90 天的資料庫或資料表。 由於商務需求發生變更，現在只需要 60 天的資料。 在此情況下，以下列其中一種方式刪除較舊的資料。

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    如需詳細資訊，請參閱[保留原則](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy) \(英文\)。

若您需要協助來解決資料刪除問題，請在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)中開立支援要求。
