---
title: 適用於 MySQL 的 Azure 資料庫中支援的版本
description: 描述適用於 MySQL 的 Azure 資料庫中支援的版本。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/22/2018
ms.openlocfilehash: cfebdbe7485f0ffaa15828803d72c2a3f97c118d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>支援的適用於 MySQL 的 Azure 資料庫伺服器版本
適用於 MySQL 的 Azure 資料庫是使用 InnoDB 引擎，從 [MySQL Community Edition](https://www.mysql.com/products/community/) 開發的。  適用於 MySQL 的 Azure 資料庫目前支援下列版本：

## <a name="mysql-version-5638"></a>MySQL 版本 5.6.38
請參閱 MySQL [文件](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-38.html)，以深入了解 MySQL 5.6.38 中的改進功能與修正。

## <a name="mysql-version-5720"></a>MySQL 版本 5.7.20
請參閱 MySQL [文件](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-20.htmll)，以了解 MySQL 5.7.20 中的改進功能與修正。

> [!NOTE]
> 在服務中，會使用閘道將連線重新導向到伺服器執行個體。 建立連線之後，MySQL 用戶端會顯示閘道中設定的 MySQL 的版本，而非 MySQL 伺服器執行個體上執行的實際版本。 若要判斷您的 MySQL 伺服器執行個體的版本，請在 MySQL 提示字元命令下使用 `SELECT VERSION();` 命令。 

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
服務會自動管理對於次要版本更新的修補。 不支援主要版本升級 (例如 從 MySQL 5.6 升級為 MySQL 5.7)。

## <a name="next-steps"></a>後續步驟

如需以您**服務層**為依據之特定資源配額和限制的相關資訊，請參閱[服務層](./concepts-pricing-tiers.md)
