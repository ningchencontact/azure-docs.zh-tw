---
title: 適用於 PostgreSQL 的 Azure 資料庫中的限制
description: 本文描述適用於 PostgreSQL 的 Azure 資料庫中的限制，例如連線數量和儲存引擎選項。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/22/2019
ms.openlocfilehash: 843107b8d251c2073ba9e02beacb16ab7615eca6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470727"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫中的限制
下列各節說明資料庫服務中的容量和功能限制。

## <a name="maximum-connections"></a>最大連線數
每個定價層和 vCores 的連線數目上限如下所示： 

|定價層| **vCore(s)**| **連線數目上限** |
|---|---|---|
|基本| 1| 50 |
|基本| 2| 100 |
|一般用途| 2| 150|
|一般用途| 4| 250|
|一般用途| 8| 480|
|一般用途| 16| 950|
|一般用途| 32| 1500|
|一般用途| 64| 1900|
|記憶體最佳化| 2| 300|
|記憶體最佳化| 4| 500|
|記憶體最佳化| 8| 960|
|記憶體最佳化| 16| 1900|
|記憶體最佳化| 32| 1900|

當連線超過限制時，則可能會收到下列錯誤：
> 嚴重錯誤︰很抱歉，已經有太多用戶端

Azure 系統需要五個連線，以用於監控適用於 PostgreSQL 伺服器的 Azure 資料庫。 

## <a name="functional-limitations"></a>功能限制：
### <a name="scale-operations"></a>調整作業
- 目前不支援基本定價層的雙向動態調整。
- 目前不支援減少伺服器儲存體大小。

### <a name="server-version-upgrades"></a>伺服器版本升級
- 目前不支援在主要資料庫引擎版本之間進行自動轉換。 如果您希望升級至下個主要版本，請將資料庫[備份和還原](./howto-migrate-using-dump-and-restore.md)至使用新引擎版本所建立的伺服器。

### <a name="vnet-service-endpoints"></a>VNet 服務端點
- VNet 服務端點的支援僅適用於一般用途伺服器和記憶體最佳化伺服器。

### <a name="restoring-a-server"></a>還原伺服器
- 使用 PITR 功能時，建立新伺服器的定價層會與作為新伺服器基礎的伺服器相同。
- 在還原期間建立的新伺服器不會有原始伺服器中的防火牆規則。 新伺服器的防火牆規則必須另外設定。
- 不支援還原已刪除的伺服器。

### <a name="utf-8-characters-on-windows"></a>Windows 上的 UTF-8 字元
- 在某些情況下，於 Windows 上的開放原始碼 PostgreSQL 中不完全支援 UTF-8 字元 ，這會影響適用於 PostgreSQL 的 Azure 資料庫。 如需詳細資訊，請參閱 [postgresql-archive 中的 Bug #15476](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html)。

## <a name="next-steps"></a>後續步驟
- 了解[每個定價層中可用的項目](concepts-pricing-tiers.md)
- 了解[支援的 PostgreSQL 資料庫版本](concepts-supported-versions.md)
- 檢閱[如何使用 Azure 入口網站，在適用於 PostgreSQL 的 Azure 資料庫中備份和還原伺服器](howto-restore-server-portal.md)
