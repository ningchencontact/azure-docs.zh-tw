---
title: 適用於 MySQL 的 Azure 資料庫中支援的版本
description: 描述適用於 MySQL 的 Azure 資料庫中支援的版本。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 7/25/2019
ms.openlocfilehash: 3d4bab4558ebfd0f6031ef00a0b67bb0d5b61120
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501452"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>支援的適用於 MySQL 的 Azure 資料庫伺服器版本

適用於 MySQL 的 Azure 資料庫是使用 InnoDB 引擎，從 [MySQL Community Edition](https://www.mysql.com/products/community/) 開發的。

MySQL 會使用 X.Y.Z 命名配置。 X 是主要版本，Y 是次要版本，而 Z 是 Bug 修正版本。 如需此配置的詳細資訊，請參閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)。

> [!NOTE]
> 在服務中，會使用閘道將連線重新導向到伺服器執行個體。 建立連線之後，MySQL 用戶端會顯示閘道中設定的 MySQL 的版本，而非 MySQL 伺服器執行個體上執行的實際版本。 若要判斷您的 MySQL 伺服器執行個體的版本，請在 MySQL 提示字元命令下使用 `SELECT VERSION();` 命令。

適用於 MySQL 的 Azure 資料庫目前支援下列版本：

## <a name="mysql-version-56"></a>MySQL 5.6 版

Bug 修正版本：5.6.42

請參閱 MySQL[版本](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-42.html)資訊, 以深入瞭解 mysql 5.6.42 中的改進功能和修正。

## <a name="mysql-version-57"></a>MySQL 5.7 版

Bug 修正版本：5.7.24

請參閱 MySQL[版本](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-24.html)資訊, 以瞭解 mysql 5.7.24 中的改良功能和修正。

## <a name="mysql-version-80"></a>MySQL 版本8。0

> [!NOTE]
> MySQL 8.0 目前為預覽狀態。 如果您在 Azure 入口網站中看不到 MySQL 8.0, 部署可能未在您的區域中完成。 

Bug 修正版本：8.0.15

請參閱 MySQL[版本](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html)資訊, 以瞭解 mysql 8.0.15 中的改良功能和修正。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
服務會自動管理對於錯誤 (bug) 修正版本更新的修補。 例如 5.7.20 至 5.7.21。  

目前不支援主要和次要版本升級。 例如，不支援從 MySQL 5.6 升級至 MySQL 5.7。 如果您想要從 5.6 升級至 5.7，請將資料庫[備份和還原](./concepts-migrate-dump-restore.md)至使用新引擎版本所建立的伺服器。

## <a name="next-steps"></a>後續步驟

如需以您**服務層級**為依據之特定資源配額和限制的相關資訊，請參閱[服務層級](./concepts-pricing-tiers.md)
