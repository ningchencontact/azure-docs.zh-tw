---
title: 適用於 MariaDB 的 Azure 資料庫中支援的版本
description: 描述適用於 MariaDB 的 Azure 資料庫中支援的版本。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 69330e9d5a05fbcc892889f70a04f5eb4a4a2fb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935546"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>支援的適用於 MariaDB 的 Azure 資料庫伺服器版本
適用於 MariaDB 的 Azure 資料庫已使用 InnoDB 引擎從開放式原始碼 [MariaDB Server](https://downloads.mariadb.org/) 來開發。 適用於 MariaDB 的 Azure 資料庫目前支援下列版本：

## <a name="mariadb-version-10217"></a>MariaDB 版本 10.2.17
請參閱 [MariaDB 文件](https://downloads.mariadb.org/mariadb/10.2.17/)，以深入了解 MariaDB 10.2.17 中的改進功能與修正。

> [!NOTE]
> 在服務中，會使用閘道將連線重新導向到伺服器執行個體。 建立連線之後，MySQL 用戶端會顯示閘道中設定的 MariaDB 的版本，而非 MariaDB 伺服器執行個體上執行的實際版本。 若要判斷您的 MariaDB 伺服器執行個體的版本，請在 MySQL 提示字元命令下使用 `SELECT VERSION();` 命令。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
服務會自動管理對於次要版本更新的修補。

## <a name="next-steps"></a>後續步驟
如需以您**服務層級**為依據之特定資源配額和限制的相關資訊，請參閱[服務層級](./concepts-pricing-tiers.md)
