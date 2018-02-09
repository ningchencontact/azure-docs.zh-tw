---
title: "如何在 MySQL 的 Azure 資料庫中設定伺服器參數 | Microsoft Docs"
description: "本文說明如何使用 Azure 入口網站，在適用於 MySQL 的 Azure 資料庫中設定 MySQL 伺服器參數。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: 59eeed42356a276c259bd8da55890b7ada67d729
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>如何使用 Azure 入口網站，在適用於 MySQL 的 Azure 資料庫中設定伺服器參數

MySQL 的 Azure 資料庫支援某些伺服器參數的組態。 本文說明如何使用 Azure 入口網站來設定這些參數。 並非所有伺服器參數皆可調整。 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>瀏覽至 Azure 入口網站上的伺服器參數
1. 登入 Azure 入口網站，然後找到適用於 MySQL 的 Azure 資料庫伺服器。
2. 在 [設定] 區段下方，按一下 [伺服器參數] 以開啟適用於 MySQL 的 Azure 資料庫的 [伺服器參數] 頁面。
3. 找出您需要調整的任何設定。 檢閱 [描述] 資料行，以了解用途和允許的值。 
4. 按一下 [儲存] 以儲存變更。

![Azure 入口網站伺服器參數頁面](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>可設定的伺服器參數清單

支援的伺服器參數清單會不斷成長。 使用 Azure 入口網站中的 [伺服器參數] 索引標籤，即可根據您的應用程式需求取得定義及設定伺服器參數。 

## <a name="nonconfigurable-server-parameters"></a>無法設定的伺服器參數
InnoDB 緩衝集區和連線數目上限無法設定，且與您的[定價層](concepts-service-tiers.md)有關。 

| **定價層** | **InnoDB 緩衝集區 (MB)** | **連線數目上限** |
| :------------------------ | :-------- | :----------- |
| 基本 50 | 1024 | 50 | 
| 基本 100  | 2560 | 100 | 
| 標準 100 | 2560 | 200 | 
| 標準 200 | 5120 | 400 | 
| 標準 400 | 10240 | 800 | 
| 標準 800 | 20480 | 1600 |

這些額外的伺服器參數皆無法在系統中設定：

|**參數**|**固定值**|
| :------------------------ | :-------- |
|基本層中的 innodb_file_per_table|關|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

未列在此的其他伺服器參數會設定為其在 MySQL [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) 和 [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html) 版中的現成可用預設值。

## <a name="next-steps"></a>後續步驟
- [適用於 MySQL 的 Azure 資料庫的連線庫](concepts-connection-libraries.md)。
