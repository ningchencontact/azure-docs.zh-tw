---
title: 在適用於 MariaDB 的 Azure 資料庫中監視
description: 本文說明適用於 MariaDB 的 Azure 資料庫之監視和警示的計量，包括 CPU、儲存體和連線統計資料。
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/10/2018
ms.openlocfilehash: f34e32b9bda83ac1185d0c7b7dcaaaa7b47161a1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082478"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>在適用於 MariaDB 的 Azure 資料庫中監視
監視伺服器的相關資料，可協助您疑難排解並最佳化您的工作負載。 適用於 MariaDB 的 Azure 資料庫提供多種計量，可讓您深入了解您伺服器的行為。

## <a name="metrics"></a>度量
所有 Azure 計量都有一分鐘頻率，且每個計量皆提供 30 天的記錄。 您可以在計量上設定警示。 其他工作包含設定自動化動作、執行進階分析，以及封存記錄。 如需詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

如需逐步指引，請參閱[如何設定警示](howto-alert-metric.md)。

### <a name="list-of-metrics"></a>計量清單
這些計量可供適用於 MariaDB 的 Azure 資料庫使用：

|計量|計量顯示名稱|單位|描述|
|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用中的 CPU 百分比。|
|memory_percent|記憶體百分比|百分比|使用中記憶體的百分比。|
|io_consumption_percent|IO 百分比|百分比|使用中 IO 的百分比。|
|storage_percent|儲存體百分比|百分比|使用的儲存體佔伺服器最大值的百分比。|
|storage_used|已使用儲存體|位元組|使用中的儲存體數量。 此服務所使用的儲存體可能包括資料庫檔案、交易記錄和伺服器記錄。|
|serverlog_storage_percent|伺服器記錄儲存體百分比|百分比|使用的伺服器記錄儲存體佔伺服器記錄儲存體上限的百分比。|
|serverlog_storage_usage|使用的伺服器記錄儲存體|位元組|使用中的伺服器記錄儲存體數量。|
|serverlog_storage_limit|伺服器記錄儲存體限制|位元組|此伺服器的伺服器記錄儲存體上限。|
|storage_limit|儲存體限制|位元組|此伺服器的儲存體上限。|
|active_connections|作用中的連線|計數|伺服器的使用中連線數量。|
|connections_failed|失敗的連線|計數|伺服器的失敗連線數量。|
|network_bytes_egress|Network Out|位元組|跨作用中連線的網路輸出。|
|network_bytes_ingress|Network In|位元組|跨作用中連線的網路輸入。|

## <a name="server-logs"></a>伺服器記錄
您可以在伺服器上啟用慢速查詢記錄。 若要深入了解記錄，請造訪[伺服器記錄](concepts-server-logs.md)頁面。

## <a name="next-steps"></a>後續步驟
- 如需如何使用 Azure 入口網站、REST API 或 CLI 存取及匯出計量的詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。
  - 請參閱[如何設定警示](howto-alert-metric.md)，取得根據計量來建立警示的指引。
