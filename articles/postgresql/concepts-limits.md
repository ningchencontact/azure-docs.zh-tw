---
title: "適用於 PostgreSQL 的 Azure 資料庫中的限制"
description: "本文描述適用於 PostgreSQL 的 Azure 資料庫中的限制，例如連線數量和儲存引擎選項。"
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ba05308039e9743dd207333476e61a45c0ca166a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫中的限制
適用於 PostgreSQL 的 Azure 資料庫服務目前為公開預覽狀態。 下列各節說明資料庫服務中的容量和功能限制。

## <a name="pricing-tier-maximums"></a>定價層上限
適用於 PostgreSQL 的 Azure 資料庫具有多個您在建立伺服器時可從中選擇的定價層。 如需詳細資訊，請參閱[適用於 PostgreSQL 的 Azure 資料庫定價層](concepts-pricing-tiers.md)。  

如下所示，在每個定價層中都有連線、計算單位及儲存體的數目上限： 

|定價層| 計算世代| 虛擬核心| 連線數目上限 |
|---|---|---|---|
|基本| Gen 4| 1| 50 |
|基本| Gen 4| 2| 100 |
|基本| Gen 5| 1| 50 |
|基本| Gen 5| 2| 100 |
|一般用途| Gen 4| 2| 150|
|一般用途| Gen 4| 4| 250|
|一般用途| Gen 4| 8| 480|
|一般用途| Gen 4| 16| 950|
|一般用途| Gen 4| 32| 1500|
|一般用途| Gen 5| 2| 150|
|一般用途| Gen 5| 4| 250|
|一般用途| Gen 5| 8| 480|
|一般用途| Gen 5| 16| 950|
|一般用途| Gen 5| 32| 1500|
|記憶體最佳化| Gen 5| 2| 150|
|記憶體最佳化| Gen 5| 4| 250|
|記憶體最佳化| Gen 5| 8| 480|
|記憶體最佳化| Gen 5| 16| 950|
|記憶體最佳化| Gen 5| 32| 1900|

當連線超過限制時，則可能會收到下列錯誤：
> 嚴重錯誤︰很抱歉，已經有太多用戶端

Azure 系統需要五個連線，以用於監控適用於 PostgreSQL 伺服器的 Azure 資料庫。 

## <a name="functional-limitations"></a>功能限制：
### <a name="scale-operations"></a>調整作業
1.  目前不支援跨定價層動態調整伺服器。 亦即在「基本」、「一般用途」或「記憶體最佳化」層之間切換。
2.  目前不支援減少伺服器儲存體大小。

### <a name="server-version-upgrades"></a>伺服器版本升級
- 目前不支援在主要資料庫引擎版本之間進行自動轉換。

### <a name="subscription-management"></a>訂用帳戶管理
- 目前不支援跨訂用帳戶和資源群組動態移動伺服器。

### <a name="point-in-time-restore-pitr"></a>還原時間點 (PITR)
1.  使用 PITR 功能時，所建立新伺服器的設定會與作為新伺服器基礎的伺服器設定相同。
2.  不支援還原已刪除的伺服器。

## <a name="next-steps"></a>後續步驟
- 了解[每個定價層中可用的項目](concepts-pricing-tiers.md)
- 了解[支援的 PostgreSQL 資料庫版本](concepts-supported-versions.md)
- 檢閱[如何使用 Azure 入口網站，在適用於 PostgreSQL 的 Azure 資料庫中備份和還原伺服器](howto-restore-server-portal.md)
