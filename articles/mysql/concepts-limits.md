---
title: "適用於 MySQL 的 Azure 資料庫中的限制 | Microsoft Docs"
description: "描述適用於 MySQL 的 Azure 資料庫中的預覽限制。"
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 12/09/2017
ms.openlocfilehash: e16982e4e57ba9f2f11e9ee59f88f24b3fe3fe3f
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>在 Azure 資料庫的 MySQL 的限制
適用於 MySQL 的 Azure 資料庫服務目前為公開預覽狀態。 下列章節說明容量、 儲存體引擎支援、 權限支援、 資料操作陳述式支援及資料庫服務中的功能限制。 另請參閱適用於 MySQL 資料庫引擎的[一般限制](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) \(英文\)。

## <a name="service-tier-maximums"></a>服務層上限
適用於 MySQL 的 Azure 資料庫具有多個可在建立伺服器時從中選擇的服務層。 如需詳細資訊，請參閱[了解每個服務層中可用的項目](concepts-service-tiers.md)。  

在預覽期間，每個服務層中具有連線、計算單位及儲存體的數目上限，如下： 

|                            |                   |
| :------------------------- | :---------------- |
| **連接數目上限**        |                   |
| 基本的 50 個計算單位     | 50 個連接    |
| 基本的 100 個計算單位    | 100 個連接   |
| 標準的 100 個計算單位 | 200 個連接   |
| 標準的 200 個計算單位 | 400 個連接   |
| 標準的 400 個計算單位 | 800 個連線   |
| 標準的 800 個計算單位 | 1600 個連線  |
| **計算單位數目上限**      |                   |
| 基本服務層         | 100 個計算單位 |
| 標準服務層      | 800 個計算單位 |
| **儲存體上限**            |                   |
| 基本服務層         | 1 TB              |
| 標準服務層      | 1 TB              |

到達太多連接時，您可能會收到下列錯誤：
> 錯誤 1040 (08004)：太多的連接

## <a name="storage-engine-support"></a>儲存體引擎支援

### <a name="supported"></a>支援
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [記憶體](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>不支援
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [封存](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [同盟](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>權限的支援

### <a name="unsupported"></a>不支援
- DBA 角色許多伺服器參數和設定不小心降低伺服器效能或變換正負號的 DBMS ACID 屬性。 因此，我們的服務整合性和產品層級的 SLA 維護我們不公開給客戶的 DBA 角色。 預設使用者帳戶，建立新的資料庫執行個體時，所建構，可讓客戶在受管理的資料庫執行個體中執行大部分的 DDL 和 DML 陳述式。 
- SUPER 權限來執行同樣的[進階權限](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)也受到限制。

## <a name="data-manipulation-statement-support"></a>資料操作陳述式支援

### <a name="supported"></a>支援
- 載入資料 INFILE-支援，但它必須指定 [本機] 參數，已導向至 UNC 路徑 （透過 XSMB 掛接的 Azure 儲存體）。

### <a name="unsupported"></a>不支援
- 選取此項目...到 OUTFILE

## <a name="preview-functional-limitations"></a>預覽功能限制

### <a name="scale-operations"></a>調整作業
- 目前不支援跨服務層動態調整伺服器。 也就是在基本和標準服務層之間切換。
- 目前不支援依需求在預先建立的伺服器上動態增加儲存體。
- 不支援減少伺服器儲存體大小。

### <a name="server-version-upgrades"></a>伺服器版本升級
- 目前不支援在主要資料庫引擎版本之間進行自動轉換。

### <a name="point-in-time-restore"></a>還原時間點
- 不允許還原到不同服務層和/或計算單位與儲存體大小。
- 不支援還原已刪除的伺服器。

## <a name="functional-limitations"></a>功能限制：

### <a name="subscription-management"></a>訂用帳戶管理
- 目前不支援跨訂用帳戶和資源群組動態移動預先建立的伺服器。

## <a name="current-known-issues"></a>目前已知的問題：
- 在連線建立之後，MySQL 伺服器執行個體就會顯示錯誤的伺服器版本。 若要取得正確的伺服器執行個體的版本控制，請使用 選取 version();在 MySQL 提示字元命令。

## <a name="next-steps"></a>後續步驟
- [每個服務層中可用的項目](concepts-service-tiers.md)
- [支援的 MySQL 資料庫版本](concepts-supported-versions.md)
