---
title: 適用於 MySQL 的 Azure 資料庫中支援的版本
description: 描述適用於 MySQL 的 Azure 資料庫中支援的版本。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/07/2019
ms.openlocfilehash: ecd6466d8d7a7e4497d076ced0c9f2375d5dfb7f
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106030"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>支援的適用於 MySQL 的 Azure 資料庫伺服器版本
適用於 MySQL 的 Azure 資料庫是使用 InnoDB 引擎，從 [MySQL Community Edition](https://www.mysql.com/products/community/) 開發的。

MySQL 會使用 X.Y.Z 命名配置。 X 是主要版本，Y 是次要版本，而 Z 是 Bug 修正版本。 如需此配置的詳細資訊，請參閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)。

適用於 MySQL 的 Azure 資料庫目前支援下列版本：

## <a name="mysql-version-56"></a>MySQL 5.6 版

Bug 修正版本：5.6.39

請參閱 MySQL [版本資訊](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html)，以深入了解 MySQL 5.6.39 的改進功能與修正。

## <a name="mysql-version-57"></a>MySQL 5.7 版

Bug 修正版本：5.7.21

請參閱 MySQL [版本資訊](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html)，以了解 MySQL 5.7.21 的改進功能與修正。

> [!NOTE]
> 在服務中，會使用閘道將連線重新導向到伺服器執行個體。 建立連線之後，MySQL 用戶端會顯示閘道中設定的 MySQL 的版本，而非 MySQL 伺服器執行個體上執行的實際版本。 若要判斷您的 MySQL 伺服器執行個體的版本，請在 MySQL 提示字元命令下使用 `SELECT VERSION();` 命令。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
服務會自動管理對於錯誤 (bug) 修正版本更新的修補。 例如 5.7.20 至 5.7.21。  

目前不支援主要和次要版本升級。 例如，不支援從 MySQL 5.6 升級至 MySQL 5.7。 如果您想要從 5.6 升級至 5.7，請將資料庫[備份和還原](./concepts-migrate-dump-restore.md)至使用新引擎版本所建立的伺服器。

## <a name="next-steps"></a>後續步驟

如需以您**服務層**為依據之特定資源配額和限制的相關資訊，請參閱[服務層](./concepts-pricing-tiers.md)
