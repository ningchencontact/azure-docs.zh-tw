---
title: 適用於 MariaDB 的 Azure 資料庫中支援的版本
description: 描述適用於 MariaDB 的 Azure 資料庫中支援的版本。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 760cb39ea1f3980faba348c7aa1de68a66b20a8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065713"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>支援的適用於 MariaDB 的 Azure 資料庫伺服器版本

適用於 MariaDB 的 Azure 資料庫已使用 InnoDB 引擎從開放式原始碼 [MariaDB Server](https://downloads.mariadb.org/) 來開發。 適用於 MariaDB 的 Azure 資料庫目前支援下列版本：

## <a name="mariadb-version-102"></a>10\.2 的 MariaDB 版本

請參閱[MariaDB 文件](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/)若要深入了解改進和修正 MariaDB 10.2.23。

## <a name="mariadb-version-103"></a>MariaDB 版本 10.3

請參閱[MariaDB 文件](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/)若要深入了解改進和修正 MariaDB 10.3.14。

> [!NOTE]
> 在服務中，會使用閘道將連線重新導向到伺服器執行個體。 建立連線之後，MySQL 用戶端會顯示閘道中設定的 MariaDB 的版本，而非 MariaDB 伺服器執行個體上執行的實際版本。 若要判斷您的 MariaDB 伺服器執行個體的版本，請在 MySQL 提示字元命令下使用 `SELECT VERSION();` 命令。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級

服務會自動管理對於次要版本更新的修補。

## <a name="next-steps"></a>後續步驟

- 如需有關以您**服務層級**為依據之特定資源配額和限制的資訊，請參閱[服務層級](./concepts-pricing-tiers.md)。
